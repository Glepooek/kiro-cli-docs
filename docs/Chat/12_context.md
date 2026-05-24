# 上下文管理

## 选择正确的上下文方法

Kiro 提供三种提供上下文的方式，每种针对不同的用例优化:

| 方法 | 上下文窗口影响 | 持久性 | 最适合 |
|------|---------------|--------|--------|
| Agent资源 | 始终活跃（消耗 token） | 跨会话持久 | 基本项目文件、标准、配置 |
| 技能 | 按需 | 跨会话持久 | 大型指南、参考文档、专业知识 |
| 会话上下文 | 始终活跃（消耗 token） | 仅当前会话 | 临时文件、快速实验 |
| 知识库 | 仅在搜索时 | 跨会话持久 | 大型代码库、大量文档 |

## 决策流程图

1. 使用此决策树选择适当的上下文方法:
   - 您的内容是否大于 10MB 或包含数千个文件？
     - 是 → 使用知识库
     - 否 → 继续步骤 2
2. 您是否需要在每次对话中使用此上下文？
   - 是 → 使用Agent资源
   - 否 → 使用会话上下文

### 快速参考

- 基本项目文件（README、配置、标准）→ Agent资源
- 大型代码库或文档集 → 知识库
- 当前任务的临时文件 → 会话上下文

## 理解上下文窗口影响

Kiro 在每次会话中自动将您的工作目录和操作系统作为上下文包含。您不需要配置这个。
- 上下文文件和Agent资源在每次请求时都会消耗上下文窗口的 token，无论是否被引用。

```bash
> /context show

Agent
  - .kiro/steering/**/*.md  <project-root>/.kiro/steering/product.md
<project-root>/.kiro/steering/structure.md
<project-root>/.kiro/steering/tech.md
<project-root>/.kiro/steering/testing.md
  - README.md <project-root>/snake/README.md
  - ~/.kiro/steering/**/*.md (no matches)

Session (temporary)
  <none>

5 matched files in use
- <project-root>/.kiro/steering/testing.md (0.1% of context window)
- <project-root>/snake/.kiro/steering/tech.md (0.1% of context window)
- <project-root>/snake/README.md (0.1% of context window)
- <project-root>/snake/.kiro/steering/structure.md (0.2% of context window)
- <project-root>/snake/.kiro/steering/product.md (0.1% of context window)

Context files total: 0.5% of context window
```

输出显示:
- **Agent**: 来自agent resources 字段的持久上下文
- **Session**: 当前会话中添加的临时上下文

- 上下文文件限制为模型上下文窗口的 75%。超过此限制的文件会自动丢弃。
- 知识库在搜索前不消耗上下文窗口空间，使其成为大型参考材料的理想选择。更多信息请参见知识库上下文（用于大型数据集）。

## 管理上下文

上下文文件包含您希望 Kiro 在对话中考虑的信息。这些可以包括项目需求、编码标准、开发规则或任何帮助 Kiro 提供更相关响应的信息。

### 通过Agent资源配置持久上下文

配置上下文的推荐方法是通过agent配置文件中的 resources 字段。这会创建每次使用代理时可用的持久上下文。

在agent配置的 resources 数组中添加文件路径或 glob 模式:

```json
{
  "name": "my-agent",
  "description": "我的开发代理",
  "resources": [
    "file://README.md",
    "file://docs/**/*.md",
    "file://src/config.py"
  ]
}
```

资源使用 URI 方案指定类型:
- `file://` — 启动时直接加载到上下文中的文件
- `skill://` — 启动时加载元数据、按需加载完整内容的技能
- `knowledgeBase` — 按需搜索的索引内容（配置为对象，而非 URI 字符串）

