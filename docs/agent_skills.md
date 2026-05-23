# Agent Skills

Skills 是可移植的指令包,扩展了 Kiro 的能力。每个 Skill 包含特定工作流的指令,无论是审查拉取请求、部署基础设施,还是遵循团队的编码标准。

Skills 遵循开放的 Agent Skills 标准,使其可以在工具和团队之间共享。

## Skills 如何工作

当您开始聊天会话时,Kiro 通过读取名称和描述来发现可用的 Skills。Skills 可以通过两种方式激活:
- **自动**: Kiro 将您的请求与 Skill 描述匹配,并加载相关的 Skill。
- **作为斜杠命令**: 输入 `/` 后跟 Skill 名称以直接调用它。例如,名为 `pr-review` 的 Skill 成为 `/pr-review` 斜杠命令。

```text
> 审查此 PR 的安全问题

我将使用安全检查清单审查 PR...
```

```text
> /pr-review

我将使用安全检查清单审查 PR...
```

### 向 Skill 传递参数

如果 Skill 主体包含 `$ARGUMENTS` 或 `${N}` 占位符,斜杠命令后的文本将被替换到其中。如果 Skill 没有占位符,任何尾随文本仍会作为额外上下文传递给 Agent。

要查看当前会话中可用的 Skills,请使用 `/context show` 命令或直接询问 Kiro:

```bash
> /context show
```

## Skill 位置

Skills 可以存储在两个地方:

| 位置 | 作用域 | 用例 |
|------------------|---------|-------------------------------------------|
| `.kiro/skills/` | 工作区 | 项目特定的工作流、团队约定 |
| `~/.kiro/skills/` | 全局 | 跨所有项目的个人工作流 |

当 Skills 共享相同名称时,工作区 Skills 优先于全局 Skills。

### 默认 Agent

默认 Agent 自动从两个位置加载 Skills。无需配置。

### 自定义 Agent

自定义 Agent 默认不加载 Skills。您需要将它们显式添加到 Agent 的 `resources` 字段:

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

## 创建 Skill

Skill 是一个包含 `SKILL.md` 文件的文件夹:

```text
pr-review/
├── SKILL.md           # 必需
└── references/        # 可选
    └── checklist.md
```

### SKILL.md 格式

文件以 YAML frontmatter 开头,后跟 markdown 指令:

```markdown
---
name: pr-review
description: 审查拉取请求的代码质量、安全问题和测试覆盖率。在审查 PR 或准备代码审查时使用。
---

## 审查检查清单

审查拉取请求时:

1. 检查漏洞、注入风险、暴露的密钥
2. 验证边缘情况和故障模式已处理
3. 确认新代码有适当的测试
4. 确保变量和函数有清晰的名称
```

### Frontmatter 字段

| 字段 | 必需 | 描述 |
|-------------|----------|----------------------------------------------------------------------|
| `name` | 是 | Skill 标识符。仅限小写字母、数字和连字符。最多 64 个字符。 |
| `description` | 是 | 何时激活此 Skill。Kiro 将此与您的请求匹配。最多 1024 个字符。 |

`description` 字段决定 Kiro 何时激活 Skill。包含与您表述请求方式匹配的特定关键字和操作。
