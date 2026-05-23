# 斜杠命令

## 概述

斜杠命令是可以在交互式聊天会话中使用的特殊命令，用于快速执行操作而无需离开对话。它们以前斜杠（`/`）开头，为常见任务提供快捷方式。

## 使用斜杠命令

斜杠命令仅在交互式聊天模式下可用：

```bash
kiro chat
> /help
```

## 可用命令

### `/help`

切换到帮助代理询问关于 Kiro CLI 功能的问题，或显示经典帮助文本。

```bash
# 切换到帮助代理
> /help

# 直接提问
> /help 如何配置 MCP 服务器？

# 显示经典帮助文本
> /help --legacy

# 显示特定命令的帮助
> /help --legacy /context
```

### `/quit`

退出交互式聊天会话。

```bash
> /quit
```

别名：/exit, /q

### `/clear`

清除当前对话历史。

```bash
> /clear
```

注意：这仅清除显示，不清除已保存的对话。

### `/context`

管理上下文文件和查看上下文窗口使用情况。上下文规则确定哪些文件包含在您的 Kiro 会话中，并从当前活动代理派生。

```bash
# 显示上下文规则配置和匹配的文件
> /context show

# 添加上下文规则（文件名或 glob 模式）
> /context add src/app.js
> /context add "*.py"
> /context add "src/**/*.js"

# 移除指定的规则
> /context remove src/app.js

# 移除所有规则
> /context clear
```

**可用子命令：**
* `show` - 显示上下文规则配置和匹配的文件
* `add` - 添加上下文规则（文件名或 glob 模式）
* `remove` - 移除指定的规则

**注意：**
* 您可以添加特定文件或使用 glob 模式（例如 `*.py`、`src/**/*.js`）
* 代理规则仅适用于当前代理
* 上下文更改不会在聊天会话之间保留。要使更改永久生效，请编辑代理配置文件。
* 这些规则匹配的文件为 Kiro 提供有关您的项目或环境的额外信息

### `/model`

为当前会话选择 AI 模型。使用交互式选择器或直接指定模型名称。

```bash
# 打开交互式模型选择器
> /model

# 直接按名称选择模型
> /model claude-opus-4.6

# Tab 键自动完成模型名称
> /model clau<Tab>

# 将当前模型保存为未来会话的默认模型
> /model set-current-as-default
```

**可用子命令：**
* `set-current-as-default` - 将当前模型选择持久化为所有未来会话的默认值

**功能：**
* Tab 键自动完成在您键入时显示 API 中可用的模型
* 幽灵文本提示显示匹配的模型名称
* 如果找不到确切名称，模糊匹配会建议相似的模型
* 对模型名称和 ID 进行不区分大小写的匹配

**注意：** `set-current-as-default` 命令将您当前的模型偏好保存到 `~/.kiro/settings/cli.json`，因此它将在所有未来的聊天会话中自动使用。

### `/agent`

管理代理并在不同的代理配置之间切换。

```bash
# 列出所有可用的代理
> /agent list

# 创建新代理（默认为 AI 辅助）
> /agent create my-agent

# 创建带有描述和 MCP 服务器的代理
> /agent create my-agent -D "代码审查员" -m code-analysis

# 使用基于编辑器的模式而不是 AI 生成
> /agent create my-agent --manual

# 在特定目录中创建
> /agent create my-agent --directory workspace

# generate 是 create 的别名
> /agent generate my-agent

# 编辑当前代理
> /agent edit

# 按名称编辑特定代理（未提供名称时显示交互式选择器）
> /agent edit my-agent

# 按文件路径编辑代理
> /agent edit --path /absolute/path/to/my-agent.json

# 显示代理配置架构
> /agent schema

# 设置默认代理（未提供名称时显示交互式选择器）
> /agent set-default my-agent

# 在运行时切换到不同的代理
> /agent swap code-reviewer
```

**可用子命令：**
* `list` - 列出所有可用的代理及其描述
* `create` - 使用 AI 辅助生成创建新代理。传递 `--manual` 进行基于编辑器的创建。支持 `-D`（描述）、`-m`（MCP 服务器）、`-d`（目录）和 `-f`（模板代理）标志。
* `edit` - 编辑现有的代理配置。未提供代理名称时显示交互式选择器。支持 `--path` 进行直接文件访问。
* `generate` - `create` 的别名
* `schema` - 显示代理配置架构
* `set-default` - 为新聊天会话定义默认代理。未提供代理名称时显示交互式选择器。
* `swap` - 在运行时切换到新代理（显示代理描述以供选择）

