# CLI 命令

本页面提供所有 Kiro CLI 命令及其参数的完整参考。

## 全局参数

这些参数适用于任何 Kiro CLI 命令：

| 参数 | 简写 | 描述 |
|------|------|------|
| `--verbose` | `-v` | 增加日志详细程度（可重复使用：`-v`、`-vv`、`-vvv`） |
| `--agent` | | 使用特定的自定义代理配置启动会话 |
| `--help` | `-h` | 显示帮助信息 |
| `--version` | `-V` | 显示版本信息 |
| `--help-all` | | 打印所有子命令的帮助信息 |

## 命令

### kiro-cli agent

管理代理配置。代理名称现在是 create、edit 和其他子命令的位置参数。

**语法：**

```bash
kiro-cli agent [子命令] [代理名称] [选项]
```

**子命令：**

| 子命令 | 描述 |
|--------|------|
| `list` | 列出可用的代理 |
| `create <名称>` | 创建代理配置（名称为位置参数，v1.26.0+） |
| `edit [名称]` | 编辑现有的代理配置（未提供名称时默认为当前代理，v1.26.0+） |
| `validate` | 验证指定路径的配置 |
| `migrate` | 将配置文件迁移为代理（可能会破坏现有代理） |
| `set-default` | 定义启动会话时使用的默认代理 |

**示例：**

```bash
kiro-cli agent list

# v1.26.0+: 代理名称作为位置参数
kiro-cli agent create my-agent
kiro-cli agent edit my-agent
kiro-cli agent edit  # 默认编辑当前代理

# 以前的语法（仍然支持）
kiro-cli agent validate ./my-agent.json
kiro-cli agent set-default my-agent
```

**v1.26.0 新增：**
- 代理名称现在是位置参数（例如 `kiro-cli agent create my-agent` 而不是 `--name my-agent`）
- `edit` 命令在不提供参数时默认编辑当前代理

### kiro-cli chat

启动与 Kiro 的交互式聊天会话。当未指定子命令时，`kiro` 默认为 `kiro-cli chat`。

**语法：**

```bash
kiro-cli chat [选项] [输入]
```

**参数：**

| 参数 | 描述 |
|------|------|
| `--no-interactive` | 将第一个响应打印到 STDOUT，不进入交互模式 |
| `--resume` / `-r` | 从当前目录恢复之前的对话 |
| `--resume-picker` | 打开交互式会话选择器选择要恢复的会话 |
| `--resume-id <ID>` | 通过 ID 恢复特定会话 |
| `--list-sessions` | 列出当前目录的所有已保存聊天会话 |
| `--list-models` | 显示可用的模型 |
| `--delete-session <ID>` | 通过 ID 删除已保存的聊天会话 |
| `--agent` | 指定要使用的代理 |
| `--trust-all-tools` | 允许模型使用任何工具而无需确认 |
| `--trust-tools` | 仅信任指定的工具（逗号分隔列表） |
| `--require-mcp-startup` | 如果任何 MCP 服务器启动失败，则以代码 3 退出 |
| `--wrap` | 换行模式：`always`、`never` 或 `auto`（默认） |
| `INPUT` | 第一个问题（位置参数） |

**示例：**

```bash
# 启动交互式聊天
kiro-cli 

# 直接提问
kiro-cli chat "如何在 Linux 中列出文件？"

# 非交互模式，信任所有工具
kiro-cli chat --no-interactive --trust-all-tools "显示当前目录"

# 恢复之前的对话
kiro-cli chat --resume

# 通过 ID 恢复特定会话
kiro-cli chat --resume-id abc123-def456

# 打开会话选择器选择要恢复的会话
kiro-cli chat --resume-picker

# 列出所有已保存的会话
kiro-cli chat --list-sessions

# 列出可用的模型（纯文本）
kiro-cli chat --list-models

# 列出可用的模型（JSON 输出，用于脚本）
kiro-cli chat --list-models --format json

# 使用特定代理
kiro-cli chat --agent my-agent "帮助我使用 AWS CLI"
```

### kiro-cli translate

使用 AI 将自然语言指令翻译为可执行的 shell 命令。

**语法：**

```bash
kiro-cli translate [选项] [输入...]
```

**参数：**

| 参数 | 简写 | 描述 |
|------|------|------|
| `--n` | `-n` | 要生成的完成数量（最多 5 个） |
| `INPUT` | | 自然语言描述（位置参数） |

