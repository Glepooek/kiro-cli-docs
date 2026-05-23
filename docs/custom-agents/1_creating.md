# 创建自定义代理

自定义代理允许您通过定义可用的工具、授予的权限和自动包含的上下文，为特定任务定制 Kiro CLI 行为。

## 快速开始

您可以在 Kiro CLI 聊天会话中使用 `/agent create` 斜杠命令创建代理。默认情况下，它使用 AI 辅助模式根据您的描述生成完整的代理配置：

```text
> /agent create

✔ 输入代理名称:  · backend-specialist
✔ 输入代理描述:  · 您是后端编码实践专家
✔ 代理范围 · 本地（当前工作区）
选择 MCP 服务器（使用空格切换，回车确认）: markdown-downloader (node), code-analysis (uv)

✓ 代理 'backend-specialist' 已成功创建并保存！
```

您也可以在行内提供名称和选项：

```text
> /agent create backend-specialist -D "后端编码专家" -m code-analysis
```

> **信息**: `/agent generate` 是 `/agent create` 的别名。两个命令的行为相同。

或者，直接从终端使用 CLI 命令：

```bash
kiro-cli agent create backend-specialist
```

## 选项

`/agent create` 斜杠命令和 `kiro-cli agent create` CLI 命令共享一些标志，而其他标志则是斜杠命令独有的：

| 标志 | 描述 | 可用性 |
|------|------|--------|
| `--directory` | 保存代理的位置（参见目录值） | 两者 |
| `--from` | 基于新代理的模板代理（隐含 `--manual`） | 两者 |
| `--description` | 代理的描述 | 斜杠命令 |
| `--mcp-server` | 要包含的 MCP 服务器（可重复） | 斜杠命令 |
| `--manual` | 使用基于编辑器的创建而不是 AI 生成 | 斜杠命令 |

> **警告**: `--description` 和 `--mcp-server` 标志仅在 AI 辅助模式下可用。它们不能与 `--manual` 或 `--from` 组合使用。

## 目录值

`--directory` 标志除了自定义路径外还接受两个特殊值：

| 值 | 描述 |
|----|------|
| `workspace` | 在当前工作目录的 `.kiro/agents/` 中创建代理 |
| `global` | 在 `~/.kiro/agents/` 中创建代理（默认） |
| `./path` 或 `/path` | 在指定的自定义路径中创建代理 |

当未指定 `--directory` 时，代理保存到全局目录（`~/.kiro/agents/`）。

## 手动创建模式

如果您更喜欢在编辑器中自己定义代理配置而不是使用 AI 生成，请传递 `--manual` 标志：

```text
> /agent create my-agent --manual
```

这将使用基本代理配置文件打开您的默认编辑器。您也可以使用 `--from` 基于现有代理创建新代理：

```text
> /agent create my-agent --from backend-specialist
```

## 代理配置文件

自定义代理使用 JSON 配置文件定义。这是一个基本示例：

```json
{
  "name": "my-agent",
  "description": "用于我的工作流程的自定义代理",
  "tools": ["read", "write"],
  "allowedTools": ["read"],
  "resources": [
    "file://README.md",
    "file://.kiro/steering/**/*.md",
    "skill://.kiro/skills/**/SKILL.md"
  ],
  "prompt": "您是一个有帮助的编码助手",
  "model": "claude-sonnet-4"
}
```

## 使用您的自定义代理

启动新的聊天会话 - 它使用默认代理（"kiro_default"）并使用代理斜杠命令切换到代理：

```bash
> /agent swap

 选择以下代理之一
❯ rust-developer-agent
  kiro_default
  backend-specialist
```

选择代理后，您将看到以下内容：

```bash
✔ 选择以下代理之一 · backend-specialist

[backend-specialist] > 
```

或者，使用您的自定义代理启动聊天会话：

```bash
kiro-cli --agent my-agent
```

## 下一步

- 详细探索[代理配置](2_configuration-reference.md)选项