**注意：**
* 代理可以全局存储在 `~/.kiro/agents/` 或每个工作区存储在 `.kiro/agents/`
* `--directory` 标志接受 `workspace`、`global` 或自定义路径
* 使用 `kiro-cli chat --agent agent_name` 启动带有特定代理的 kiro-cli chat
* 内置代理（`kiro_default`、`kiro_help`、`kiro_planner`）无法编辑
* 输入 `/agent ` 或 `/agent swap ` 并按 Tab 键自动完成代理名称。幽灵文本提示在您键入时内联显示，匹配 `/model` 中的行为。
* 切换代理会保留您当前的 `/model` 选择，除非目标代理的配置指定了自己的模型——然后它会切换到该模型。

编辑代理时，更改首先在**临时文件**中进行。如果验证失败（例如，无效的 JSON 或缺少必需字段），系统会提示您继续编辑或取消。原始配置文件永远不会被失败的编辑覆盖。

### `/spawn`

生成一个新的代理会话，在当前对话中并行运行任务。

```bash
# 生成带有任务的代理
> /spawn 分析 src/utils 中的测试覆盖率并提出改进建议

# 为生成的会话命名
> /spawn --name test-analysis 审查所有失败的测试并对失败进行分类
```

生成的会话与您的主对话并行运行，不会阻塞它。使用 `Ctrl+G` 打开 crew 监视器实时监视它们，该监视器显示每个生成会话的状态。

与[子代理](./subagents.md)不同（子代理由主代理生成以在任务图中委派聚焦工作），`/spawn` 是用户驱动的命令，启动一个新的长期运行的会话，您可以稍后返回。

**注意：**
* 需要任务描述 — 没有参数的 `/spawn` 会失败。
* 使用 `--name` 标记会话以便在 crew 监视器中更容易识别。

### `/chat`

管理聊天会话，包括保存、加载和在会话之间切换。Kiro CLI 在每次对话轮次自动保存所有聊天会话。

```bash
# 启动新的对话而不重新启动 CLI
> /chat new

# 带初始提示启动新对话
> /chat new 如何设置 React 项目

# 打开交互式会话选择器恢复之前的会话
> /chat resume

# 将当前会话保存到文件
> /chat save /myproject/codereview.json

# 从文件加载会话
> /chat load /myproject/codereview.json
```

**可用子命令：**
* `new` - 启动新的对话而不重新启动 CLI。可选择传递初始提示（例如 `/chat new 如何设置 React 项目`）。使用 `/chat resume` 返回到之前的会话。
* `resume` - 打开交互式会话选择器选择要恢复的会话
* `save` - 将当前会话保存到文件
* `load` - 从文件加载会话（`.json` 扩展名是可选的）
* `save-via-script` - 使用自定义脚本保存会话（通过 stdin 接收 JSON）
* `load-via-script` - 使用自定义脚本加载会话（输出 JSON 到 stdout）

**注意：**
* 会话在每次对话轮次自动保存
* 会话按目录存储，因此每个项目都有自己的会话集
* 会话选择器显示会话名称、最后活动和消息预览
* 在选择器中使用键盘快捷键：`↑`/`↓` 导航，`Enter` 选择，`/` 过滤

### `/session-id`

打印当前聊天会话 ID。当您想稍后使用 `kiro-cli chat --resume-id <ID>` 恢复此确切会话或与支持人员共享 ID 时很有用。

```bash
> /session-id
```

退出时，Kiro CLI 也会显示带有 ID 的恢复提示，以便您可以从下一个终端跳回同一会话。

#### 自定义会话存储

您可以使用自定义脚本控制聊天会话的保存和加载位置。这允许您将会话存储在版本控制系统、云存储、数据库或任何自定义位置。

**通过脚本保存：**

```bash
> /chat save-via-script ./scripts/save-to-git.sh
```

您的脚本通过 stdin 接收聊天会话 JSON。保存到 Git notes 的示例脚本：

```bash
#!/bin/bash
set -ex
COMMIT=$(git rev-parse HEAD)
TEMP=$(mktemp)
cat > "$TEMP"
git notes --ref=kiro/notes add -F "$TEMP" "$COMMIT" --force
rm "$TEMP"
echo "Saved to commit ${COMMIT:0:8}" >&2
```

**通过脚本加载：**