**示例：**

```bash
kiro-cli translate "列出当前目录中的所有文件"
kiro-cli translate "查找上周修改的所有 Python 文件"
kiro-cli translate "压缩所有超过 30 天的日志文件"
kiro-cli translate -n 3 "在文件中搜索文本"
```

### kiro-cli doctor

诊断和修复常见的安装和配置问题。

**语法：**

```bash
kiro-cli doctor [选项]
```

**参数：**

| 参数 | 简写 | 描述 |
|------|------|------|
| `--all` | `-a` | 运行所有诊断测试而不进行修复 |
| `--strict` | `-s` | 对警告报错 |
| `--format` | `-f` | 输出格式：`plain`、`json`、`json-pretty` |

**示例：**

```bash
kiro-cli doctor
kiro-cli doctor --all
kiro-cli doctor --strict
```

### kiro-cli update

将 Kiro CLI 更新到最新版本。

**语法：**

```bash
kiro-cli update [选项]
```

**参数：**

| 参数 | 简写 | 描述 |
|------|------|------|
| `--non-interactive` | `-y` | 不提示确认 |
| `--relaunch-dashboard` | | 更新后重新启动仪表板（默认：true） |

**示例：**

```bash
kiro-cli update
kiro-cli update --non-interactive
```

### kiro-cli theme

获取或设置自动完成下拉菜单的可视化主题。

**语法：**

```bash
kiro-cli theme [选项] [主题]
```

**参数：**

| 参数 | 描述 |
|------|------|
| `--list` | 列出所有可用的主题 |
| `--folder` | 显示主题目录路径 |
| `THEME` | 主题名称：`dark`、`light`、`system` |

**示例：**

```bash
kiro-cli theme --list
kiro-cli theme dark
kiro-cli theme light
kiro-cli theme system
```

### kiro-cli integrations

管理 Kiro 的系统集成。

**语法：**

```bash
kiro-cli integrations [子命令] [选项]
```

**子命令：**

| 子命令 | 描述 |
|--------|------|
| `install [集成]` | 安装集成（例如 kiro-command-router） |
| `uninstall [集成]` | 卸载集成 |
| `reinstall [集成]` | 重新安装集成 |
| `status` | 检查集成状态 |

**选项：**
* `--silent` / `-s`：抑制状态消息
* `--format` / `-f`：输出格式（用于 status 命令）

**示例：**

```bash
# 安装 kiro 命令路由器（v1.26.0+）
kiro-cli integrations install kiro-command-router

# 检查集成状态
kiro-cli integrations status

# 静默卸载
kiro-cli integrations uninstall --silent
```

#### Kiro 命令路由器（v1.26.0+）

kiro 命令路由器是一个统一的入口点，根据您的偏好将 `kiro` 命令路由到 CLI 或 IDE。

**解决的问题：** 默认情况下，`kiro` 命令启动 Kiro IDE。许多用户更喜欢它启动 CLI，因为他们使用应用程序图标打开 IDE。

**安装：**

```bash
# 安装路由器
kiro-cli integrations install kiro-command-router

# 将 CLI 设置为默认
kiro set-default cli

# 或将 IDE 设置为默认
kiro set-default ide
```

**安装后：**
* `kiro` - 启动您的默认选择（CLI 或 IDE）
* `kiro-cli` - 始终启动 CLI
* `kiro ide` - 始终启动 IDE

**使用场景：**
* 以 CLI 为中心的工作流，主要使用终端
* 无需每次输入 `kiro-cli` 即可快速访问 CLI
* 根据当前项目或工作流切换默认设置

### kiro-cli inline

管理键入时出现的内联建议（幽灵文本）。

**语法：**

```bash
kiro-cli inline [子命令] [选项]
```

**子命令：**

| 子命令 | 描述 |
|--------|------|
| `enable` | 启用内联建议 |
| `disable` | 禁用内联建议 |
| `status` | 显示当前状态 |
| `set-customization` | 选择自定义模型 |
| `show-customizations` | 显示可用的自定义选项 |

**示例：**

```bash
kiro-cli inline enable
kiro-cli inline disable
kiro-cli inline status
kiro-cli inline set-customization
kiro-cli inline show-customizations --format json
```

### kiro-cli login

使用 Builder ID、Identity Center 或社交登录（Google、GitHub）向 Kiro CLI 服务进行身份验证。

**语法：**

```bash
kiro-cli login [选项]
```

**选项：**