这些文件将在使用此代理的所有聊天会话中自动可用。有关资源类型的更多详情，请参见[代理配置参考](/docs/cli/custom-agents/configuration-reference#resources-field)。

### 添加临时会话上下文

您可以使用 /context add 命令临时将文件添加到当前聊天会话。这些添加仅在当前会话中可用，在您开始新的聊天会话时不会持久。

```bash
> /context add README.md
Added 1 path(s) to context.
```

> **注意**: 通过斜杠命令进行的上下文修改是临时的。

您还可以使用 glob 模式一次添加多个文件:

```bash
> /context add docs/*.md
Added 3 path(s) to context.
```

要使上下文更改永久生效，请改为将文件添加到agent resources 字段。更多信息请参见通过Agent资源配置持久上下文。

### 知识库上下文（用于大型数据集）

对于会超出上下文窗口限制的大型代码库、文档集或参考材料，请使用知识库。知识库提供语义搜索能力，在搜索前不消耗上下文窗口空间。

启用知识库:

```
kiro-cli settings chat.enableKnowledge true
```

添加内容到知识库:

```
kiro-cli chat

> /knowledge add /path/to/large-codebase --include "**/*.py" --exclude "node_modules/**"
```

知识库在需要相关信息时由 Kiro 按需搜索，使其成为大型参考材料的理想选择。

### 对话压缩

压缩总结旧消息同时保留近期消息，释放上下文窗口空间。
- **手动**: 运行 [`/compact`](/docs/cli/reference/slash-commands#compact)
- **自动**: 上下文窗口溢出时触发

#### 配置

| 设置 | 默认值 | 描述 |
|------|--------|------|
| `compaction.excludeMessages` | 2 | 保留的最小消息对数 |
| `compaction.excludeContextWindowPercent` | 2 | 保留的最小上下文窗口百分比 |

两个设置都会被评估，更保守（更大）的值优先。

压缩会创建一个新会话。您可以通过 `/chat resume` 恢复原始会话。

### 查看上下文使用情况

要查看当前上下文，使用 /context show 命令:

```bash
> /context show

Current context window (5.9% used)
|||████████████████████████████████████████████████████████████████ 5.9%

█ Context files 0.9%
█ Tools 0.5%
█ Kiro responses 0.7%
█ Your prompts 3.8%
```

输出显示上下文条目的 glob 模式，便于识别和移除它们。

### 移除上下文

要从当前会话上下文中移除文件:

```bash
> /context remove src/temp-file.py
Removed 1 path(s) from context.
```

要清除所有会话上下文，使用 /context clear 命令:

```bash
> /context clear
Cleared context
Note: Context modifications via slash command is temporary.
```

注意: 您不能使用 /context 命令移除agent定义的上下文。要永久移除上下文，请编辑agent resources 字段。

## 上下文管理操作

以下是主动管理上下文窗口的一些常见原因:

1. 如果您发现自己在每次会话中使用 `/context add` 命令重复添加相同的上下文文件，请考虑将它们移到agent resources 字段以持久化:

```bash
# 与其每次会话运行这些命令：
> /context add README.md
> /context add docs/*.md

# 一次性将它们添加到代理配置：
{
  "resources": [
    "file://README.md",
    "file://docs/**/*.md"
  ]
}
```

您可以配置包含首选上下文文件的默认代理。这确保您的新聊天会话自动可用上下文，而无需每次指定代理。

```bash
   >kiro-cli settings chat.defaultAgent my-project-agent
```

2. 如果您有大型上下文文件（消耗大量上下文窗口），请考虑使用知识库而不是直接包含这些文件。考虑使用知识的情况:
   - 您有大型代码库或文档集
   - 您需要在大量材料中进行语义搜索
   - 您想避免持续的上下文窗口消耗
   - 示例: 与其将大型代码库作为上下文文件添加:

```bash
# 这会消耗太多 token：
> /context add src/**/*.py

# 改用知识库：
> /knowledge add src/ --include "**/*.py" --exclude "__pycache__/**"
```

## 最佳实践

### 上下文文件组织

1. 保持上下文文件专注于相关内容，避免达到 token 限制
2. 使用描述性文件名表明其用途
3. 在逻辑目录结构中组织规则和文档
4. 考虑文件大小 - 非常大的文件可能消耗大量 token

### 性能考虑

1. 使用 /context show 监控 token 使用量以保持在限制内
2. 使用特定的 glob 模式而非过于宽泛的模式
3. 从代理配置中移除未使用的上下文文件
4. 考虑将大型上下文文件拆分为更小、更专注的文件
5. 对大型数据集使用知识库以避免上下文窗口消耗

### 安全考虑

1. 避免在上下文文件中包含敏感信息
2. 使用 `.gitignore` 防止意外提交敏感上下文
3. 定期审查上下文文件以确保它们不包含过时信息
4. 在对话中使用上下文时要注意共享了什么信息

## 相关文档

- [斜杠命令](/docs/cli/reference/slash-commands) - 聊天内上下文命令
- [CLI 命令](/docs/cli/reference/cli-commands) - 终端上下文命令
- [交互式聊天模式](/docs/cli/chat/interactive-mode) - 在聊天中使用上下文

---

页面更新时间: 2026年4月24日