```bash
> /chat load-via-script ./scripts/load-from-git.sh
```

您的脚本应将聊天会话 JSON 输出到 stdout。从 Git notes 加载的示例脚本：

```bash
#!/bin/bash
set -ex
COMMIT=$(git rev-parse HEAD)
git notes --ref=kiro/notes show "$COMMIT"
```

### `/save`

将当前对话保存到文件。

```bash
# /save <路径>
> /chat save /myproject/codereview.json
```

### `/load`

加载之前保存的对话。

```bash
# 列出可用的对话
> /chat load /myproject/codereview.json
```

### `/editor`

打开默认编辑器以编写更长的提示。

```bash
> /editor
```

打开 `$EDITOR`（如果未设置则默认为 vi）以编写多行消息。用于编写详细的提示、粘贴代码片段或编写更长的说明。保存并退出编辑器以发送消息。

### `/reply`

打开编辑器，引用最近的助手消息进行回复。

```bash
> /reply
```

打开 `$EDITOR`，引用 AI 的最后回复，允许您引用和回复消息的特定部分。用于提供有关特定部分的反馈或询问有关特定细节的后续问题。

### `/checkpoint`

管理工作区检查点以跟踪和恢复文件更改。

```bash
# 初始化新检查点
> /checkpoint init

# 列出所有检查点
> /checkpoint list

# 恢复到检查点（交互式选择器）
> /checkpoint restore

# 恢复到特定检查点
> /checkpoint restore 2

# 精确状态匹配恢复（删除较新的文件）
> /checkpoint restore 2 --hard

# 查看检查点详情
> /checkpoint expand 1

# 查看检查点之间的差异
> /checkpoint diff 1 2

# 清理检查点数据
> /checkpoint clean
```

**可用子命令：**
* `init` - 创建当前工作区状态的快照
* `list` - 显示可用的检查点及其时间戳和文件统计信息
* `restore` - 将工作区恢复到以前的检查点状态
* `expand` - 显示详细的检查点信息
* `diff` - 查看检查点之间的差异
* `clean` - 清理检查点影子存储库

**工作原理：**
* 创建影子裸 git 存储库以跟踪文件更改
* 每个对话轮次创建检查点
* 每个工具使用创建子检查点
* 恢复检查点时对话历史恢复
* 在 git 存储库中自动启用（临时模式）
* 可以在非 git 目录中手动初始化

**恢复模式：**
* **默认**：恢复跟踪的更改和删除，保留检查点后创建的文件
* **硬模式**（`--hard`）：精确匹配检查点状态，删除检查点后创建的文件

**注意：** 这是一个实验性功能。使用 `kiro-cli settings chat.enableCheckpoint true` 启用。

### `/plan`

切换到计划代理，将复杂想法分解为实现计划。

```bash
# 切换到计划代理
> /plan

# 带即时提示切换
> /plan 构建用于用户管理的 REST API
```

计划代理专门将复杂想法分解为可执行的实施计划。使用 `Shift+Tab` 返回您之前的代理。

### `/guide`

切换到向导代理，获取基于文档的帮助和入门指导。

```bash
# 切换到向导代理
> /guide
```

向导代理使用索引的文档回答有关 Kiro CLI 功能、命令、工具和设置的问题。它还可以在您的 `.kiro/` 目录中创建配置文件（代理、提示、引导文件）。

使用 `Shift+Tab` 或 `/agent swap` 返回您之前的代理。

**注意**

`/guide` 命令仅在终端 UI 中可用。在经典界面中，使用 `/help` 获得相同的交互式代理体验。

### `/knowledge`

管理您的知识库，以便在文件和目录之间进行语义搜索。

```bash
# 显示所有知识库条目
> /knowledge show

# 添加文件或目录
> /knowledge add --name my-docs --path ./docs

# 带包含/排除模式添加
> /knowledge add --name src --path ./src --include "*.ts" --exclude "*.test.ts"

# 带索引类型添加
> /knowledge add --name api --path ./api --index-type Best

# 搜索知识库
> /knowledge search "认证流程"

# 移除条目
> /knowledge remove ./docs

# 更新（重新索引）条目
> /knowledge update ./docs

# 清除整个知识库
> /knowledge clear

# 取消后台索引操作
> /knowledge cancel
```