| 选项 | 描述 |
|------|------|
| `--license <类型>` | 许可证类型：`pro`（Identity Center）或 `free`（Builder ID、Google、GitHub） |
| `--identity-provider <URL>` | 身份提供者 URL（用于 Identity Center） |
| `--region <区域>` | AWS 区域（用于 Identity Center） |
| `--social <提供者>` | 社交提供者：`google` 或 `github` |
| `--use-device-flow` | 强制使用设备流程（用于远程/SSH 环境） |
| `--verbose` | 增加日志详细程度（可重复使用） |
| `--help` | 打印帮助信息 |

**身份验证方法：**

**本地环境：**
* 打开浏览器进入统一认证门户
* 交互式选择身份验证方法
* 登录选择标志（如 `--license`、`--social`）在本地通常被忽略；浏览器流程控制方法

**远程环境（SSH/终端）：**
* 自动使用设备流程
* 显示设备代码和 URL
* 在另一台设备上完成身份验证
* CLI 轮询完成状态

**示例：**

```bash
# 基本登录（本地打开浏览器，远程显示设备代码）
kiro-cli login

# Identity Center 登录
kiro-cli login --license pro --identity-provider https://my-org.awsapps.com/start --region us-east-1

# 社交登录
kiro-cli login --social google

# 强制使用设备流程（用于 SSH 会话）
kiro-cli login --use-device-flow
```

**故障排除：**
* **已登录错误**：先用 `kiro-cli logout` 注销
* **浏览器无法打开**：使用 `--use-device-flow` 标志
* **身份验证超时**：重新启动登录过程
* **Identity Center 失败**：与管理员验证 URL 和区域

### kiro-cli logout

注销 Kiro CLI 服务并清除身份验证凭据。

**语法：**

```bash
kiro-cli logout
```

**选项：**

| 选项 | 简写 | 描述 |
|------|------|------|
| `--verbose` | `-v` | 增加日志详细程度（可重复使用） |
| `--help` | `-h` | 打印帮助信息 |

**清除的内容：**
* 身份验证令牌
* 会话凭据
* 用户配置文件信息

**保留的内容：**
* 代理配置
* 已保存的对话
* 设置
* MCP 服务器配置

**示例：**

```bash
kiro-cli logout
```

**输出：**

```
You are now logged out
Run kiro-cli login to log back in to Kiro CLI
```

**注意：** 注销是用户范围的，影响所有工作区。

### kiro-cli whoami

显示当前用户和身份验证状态的信息，包括 Builder ID、IAM Identity Center 和社交登录类型的电子邮件地址。

**语法：**

```bash
kiro-cli whoami [选项]
```

**选项：**

| 选项 | 简写 | 描述 |
|------|------|------|
| `--format` | `-f` | 输出格式：`plain`、`json`、`json-pretty` |
| `--verbose` | `-v` | 增加日志详细程度（可重复使用） |
| `--help` | `-h` | 打印帮助信息 |

**输出信息：**
* 用户名/用户 ID
* 身份验证方法（Builder ID、Identity Center、社交登录）
* 会话状态
* 配置文件信息

**示例：**

```bash
# 检查当前用户
kiro-cli whoami

kiro-cli whoami --format json
kiro-cli whoami --format json-pretty
```

**示例输出（Identity Center）：**

```
Logged in with IAM Identity Center (https://my-org.awsapps.com/start)

Profile:
Q-Dev-Amazon-Profile
arn:aws:codewhisperer:us-east-1:...:profile/...
```

**示例输出（Builder ID）：**

```
Logged in with Builder ID

Profile:
builder-id-username
```

**故障排除：**
* **未登录错误**：使用 `kiro-cli login` 登录

### kiro-cli settings

管理 kiro-cli 配置设置。

**语法：**

```bash
kiro-cli settings [子命令] [选项] [键] [值]
```

**参数：**

| 参数 | 简写 | 描述 |
|------|------|------|
| `--delete` | `-d` | 删除设置 |
| `--format` | `-f` | 输出格式：`plain`、`json`、`json-pretty` |
| `KEY` | | 设置键（位置参数） |
| `VALUE` | | 设置值（位置参数） |

**子命令：**

| 子命令 | 描述 |
|--------|------|
| `open` | 在默认编辑器中打开设置文件 |
| `list` | 列出已配置的设置 |
| `list --all` | 列出所有可用的设置及其描述 |

**示例：**

