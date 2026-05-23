# Agent Skills

Skills 是可移植的指令包，用于扩展 Kiro 知道如何做的事情。每个技能包含特定工作流程的指令，无论是审查拉取请求、部署基础设施，还是遵循团队的编码标准。

Skills 遵循开放的 [Agent Skills](https://agentskills.io) 标准，使其可以在工具和团队之间共享。

## Skills 如何工作

当您开始聊天会话时，Kiro 通过读取名称和描述来发现可用的技能。技能可以通过两种方式激活：
- **自动**：Kiro 将您的请求与技能描述匹配，并加载相关技能。
- **作为斜杠命令**：输入 `/` 后跟技能名称直接调用。例如，名为 `pr-review` 的技能变成 `/pr-review` 斜杠命令。

```
> Review this PR for security issues

I'll review the PR using the security checklist...
```

```
> /pr-review

I'll review the PR using the security checklist...
```

### 向技能传递参数

如果技能主体包含 `$ARGUMENTS` 或 `${N}` 占位符，斜杠命令后的文本会被替换到其中。如果技能没有占位符，任何尾随文本仍会作为额外上下文传递给代理：

```
> /pr-review focus on the authentication changes

I'll review the PR using the security checklist, with extra attention to the authentication changes.
```

有关详细信息，请参阅[基于技能的斜杠命令](/docs/cli/reference/slash-commands/#skill-based-slash-commands)。

要查看当前会话中可用的技能，使用 `/context show` 命令或直接询问 Kiro：

```bash
> /context show
```

## 技能位置

技能可以存储在两个地方：

| 位置 | 作用域 | 用例 |
|------|--------|------|
| `.kiro/skills/` | 工作区 | 项目特定工作流程、团队约定 |
| `~/.kiro/skills/` | 全局 | 跨所有项目的个人工作流程 |

当技能同名时，工作区技能优先于全局技能。

### 默认代理

默认代理自动从两个位置加载技能。无需配置。

### 自定义代理

自定义代理默认不加载技能。您需要将它们显式添加到代理的 `resources` 字段：

```json
{
  "name": "my-agent",
  "resources": [
    "skill://.kiro/skills/*/SKILL.md",
    "skill://~/.kiro/skills/*/SKILL.md"
  ]
}
```

`skill://` URI 方案支持特定路径、glob 模式和主目录扩展。

## 创建技能

技能是一个包含 `SKILL.md` 文件的文件夹：

```
pr-review/
├── SKILL.md           # 必需
└── references/        # 可选
    └── checklist.md
```

### SKILL.md 格式

文件以 YAML frontmatter 开头，后跟 markdown 指令：

```markdown
---
name: pr-review
description: Review pull requests for code quality, security issues, and test coverage. Use when reviewing PRs or preparing code for review.
---

## Review checklist

When reviewing a pull request:

1. Check for vulnerabilities, injection risks, exposed secrets
2. Verify edge cases and failure modes are handled
3. Confirm new code has appropriate tests
4. Ensure variables and functions have clear names

## Common issues to flag

- Hardcoded credentials or API keys
- Missing input validation
- Unhandled promise rejections
- Console.log statements left in production code
```

### Frontmatter 字段

| 字段 | 必需 | 描述 |
|------|------|------|
| `name` | 是 | 技能标识符。仅限小写字母、数字和连字符。最多 64 个字符。 |
| `description` | 是 | 何时激活此技能。Kiro 将此与您的请求匹配。最多 1024 个字符。 |

`description` 字段决定 Kiro 何时激活技能。包含与您表述请求方式匹配的特定关键词和操作。

### 引用文件

对于大量文档，使用 `references/` 文件夹：

```
aws-deployment/
├── SKILL.md
└── references/
    ├── ecs-guide.md
    └── troubleshooting.md
```

在 SKILL.md 中引用这些文件：

```markdown
For ECS deployments, follow the guide in `references/ecs-guide.md`.
```

Kiro 仅在指令指示时加载引用文件。

## 示例

CDK 部署技能：

```
cdk-deploy/
├── SKILL.md
└── references/
    └── stack-patterns.md
```

**SKILL.md:**

```markdown
---
name: cdk-deploy
description: Deploy AWS CDK stacks with best practices. Use when deploying infrastructure, running cdk deploy, or troubleshooting CDK issues.
---

## Deployment workflow

1. Run `cdk synth` to validate templates before deploying
2. Use `cdk diff` to preview what will change
3. Run `cdk deploy` and review IAM changes

## Pre-deployment checks

- Verify AWS credentials are configured for the target account
- Check that the CDK version matches the project's requirements
- Review `references/stack-patterns.md` for environment-specific patterns

## Rollback procedure

If deployment fails:
1. Check CloudFormation console for the specific error
2. Run `cdk destroy` only if the stack is in a failed state
3. Fix the issue and redeploy
```

用法：

```bash
> Deploy my CDK stack to staging

I'll follow the deployment workflow. First, let me synthesize the templates...
```

## 最佳实践

**编写精确的描述。** 描述决定 Kiro 何时激活技能：
- 好：`Review pull requests for security vulnerabilities and test coverage. Use when reviewing PRs or preparing code for review.`
- 模糊：`Helps with code review`

**保持 SKILL.md 可操作。** 将详细的参考材料放在 `references/` 文件中。

**选择合适的作用域。** 对到处使用的个人工作流程使用全局技能。对团队流程和项目特定约定使用工作区技能。

**版本控制工作区技能。** 将 `.kiro/skills/` 提交到您的仓库，以便团队共享相同的工作流程。

## 故障排除

| 问题 | 解决方案 |
|------|----------|
| 技能未激活 | 使描述更具体，包含与请求匹配的关键词 |
| 斜杠命令未找到 | 验证技能文件夹名称与您输入的内容匹配。技能必须有有效的带 frontmatter 的 SKILL.md。检查 `/context show` 确认技能已加载 |
| 技能未找到 | 验证 SKILL.md 存在且具有有效的 frontmatter |
| 自定义代理缺少技能 | 将 `skill://` URI 添加到代理的 `resources` 字段 |
| 错误的技能激活 | 用更具体的关键词区分描述 |

## 相关

- [斜杠命令](/docs/cli/reference/slash-commands/)：基于技能的斜杠命令和所有其他斜杠命令
- [Steering](/docs/cli/steering/)：项目特定的上下文和约定
- [自定义代理](/docs/cli/custom-agents/)：代理配置和资源
- [Agent Skills 规范](https://agentskills.io/specification)：完整格式详情

---

页面更新时间: 2026年5月12日