**可用子命令：**
* `show` - 列出所有知识库条目及其状态
* `add` - 将文件或目录添加到知识库
* `search` - 语义搜索索引内容
* `remove`（别名：`rm`）- 按路径移除条目
* `update` - 重新索引现有条目
* `clear` - 清除整个知识库（需要确认）
* `cancel` - 取消后台索引操作

**添加选项：**
* `--name, -n` - 条目名称（必需）
* `--path, -p` - 文件或目录路径（必需）
* `--include` - 包含模式（可指定多个）
* `--exclude` - 排除模式（可指定多个）
* `--index-type` - Fast 或 Best（默认来自设置）

**注意：** 这是一个实验性功能。使用 `kiro-cli settings chat.enableKnowledge true` 启用。

### `/compact`

压缩对话以释放上下文空间。

```bash
> /compact
```

压缩对话历史同时保留关键信息，当接近上下文限制时很有用。AI 创建到目前为止对话的摘要，并用此摘要替换消息历史，释放上下文窗口中的标记。

### `/paste`

从剪贴板粘贴图像到对话中。

```bash
> /paste
```

从系统剪贴板添加图像到对话，允许 AI 分析屏幕截图、图表、错误消息或任何视觉内容。支持常见的图像格式（PNG、JPEG 等）。

### `/tools`

查看工具和权限。默认情况下，Kiro 会请求您的许可才能使用某些工具。您可以控制信任哪些工具，以便无需确认。

```bash
# 查看所有工具、估计的标记计数和权限
> /tools

# 显示所有可用工具的输入架构
> /tools schema

# 为会话信任特定工具
> /tools trust write

# 将工具恢复为每次请求确认
> /tools untrust write

# 信任所有工具（相当于已弃用的 /acceptall）
> /tools trust-all

# 将所有工具重置为默认权限级别
> /tools reset
```

**可用子命令：**
* `schema` - 显示所有可用工具的输入架构
* `trust` - 为会话信任特定工具
* `untrust` - 将工具恢复为每次请求确认
* `trust-all` - 信任所有工具（相当于已弃用的 /acceptall）
* `reset` - 将所有运行时权限重置为默认值，包括 shell 信任模式、文件系统路径权限和拒绝的工具

**输出列：**
* `~Tokens` - 每个工具架构的估计标记计数（值 1000+ 显示为 `k`）
* `Permission` - 当前权限状态（Trusted、Ask 或 Allowed）
* `Total` - 每个来源的组合标记计数（本机工具、每个 MCP 服务器）

**注意：** 对于永久工具配置，请参阅代理配置参考。

### `/prompts`

查看和检索提示。提示是可重用的模板，帮助您快速访问常见的工作流程和任务。这些模板由您已安装和配置的 MCP 服务器提供。

```bash
# 列出工具中的可用提示或显示所有可用提示
> /prompts list

# 显示特定提示的详细信息
> /prompts details code-review

# 按名称获取特定提示
> /prompts get code-review [参数]

# 快速检索（不带 /prompts 前缀）
> @code-review [参数]

# 创建新的本地提示
> /prompts create my-prompt

# 编辑现有的本地提示
> /prompts edit my-prompt

# 移除现有的本地提示
> /prompts remove my-prompt
```

**可用子命令：**
* `list` - 列出工具中的可用提示或显示所有可用提示
* `details` - 显示特定提示的详细信息
* `get` - 按名称获取特定提示
* `create` - 创建新的本地提示
* `edit` - 编辑现有的本地提示
* `remove` - 移除现有的本地提示

**快速提示：** 要直接检索提示，使用 `@<提示名称> [参数]` 而不带 `/prompts get` 前缀。

### `/hooks`

查看上下文钩子。

```bash
> /hooks
```

显示当前会话的活动上下文钩子。

### `/usage`

显示计费和积分信息。

```bash
> /usage
```

查看您当前的使用统计和剩余积分。

### `/mcp`

查看 MCP 服务器和注册表状态。

```bash
> /mcp
```

显示当前活动的模型上下文协议服务器、其可用工具以及托管服务器的注册表状态。

当您的组织配置了注册表时，`/mcp add` 和 `/mcp remove` 将更改持久保存到您的代理配置文件，因此添加和删除在重启后仍然有效。

**管理员禁用了 MCP**

如果您的组织管理员在 Kiro 控制台中禁用了 MCP，`/mcp` 显示以下两条消息之一：
* **`MCP has been disabled by your administrator`** — MCP 已为您的组织关闭。所有服务器（用户配置、旧版、注册表和会话注入）被抑制，代理无法使用 MCP 工具。
* **`Failed to retrieve MCP settings — MCP disabled`** — Kiro 客户端无法访问治理 API。MCP 为安全而失败关闭。这通常是暂时的；重试、检查连接性，或如果问题持续存在请联系您的管理员。

