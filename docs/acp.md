# Agent Client Protocol (ACP)

Kiro CLI 实现了 Agent Client Protocol (ACP),这是一个开放标准,使 AI Agent 能够与任何兼容的编辑器一起工作。这意味着您可以在 JetBrains IDE、Zed 和其他 ACP 兼容的编辑器中使用 Kiro 的 Agent 能力。

## 什么是 ACP?

AI 编码 Agent 和编辑器紧密耦合,但互操作性不是默认的。每个编辑器必须为每个 Agent 构建自定义集成,Agent 必须实现特定于编辑器的 API。这会导致集成开销、有限的兼容性和开发者锁定。

ACP 通过提供 Agent-编辑器通信的标准化协议来解决这个问题 - 类似于 Language Server Protocol (LSP) 如何标准化语言服务器集成。实现 ACP 的 Agent 可以与任何兼容的编辑器一起工作,支持 ACP 的编辑器可以访问所有 ACP 兼容的 Agent。

## 快速开始

将 Kiro 作为 ACP Agent 运行:

```bash
kiro-cli acp
```

要使用特定的 Agent 配置:

```bash
kiro-cli acp --agent my-agent
```

Agent 通过 stdin/stdout 使用 JSON-RPC 2.0 进行通信。配置您的编辑器生成此命令,您就可以开始了。

## 编辑器设置

Kiro CLI 可以在任何支持该协议的编辑器中用作 ACP Agent。

> 使用 `kiro-cli` 的完整路径在您的编辑器配置中。IDE 通常不会继承您 shell 的 PATH,因此像 `kiro-cli` 这样的命令可能无法找到。运行 `which kiro-cli` 查找系统上的路径(在 Linux/macOS 上通常为 `~/.local/bin/kiro-cli`)。

### JetBrains IDE

JetBrains IDE(IntelliJ IDEA、WebStorm、PyCharm 等)通过 AI Assistant 支持 ACP。

要将 Kiro 添加为自定义 Agent:
1. 打开 AI Chat 工具窗口
2. 点击设置按钮并选择 **Add Custom Agent**
3. 将以下内容添加到 `~/.jetbrains/acp.json`:

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

Agent 将出现在 AI Chat 模式选择器中。

### Zed

Zed 原生支持 ACP Agent。将以下内容添加到您的 Zed 设置(`~/.config/zed/settings.json`):

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

从 Zed 的 AI 面板中的 Agent 选择器中选择 "Kiro Agent"。

### 其他编辑器

任何支持 ACP 的编辑器都可以通过生成 `kiro-cli acp` 并通过 stdio 进行 JSON-RPC 通信来集成 Kiro。

## 支持的 ACP 方法

Kiro CLI 实现以下 ACP 方法,让您在使用任何 ACP 兼容编辑器时可以访问会话管理、模型选择和流式响应。

### 核心协议

| 方法 | 描述 |
|-------------------|---------------------------------------------------|
| `initialize` | 初始化连接并交换能力 |
| `session/new` | 创建新的聊天会话 |
| `session/load` | 按 ID 加载现有会话 |
| `session/prompt` | 向 Agent 发送提示 |
| `session/cancel` | 取消当前操作 |
| `session/set_mode` | 切换 Agent 模式(例如不同的 Agent 配置) |
| `session/set_model` | 更改会话的模型 |

### Agent 能力

Kiro ACP Agent 在初始化期间公布这些能力:
- `loadSession: true` - 支持加载现有会话
- `promptCapabilities.image: true` - 支持提示中的图像内容
