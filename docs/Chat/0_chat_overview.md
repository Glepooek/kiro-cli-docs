# Chat

Kiro CLI 提供交互式聊天模式，让您可以直接在终端中与 AI 进行自然对话。默认体验使用丰富的终端 UI，具有语法高亮代码、交互面板和可视化工具进度。

## 启动会话

要启动聊天会话：

```bash
kiro-cli
```

或使用特定上下文启动聊天：

```bash
kiro-cli --agent myagent
```

## 输入多行语句

使用以下任一方法在提示中插入换行符：

* **Shift+Enter** — 适用于 iTerm2、Ghostty、Kitty、Warp 和 Zed
* **Ctrl+J** — 适用于所有终端，包括 tmux
* **Alt+Enter** — 适用于 Terminal.app 和 Ghostty
* **`/editor`** — 打开默认编辑器（默认为 vi）以编写更长的提示

如果 `Shift+Enter` 在您的终端中不起作用，运行 `/settings terminal` 进行自动配置。有关详细信息，请参阅[会话内设置](./settings.md)。

您还可以使用 [`/reply`](./responding.md) 命令打开编辑器，并引用最近的助手消息进行回复。

## 内联运行 Shell 命令

通过前缀 `!` 直接运行 shell 命令，无需通过 AI：

```bash
!npm run build
```

输出实时流式传输。TTY 命令如 `vim`、`ssh` 和 `top` 获得完整的终端访问权限。长输出会折叠为头部+尾部视图 — 按 Ctrl+O 展开。

## 管理上下文

使用 `/context` 查看和管理会话中包含的文件：

```bash
/context show              # 查看上下文细分及每个文件的 token 使用量
/context add "src/**/*.ts" # 通过 glob 模式添加文件
/context remove src/app.js # 移除上下文规则
/context clear             # 移除所有规则
```

有关详细文档，请参阅[上下文管理](./context.md)。

## 会话持久化

Kiro 可以根据您启动会话的文件夹记住您的对话。当您在之前与 Kiro 聊天的地方启动会话时，您可以告诉 Kiro 自动加载该对话历史，从而无缝继续您的讨论。

### 基于目录的持久化

如果是您第一次在该目录中聊天，Kiro 将启动新对话（考虑任何指定的上下文）。

要在当前目录中显式恢复对话，请使用：

```bash
$ kiro-cli chat --resume
```

要通过 ID 恢复特定会话：

```bash
$ kiro-cli chat --resume-id <SESSION_ID>
```

要打开交互式会话选择器以从以前的会话中选择：

```bash
$ kiro-cli chat --resume-picker
```

### 开始新对话

您可以使用 `/chat new` 命令开始新对话，而无需重启 CLI。这会将您当前的会话保存到数据库并原地开始新的会话。

```bash
# 开始新对话
/chat new

# 带初始提示开始新对话
/chat new how do I set up a React project
```

使用 `/chat resume` 返回任何以前的会话。

### 手动保存和加载对话

您还可以在聊天会话中使用以下命令手动保存和加载对话：

```bash
/chat save [path] – 将当前对话保存到 JSON 文件。

    添加 -f 或 --force 以覆盖现有文件

    示例：

    /chat save ./my-project-conversation -f

    /chat save /home/user/project/my-project-conversation.json

    您不能使用 ~ 表示您的主目录。

/chat load [path] – 从之前保存的 JSON 文件加载对话

    示例：/chat load ./my-project-conversation.json
```

> **注意**
> `/chat save` 和 `/chat load` 命令独立于对话最初创建的目录运行。加载对话时，请注意它将替换您当前的对话，无论它是从哪个目录保存的。

---

**相关文档：**
- [Models](./models.md)
- [Session Management](./session-management.md)

---

*页面更新时间：2026年5月19日*