```bash
# 查看所有设置
kiro-cli settings list

# 查看所有可用的设置
kiro-cli settings list --all

# 获取特定设置
kiro-cli settings telemetry.enabled

# 设置设置
kiro-cli settings telemetry.enabled true

# 删除设置
kiro-cli settings --delete chat.defaultModel

# 打开设置文件
kiro-cli settings open

# JSON 输出
kiro-cli settings list --format json-pretty
```

### kiro-cli diagnostic

运行诊断测试并生成系统信息报告以进行故障排除。

**语法：**

```bash
kiro-cli diagnostic [选项]
```

**选项：**

| 选项 | 简写 | 描述 |
|------|------|------|
| `--format` | `-f` | 输出格式：`plain`、`json`、`json-pretty`（默认：`plain`） |
| `--force` | | 强制有限诊断输出（更快，无需应用运行即可工作） |
| `--verbose` | `-v` | 增加日志详细程度（可重复使用） |
| `--help` | `-h` | 打印帮助信息 |

`plain` 格式输出 Markdown 格式的文本。

**行为：**
* **不带 `--force`**：需要 Kiro CLI 应用正在运行（先使用 `kiro-cli launch`）。通过连接到运行中的应用生成全面的诊断信息。
* **带 `--force`**：独立命令，无需应用运行即可工作。生成有限但更快的诊断信息。

**输出信息：**

诊断报告包括：
* 系统信息（操作系统、架构、内存）
* Kiro CLI 版本和构建详情
* 配置状态
* 环境变量
* 已安装的依赖项
* 潜在问题

**示例：**

```bash
# 生成完整的诊断报告
kiro-cli diagnostic

# JSON 输出
kiro-cli diagnostic --format json-pretty

# 有限输出（更快）
kiro-cli diagnostic --force
```

**故障排除：**
* **"Kiro CLI 应用未运行"错误**：用 `kiro-cli launch` 启动应用或使用 `--force` 标志进行独立诊断
* **诊断挂起**：使用 `--force` 获取更快的有限输出
* **权限错误**：以适当的权限运行或忽略错误

**使用场景：**
* 故障排除安装问题
* 向支持提供信息
* 验证环境配置
* 检查潜在问题

### kiro-cli issue

创建 GitHub 问题以提供反馈或报告错误。

**语法：**

```bash
kiro-cli issue [选项] [描述...]
```

**参数：**

| 参数 | 简写 | 描述 |
|------|------|------|
| `--force` | `-f` | 强制创建问题 |
| `DESCRIPTION` | | 问题描述（位置参数） |

**示例：**

```bash
kiro-cli issue
kiro-cli issue "Autocomplete not working in zsh"
```

### kiro-cli version

显示版本信息和更新日志。

**语法：**

```bash
kiro-cli version [选项]
```

**参数：**

| 参数 | 描述 |
|------|------|
| `--changelog` | 显示当前版本的更新日志 |
| `--changelog=all` | 显示所有版本的更新日志 |
| `--changelog=x.x.x` | 显示特定版本的更新日志 |

**示例：**

```bash
kiro-cli version
kiro-cli version --changelog
kiro-cli version --changelog=all
kiro-cli version --changelog=1.5.0
```

### kiro-cli mcp

管理模型上下文协议（MCP）服务器。

**语法：**

```bash
kiro-cli mcp [子命令] [选项]
```

**子命令：**

#### kiro-cli mcp add

添加或替换已配置的 MCP 服务器。

**参数：**

| 参数 | 描述 |
|------|------|
| `--name` | 服务器名称（必需） |
| `--command` | 启动命令（必需） |
| `--scope` | 范围：`workspace` 或 `global` |
| `--env` | 环境变量：`key1=value1,key2=value2` |
| `--timeout` | 启动超时（毫秒） |
| `--force` | 覆盖现有服务器 |

**示例：**

```bash
kiro-cli mcp add --name my-server --command "node server.js" --scope workspace
```

#### kiro-cli mcp remove

移除 MCP 服务器。

**参数：**

| 参数 | 描述 |
|------|------|
| `--name` | 服务器名称（必需） |
| `--scope` | 范围：`workspace` 或 `global` |

**示例：**

```bash
kiro-cli mcp remove --name my-server --scope workspace
```

#### kiro-cli mcp list

列出已配置的 MCP 服务器。

**语法：**

```bash
kiro-cli mcp list [范围]
```

**示例：**