MCP 治理适用于 IAM Identity Center 和 API 密钥用户。Builder ID 和社交认证用户不受影响。

### `/theme`

覆盖提示和响应文本的主题颜色。

```bash
> /theme
```

自定义用于提示输入和代理响应文本的颜色。当终端颜色检测置信度较低时回退到 ANSI 颜色。

### `/copy`

将最后的助手响应复制到剪贴板。

```bash
> /copy
```

复制最近助手响应的全部内容，包括跨工具调用的所有片段。通过 OSC 52 剪贴板转义序列在 SSH 和终端多路复用器（tmux、Zellij）上工作。超过 100KB 的负载会被跳过，以避免在具有大小限制的终端上静默截断。

### `/transcript`

在分页器中打开对话记录。

```bash
> /transcript
```

在 `$PAGER` 中打开完整的对话历史（默认为 `less`，Windows 上为 `notepad`）。按 `q` 退出并返回聊天。也可通过 `Ctrl+T` 使用。

### `/code`

管理代码智能配置并获取反馈。

```bash
# 在当前目录中初始化 LSP 驱动的代码智能
> /code init

# 强制在当前目录中重新初始化 - 重启 LSP 服务器
> /code init -f

# 获取工作区的完整概述
> /code overview

# 获取更清晰的输出概述
> /code overview --silent

# 获取工作区状态和 LSP 服务器状态
> /code status

# 查看 LSP 日志以进行故障排除
> /code logs                    # 显示最后 20 条 ERROR 日志

> /code logs -l INFO            # 显示 INFO 级别及以上

> /code logs -n 50              # 显示最后 50 条

> /code logs -l DEBUG -n 100    # 显示最后 100 条 DEBUG+ 日志

> /code logs -p ./lsp-logs.json # 将日志导出到 JSON 文件
```

**可用子命令：**
* `init` - 初始化 LSP 服务器以增强代码智能
* `overview` - 获取工作区结构的完整概述
* `status` - 显示 LSP 服务器和工作区状态的详细信息
* `logs` - 查看日志

### `/experiment`

切换实验性功能。

```bash
> /experiment
```

启用或禁用实验性 CLI 功能。

### `/tangent`

创建对话检查点以探索侧面话题。

```bash
> /tangent
```

进入或退出[切线模式](./tangent-mode.md)以探索切线话题而不中断您的主对话。使用 `Ctrl+T` 作为键盘快捷键（当切线模式启用时）。

### `/todos`

查看、管理和恢复待办事项列表。

```bash
# 查看待办事项
> /todo

# 添加待办事项
> /todo add "修复认证错误"

# 完成待办事项
> /todo complete 1
```

### `/issue`

创建新的 GitHub 问题或提出功能请求。

```bash
> /issue
```

打开工作流以向 Kiro 团队提交问题或功能请求。

### `/logdump`

创建包含日志的 zip 文件，用于支持调查和故障排除。

```bash
# 创建包含聊天日志的日志存档
> /logdump

# 包含 MCP 服务器日志
> /logdump --mcp
```

**可用选项：**
* `--mcp` - 除了聊天日志外还包含 MCP 服务器日志

**输出：**

在当前目录中创建带时间戳的 zip 文件，格式为 `q-logs-YYYY-MM-DDTHH-MM-SSZ.zip`。

**包含内容：**
* `logs/kiro-chat.log` - 主要聊天日志（始终包含）
* `logs/mcp.log` - MCP 服务器日志（仅在使用 `--mcp` 标志时）

**何时使用：**
* 报告错误或意外错误
* 与 AWS 支持共享诊断信息
* 调查代理或 MCP 服务器问题
* 故障排除工具执行问题

**示例输出：**

```
Collecting logs...
✓ Successfully created q-logs-2025-01-22T14-30-52Z.zip with 1 log file
```

**注意：** 在公开共享之前查看日志内容，因为日志可能包含文件路径和会话历史。

### `/settings`

通过交互式菜单配置主题、键盘绑定、终端输入和显示偏好。

```bash
# 打开设置菜单
> /settings

# 自定义主题颜色
> /settings theme

# 查看键盘快捷键
> /settings keybindings

# 为您的终端配置多行输入
> /settings terminal

# 切换无障碍和显示选项
> /settings display
```

