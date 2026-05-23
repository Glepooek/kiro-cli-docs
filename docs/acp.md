# Agent Client Protocol (ACP)

Kiro CLI 实现了 [Agent Client Protocol (ACP)](https://agentclientprotocol.com/get-started/introduction)，这是一个开放标准，使 AI 代理能够与任何兼容的编辑器配合工作。这意味着您可以在 JetBrains IDE、Zed 和其他 ACP 兼容的编辑器中使用 Kiro 的代理功能。

## 什么是 ACP？

AI 编码代理和编辑器紧密耦合，但互操作性并非默认设置。每个编辑器必须为每个代理构建自定义集成，代理必须实现特定于编辑器的 API。这导致了集成开销、兼容性有限和开发者锁定。

ACP 通过为代理-编辑器通信提供标准化协议来解决此问题——类似于语言服务器协议 (LSP) 标准化语言服务器集成的方式。实现 ACP 的代理可与任何兼容编辑器配合工作，支持 ACP 的编辑器可以访问所有 ACP 兼容的代理。

## 快速开始

将 Kiro 作为 ACP 代理运行：

```bash
kiro-cli acp
```

使用特定代理配置：

```bash
kiro-cli acp --agent my-agent
```

代理通过 stdin/stdout 使用 JSON-RPC 2.0 进行通信。配置您的编辑器生成此命令，即可开始使用。

## 编辑器设置

Kiro CLI 可以作为 ACP 代理在任何支持该协议的编辑器中使用。

> **提示**
>
> 在编辑器配置中使用 `kiro-cli` 的完整路径。IDE 通常不会继承您 shell 的 PATH，因此像 `kiro-cli` 这样的命令可能找不到。运行 `which kiro-cli` 查找系统上的路径（Linux/macOS 上通常为 `~/.local/bin/kiro-cli`）。

### JetBrains IDE

JetBrains IDE（IntelliJ IDEA、WebStorm、PyCharm 等）通过 AI Assistant 支持 ACP。有关完整详细信息，请参阅 [JetBrains ACP 文档](https://www.jetbrains.com/help/ai-assistant/acp.html)。

将 Kiro 添加为自定义代理：
1. 打开 AI Chat 工具窗口
2. 点击设置按钮并选择 **Add Custom Agent**
3. 将以下内容添加到 `~/.jetbrains/acp.json`：

```json
{
  "agent_servers": {
    "Kiro Agent": {
      "command": "/full/path/to/kiro-cli",
      "args": ["acp"]
    }
  }
}
```

代理将出现在 AI Chat 模式选择器中。

### Zed

Zed 原生支持 ACP 代理。有关完整详细信息，请参阅 [Zed 外部代理文档](https://zed.dev/docs/ai/external-agents#custom-agents)。将以下内容添加到您的 Zed 设置（`~/.config/zed/settings.json`）：

```json
{
  "agent_servers": {
    "Kiro Agent": {
      "type": "custom",
      "command": "~/.local/bin/kiro-cli",
      "args": ["acp"],
      "env": {}
    }
  }
}
```

在 Zed 的 AI 面板中的代理选择器中选择 "Kiro Agent"。

### 其他编辑器

任何支持 ACP 的编辑器都可以通过生成 `kiro-cli acp` 并通过 stdio 上的 JSON-RPC 进行通信来集成 Kiro。有关协议详细信息，请参阅 [ACP 规范](https://agentclientprotocol.com)。

## 支持的 ACP 方法

Kiro CLI 实现以下 ACP 方法，让您在任何 ACP 兼容编辑器中使用 Kiro 时可以访问会话管理、模型选择和流式响应。

### 核心协议

| 方法 | 描述 |
|------|------|
| `initialize` | 初始化连接并交换能力 |
| `session/new` | 创建新的聊天会话 |
| `session/load` | 按 ID 加载现有会话 |
| `session/prompt` | 向代理发送提示 |
| `session/cancel` | 取消当前操作 |
| `session/set_mode` | 切换代理模式（例如，不同的代理配置） |
| `session/set_model` | 更改会话的模型 |

### 代理能力

Kiro ACP 代理在初始化期间声明以下能力：
- `loadSession: true` - 支持加载现有会话
- `promptCapabilities.image: true` - 支持提示中的图像内容

### 会话更新

代理通过 `session/notification` 发送以下会话更新类型：

| 更新类型 | 描述 |
|----------|------|
| `AgentMessageChunk` | 来自代理的流式文本/内容 |
| `ToolCall` | 工具调用，包含名称、参数、状态 |
| `ToolCallUpdate` | 运行中工具的进度更新 |
| `TurnEnd` | 表示代理轮次已完成 |

## Kiro 扩展

Kiro 使用自定义方法扩展 ACP（按照 ACP 规范以 `_kiro.dev/` 为前缀），以暴露 Kiro 特定功能，如[斜杠命令](/docs/cli/reference/slash-commands)、[MCP 服务器](/docs/cli/mcp)和[上下文压缩](/docs/cli/chat#context-management)。不支持这些扩展的客户端可以安全地忽略它们——它们是可选的增强功能。

> **警告**
>
> 这些扩展是实验性的，可能会在未来版本中更改。

### 斜杠命令

| 方法 | 类型 | 描述 |
|------|------|------|
| `_kiro.dev/commands/execute` | 请求 | 执行斜杠命令（例如 `/agent swap`、`/context add`） |
| `_kiro.dev/commands/options` | 请求 | 获取部分命令的自动补全建议 |
| `_kiro.dev/commands/available` | 通知 | 会话创建后发送可用命令列表 |

### MCP 服务器事件

| 方法 | 类型 | 描述 |
|------|------|------|
| `_kiro.dev/mcp/oauth_request` | 通知 | 当 MCP 服务器需要身份验证时提供 OAuth URL |
| `_kiro.dev/mcp/server_initialized` | 通知 | 表示 MCP 服务器已完成初始化，其工具可用 |

### 会话管理

| 方法 | 类型 | 描述 |
|------|------|------|
| `_kiro.dev/compaction/status` | 通知 | 压缩对话上下文时报告进度 |
| `_kiro.dev/clear/status` | 通知 | 清除会话历史时报告状态 |
| `_session/terminate` | 通知 | 终止子代理会话 |

## 示例：初始化连接

以下是 ACP 客户端如何与 Kiro 初始化连接：

```json
// 客户端发送 initialize 请求
{
  "jsonrpc": "2.0",
  "id": 0,
  "method": "initialize",
  "params": {
    "protocolVersion": 1,
    "clientCapabilities": {
      "fs": {
        "readTextFile": true,
        "writeTextFile": true
      },
      "terminal": true
    },
    "clientInfo": {
      "name": "my-editor",
      "version": "1.0.0"
    }
  }
}

// Kiro 响应能力
{
  "jsonrpc": "2.0",
  "id": 0,
  "result": {
    "protocolVersion": 1,
    "agentCapabilities": {
      "loadSession": true,
      "promptCapabilities": {
        "image": true
      }
    },
    "agentInfo": {
      "name": "kiro-cli",
      "version": "1.5.0"
    }
  }
}
```

初始化后，创建会话并开始提示：

```json
// 创建新会话
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "session/new",
  "params": {
    "cwd": "/home/user/my-project",
    "mcpServers": []
  }
}

// 发送提示
{
  "jsonrpc": "2.0",
  "id": 2,
  "method": "session/prompt",
  "params": {
    "sessionId": "sess_abc123",
    "content": [
      {
        "type": "text",
        "text": "Explain this codebase"
      }
    ]
  }
}
```

## 会话存储

ACP 会话持久化到磁盘：

```
~/.kiro/sessions/cli/
```

每个会话创建两个文件：
- `<session-id>.json` - 会话元数据和状态
- `<session-id>.jsonl` - 事件日志（对话历史）

## 日志

ACP 代理日志写入标准 Kiro 日志位置：

| 平台 | 位置 |
|------|------|
| macOS | `$TMPDIR/kiro-log/kiro-chat.log` |
| Linux | `$XDG_RUNTIME_DIR/kiro-log/kiro-chat.log` |

使用环境变量控制日志详细程度：

```bash
KIRO_LOG_LEVEL=debug kiro-cli acp
KIRO_CHAT_LOG_FILE=/path/to/custom.log kiro-cli acp
```

## 相关

- [交互式聊天](/docs/cli/chat) - 内部使用 ACP
- [MCP 集成](/docs/cli/mcp) - MCP 服务器可以传递给 ACP 会话
- [CLI 命令参考](/docs/cli/reference/cli-commands)

---

页面更新时间: 2026年2月16日