```bash
kiro-cli mcp list
kiro-cli mcp list workspace
kiro-cli mcp list global
```

#### kiro-cli mcp import

从文件导入服务器配置。

**参数：**

| 参数 | 描述 |
|------|------|
| `--file` | 配置文件（必需） |
| `--force` | 覆盖现有服务器 |
| `SCOPE` | 范围：`workspace` 或 `global` |

**示例：**

```bash
kiro-cli mcp import --file config.json workspace
```

#### kiro-cli mcp status

获取 MCP 服务器的状态。

**参数：**

| 参数 | 描述 |
|------|------|
| `--name` | 服务器名称（必需） |

**示例：**

```bash
kiro-cli mcp status --name my-server
```

## 会话管理

Kiro CLI 会在每次对话轮次自动保存所有聊天会话。您可以随时从任何之前的聊天会话恢复。

### 从命令行

```bash
# 恢复最近的聊天会话
kiro-cli chat --resume

# 交互式选择要恢复的聊天会话
kiro-cli chat --resume-picker

# 列出当前目录的所有已保存聊天会话
kiro-cli chat --list-sessions

# 删除已保存的聊天会话
kiro-cli chat --delete-session <会话ID>
```

### 从聊天会话内部

使用 `/chat` 命令管理会话：

```bash
# 开始新的对话（自动保存当前会话）
/chat new

# 带初始提示开始新对话
/chat new <提示>

# 恢复聊天会话（交互式选择器）
/chat resume

# 将当前会话保存到文件
/chat save <文件路径>

# 从文件加载会话
/chat load <文件路径>
```

加载时 `.json` 扩展名是可选的 - Kiro 会尝试带和不带扩展名两种情况。

### 自定义会话存储

您可以使用自定义脚本控制聊天会话的保存和加载位置。这允许您将会话存储在版本控制系统、云存储、数据库或任何自定义位置。

```bash
# 通过自定义脚本保存会话（通过 stdin 接收 JSON）
/chat save-via-script <脚本路径>

# 通过自定义脚本加载会话（输出 JSON 到 stdout）
/chat load-via-script <脚本路径>
```

**提示：**
* 会话 ID 是唯一标识每个聊天会话的 UUID
* 会话按目录存储，因此每个项目都有自己的会话集
* 最近更新的会话首先出现在列表中

## 日志文件

Kiro CLI 维护日志文件用于故障排除：

**位置：**
* **macOS**：`$TMPDIR/kiro-log/`
* **Linux**：`$XDG_RUNTIME_DIR` 或 `/tmp/kiro-log/`

**环境变量：**

使用这些环境变量控制日志记录和 Kiro 主目录：

| 变量 | 值 | 描述 |
|------|------|------|
| `KIRO_HOME` | 路径 | 覆盖用于全局代理、提示、技能、引导、设置和会话的 `~/.kiro` 目录 |
| `KIRO_LOG_LEVEL` | `error`、`warn`、`info`、`debug`、`trace` | 设置日志详细程度（默认：`error`） |
| `KIRO_LOG_NO_COLOR` | `1`、`true`、`yes` | 禁用彩色日志输出（v1.26.0+） |

**日志级别：**

通过 `KIRO_LOG_LEVEL` 环境变量设置：
* `error`：仅错误（默认）
* `warn`：警告和错误
* `info`：信息、警告和错误
* `debug`：调试信息及以上
* `trace`：所有消息，包括详细跟踪

**示例：**

```bash
# 启用调试日志
export KIRO_LOG_LEVEL=debug
kiro-cli chat

# 禁用彩色输出（用于 CI/CD，v1.26.0+）
export KIRO_LOG_NO_COLOR=1
kiro-cli chat

# 组合使用
export KIRO_LOG_LEVEL=debug
export KIRO_LOG_NO_COLOR=true
kiro-cli chat

# 对于 fish shell
set -x KIRO_LOG_LEVEL debug
set -x KIRO_LOG_NO_COLOR 1
kiro-cli chat
```

**v1.26.0 新增：** `KIRO_LOG_NO_COLOR` 环境变量用于禁用彩色日志输出，对 CI/CD 管道和日志文件处理很有用。

**警告：** 日志文件可能包含敏感信息，包括文件路径、代码片段和命令输出。共享日志时要谨慎。

## 下一步

* [斜杠命令参考](./2_slash-commands.md)
* [设置配置](./5_settings.md)
* [故障排除指南](./troubleshooting.md)