**可用子命令：**
* `theme` — 带实时预览自定义提示和响应颜色
* `keybindings` — 查看可配置的键盘快捷键（只读参考）
* `terminal` — 启用 `Shift+Enter` / `Option+Enter` 进行多行输入。自动配置支持的终端应用（VS Code、Alacritty、Zed、Apple Terminal 等）或显示不支持的终端的手动解决方法
* `display` — 切换动画、ASCII 艺术和图标渲染

**注意：**
* `terminal` 子命令在修改任何终端配置文件之前创建 `.bak` 备份。
* tmux 用户需要在其 `tmux.conf` 中添加 `set -s extended-keys on` 才能使 `Shift+Enter` 工作。
* 更改立即应用于当前会话并在未来会话中持久保存。

### `/effort`

为当前会话设置模型的推理努力级别。

```bash
# 打开交互式努力选择器
> /effort

# 设置特定级别
> /effort high
> /effort max
```

**可用级别：** `low`、`medium`、`high`、`xhigh`、`max`

可用哪些级别取决于活动模型。更高的努力级别使用更多标记，但为复杂任务生成更彻底的响应。

您可以在 `~/.kiro/settings/cli.json` 的 `chat.modelDefaults` 下设置每个模型的持久默认值：

```json
{
  "chat": {
    "modelDefaults": {
      "claude-sonnet-4": {
        "effort": "high"
      },
      "claude-opus-4": {
        "effort": "max"
      }
    }
  }
}
```

**优先顺序：** 加载的会话努力 > cli.json 中的用户默认值 > 内置默认值。

还支持通过项目根目录中的 `.kiro/settings/cli.json` 进行工作区级别覆盖。

### `/rewind`

在较早的轮次分叉对话以探索不同的路径。

```bash
# 打开交互式轮次选择器
> /rewind

# 按索引跳转到特定轮次
> /rewind 4
```

从所选轮次开始创建新会话。原始会话被保留，您可以使用 `/chat load` 或 `/chat resume` 切换回来。

交互式选择器在每个轮次显示提示预览和上下文使用百分比，最新优先列出。

**注意：**
* 新会话是分叉，而不是破坏性重写。您的原始对话保持完整。
* 当对话走向错误方向时用于回溯，或用于从同一起点探索替代方法。

### `/changelog`

在 CLI 内联查看最近的发布说明。

```bash
> /changelog
```

直接在终端中显示最新的 Kiro CLI 发布说明，以便您无需离开会话即可查看更改。

## 基于技能的斜杠命令

除了上面的内置命令外，`.kiro/skills/` 和 `~/.kiro/skills/` 中定义的技能自动作为斜杠命令可用。输入 `/` 后跟技能名称直接调用它。

```bash
# 调用名为 "pr-review" 的技能
> /pr-review

# 调用名为 "cdk-deploy" 的技能
> /cdk-deploy
```

可用的技能命令取决于您的工作区和全局技能目录中存在哪些技能。

## 键盘快捷键

在交互模式下，您还可以使用：
* `Ctrl+C` - 取消当前输入 / 退出会话
* `Ctrl+D` - 退出会话
* `Ctrl+D/U` - 在监视器中的子代理之间导航
* `Ctrl+G` - 打开子代理执行监视器
* `Ctrl+J` - 插入换行符（所有终端，包括 tmux）
* `Ctrl+O` - 展开折叠的 shell 输出
* `Ctrl+R` - 反向增量历史搜索
* `Ctrl+S` - 模糊搜索命令和上下文文件，使用 tab 选择多个项目
* `Ctrl+T` - 切换切线模式以进行隔离对话（如果启用）
* `Alt+Enter` - 插入换行符（Terminal.app、Ghostty）
* `Alt+Backspace` - 删除前一个单词
* `Shift+Enter` - 插入换行符（iTerm2、Ghostty、Kitty、Warp、Zed）
* `Shift+Tab` - 进入计划模式
* `上/下箭头` - 导航命令历史
* `Tab` - 深入审批选项 / 自动完成文件引用
* `Esc` - 关闭面板、取消代理执行、清除提示队列

## 下一步

* 了解 [CLI 命令](./1_cli-commands.md)以了解终端用法
* 探索 [聊天](../chat/index.md)以了解交互式聊天功能
* 查看 [上下文管理](../chat/context.md)以了解高级上下文处理
