# 设置

Kiro CLI 通过设置提供广泛的自定义。您可以配置从遥测到聊天行为、键盘绑定和功能切换的所有内容。

## 访问设置

直接从命令行管理设置：

```bash
# 列出所有已配置的设置
kiro-cli settings list

# 列出所有可用的设置及其描述
kiro-cli settings list --all

# 查看特定设置
kiro-cli settings telemetry.enabled

# 设置设置
kiro-cli settings telemetry.enabled true

# 删除设置
kiro-cli settings --delete chat.defaultModel

# 在编辑器中打开设置文件
kiro-cli settings open
```

### 输出格式

```bash
# 纯文本（默认）
kiro-cli settings list

# JSON
kiro-cli settings list --format json

# 格式化的 JSON
kiro-cli settings list --format json-pretty
```

## 设置参考

### 遥测和隐私

| 设置 | 类型 | 描述 | 示例 |
|------|------|------|------|
| `telemetry.enabled` | 布尔值 | 启用/禁用遥测收集 | `kiro-cli settings telemetry.enabled true` |
| `telemetryClientId` | 字符串 | 遥测的客户端标识符 | `kiro-cli settings telemetryClientId "client-123"` |

### 聊天界面

| 设置 | 类型 | 描述 | 示例 |
|------|------|------|------|
| `chat.defaultModel` | 字符串 | 对话的默认 AI 模型 | `kiro-cli settings chat.defaultModel "claude-3-sonnet"` |
| `chat.defaultAgent` | 字符串 | 默认代理配置 | `kiro-cli settings chat.defaultAgent "my-agent"` |
| `chat.diffTool` | 字符串 | 用于查看代码更改的外部差异工具（仅经典） | `kiro-cli settings chat.diffTool "delta"` |
| `chat.greeting.enabled` | 布尔值 | 聊天开始时显示问候消息 | `kiro-cli settings chat.greeting.enabled false` |
| `chat.editMode` | 布尔值 | 启用 Vi 编辑模式（仅经典） | `kiro-cli settings chat.editMode true` |
| `chat.enableNotifications` | 布尔值 | 启用桌面通知 | `kiro-cli settings chat.enableNotifications true` |
| `chat.notificationMethod` | 字符串 | 通知方法：`auto`、`bel` 或 `osc9` | `kiro-cli settings chat.notificationMethod "osc9"` |
| `chat.disableMarkdownRendering` | 布尔值 | 禁用 markdown 格式化（仅经典） | `kiro-cli settings chat.disableMarkdownRendering false` |
| `chat.disableWrap` | 布尔值 | 发出不含硬换行符的聊天输出以便干净复制粘贴；终端软换行仍然适用。 | `kiro-cli settings chat.disableWrap true` |
| `chat.disableAutoCompaction` | 布尔值 | 禁用自动对话压缩 | `kiro-cli settings chat.disableAutoCompaction true` |
| `compaction.excludeMessages` | 数字 | 压缩期间保留的最小消息对数 | `kiro-cli settings compaction.excludeMessages 2` |
| `compaction.excludeContextWindowPercent` | 数字 | 压缩期间保留的最小上下文窗口百分比 | `kiro-cli settings compaction.excludeContextWindowPercent 2` |
| `chat.enablePromptHints` | 布尔值 | 显示带有提示和快捷方式的启动提示（v1.26.0+，默认：true） | `kiro-cli settings chat.enablePromptHints false` |
| `chat.enableHistoryHints` | 布尔值 | 显示对话历史提示（仅经典） | `kiro-cli settings chat.enableHistoryHints true` |
| `chat.uiMode` | 字符串 | 要使用的 UI 变体 | `kiro-cli settings chat.uiMode "compact"` |
| `chat.ui` | 字符串 | UI 引擎：`tui`（默认）或 `classic` | `kiro-cli settings chat.ui "classic"` |
| `chat.disableGranularTrust` | 布尔值 | 禁用工具批准的分层信任选项（仅终端 UI） | `kiro-cli settings chat.disableGranularTrust true` |
| `chat.autoExpandToolOutput` | 布尔值 | 自动展开工具输出而不是折叠（仅终端 UI） | `kiro-cli settings chat.autoExpandToolOutput true` |
| `chat.modelDefaults` | 对象 | 适用于新会话的每模型默认设置（例如努力级别） | 参见[努力](../chat/effort.md) |
| `chat.enableContextUsageIndicator` | 布尔值 | 在提示中显示上下文使用百分比（仅经典） | `kiro-cli settings chat.enableContextUsageIndicator true` |

### 知识库

| 设置 | 类型 | 描述 | 示例 |
|------|------|------|------|
| `chat.enableKnowledge` | 布尔值 | 启用知识库功能 | `kiro-cli settings chat.enableKnowledge true` |
| `knowledge.defaultIncludePatterns` | 数组 | 默认包含的文件模式 | `kiro-cli settings knowledge.defaultIncludePatterns '["*.py", "*.js"]'` |
| `knowledge.defaultExcludePatterns` | 数组 | 默认排除的文件模式 | `kiro-cli settings knowledge.defaultExcludePatterns '["*.log", "node_modules"]'` |
| `knowledge.maxFiles` | 数字 | 索引的最大文件数 | `kiro-cli settings knowledge.maxFiles 1000` |
| `knowledge.chunkSize` | 数字 | 处理的文本块大小 | `kiro-cli settings knowledge.chunkSize 512` |
| `knowledge.chunkOverlap` | 数字 | 文本块之间的重叠 | `kiro-cli settings knowledge.chunkOverlap 50` |
| `knowledge.indexType` | 字符串 | 知识索引的类型 | `kiro-cli settings knowledge.indexType "fast"` |

### 键盘绑定（仅经典）

这些键盘绑定仅适用于经典界面，在终端 UI 中无效。

| 设置 | 类型 | 描述 | 示例 |
|------|------|------|------|
| `chat.skimCommandKey` | 字符 | 模糊搜索命令的键 | `kiro-cli settings chat.skimCommandKey "f"` |
| `chat.autocompletionKey` | 字符 | 自动完成提示接受的键 | `kiro-cli settings chat.autocompletionKey "Tab"` |
| `chat.tangentModeKey` | 字符 | 切线模式切换的键 | `kiro-cli settings chat.tangentModeKey "t"` |
| `chat.delegateModeKey` | 字符 | 委派命令的键 | `kiro-cli settings chat.delegateModeKey "d"` |

### 键盘绑定（终端 UI）

覆盖终端 UI 快捷键，用于取消响应、关闭覆盖菜单和退出会话。值使用 `ctrl+`、`shift+` 和 `alt+`/`meta+` 修饰符与单个键（例如 `ctrl+shift+q`、`esc`）。无效值回退到内置默认值。

| 设置 | 类型 | 默认值 | 描述 | 示例 |
|------|------|--------|------|------|
| `chat.keybindings.cancelStream` | 字符串 | `esc` | 流式传输时取消当前代理响应 | `kiro-cli settings chat.keybindings.cancelStream "ctrl+x"` |
| `chat.keybindings.closeMenu` | 字符串 | `esc` | 关闭覆盖面板和选择器 | `kiro-cli settings chat.keybindings.closeMenu "ctrl+["` |
| `chat.keybindings.quit` | 字符串 | `ctrl+c` | 退出聊天会话 | `kiro-cli settings chat.keybindings.quit "ctrl+shift+q"` |

### 工具搜索

配置按需 MCP 工具发现。详情请参阅[工具搜索](../mcp/tool-search.md)。

| 设置 | 类型 | 描述 | 示例 |
|------|------|------|------|
| `toolSearch.enabled` | 布尔值 | 启用工具搜索以进行按需 MCP 工具发现（默认：false） | `kiro-cli settings toolSearch.enabled true` |
| `toolSearch.minPct` | 数字 | 当 MCP 工具规范超过上下文窗口的此百分比时激活（默认：5） | `kiro-cli settings toolSearch.minPct 0` |
| `toolSearch.minTokens` | 数字 | 当 MCP 工具规范超过此标记计数时激活（默认：50000） | `kiro-cli settings toolSearch.minTokens 0` |

### 功能切换

| 设置 | 类型 | 描述 | 示例 |
|------|------|------|------|
| `chat.enableThinking` | 布尔值 | 为复杂推理启用思考工具 | `kiro-cli settings chat.enableThinking true` |
| `chat.enableTangentMode` | 布尔值 | 启用切线模式功能（仅经典） | `kiro-cli settings chat.enableTangentMode true` |
| `introspect.tangentMode` | 布尔值 | 为内省自动进入切线模式（仅经典） | `kiro-cli settings introspect.tangentMode true` |
| `chat.enableTodoList` | 布尔值 | 启用待办事项列表功能（仅经典） | `kiro-cli settings chat.enableTodoList true` |
| `chat.enableCheckpoint` | 布尔值 | 启用检查点功能（仅经典） | `kiro-cli settings chat.enableCheckpoint true` |
| `chat.enableDelegate` | 布尔值 | 启用委派工具（仅经典） | `kiro-cli settings chat.enableDelegate true` |
| `app.disableAutoupdates` | 布尔值 | 禁用后台自动更新 | `kiro-cli settings app.disableAutoupdates true` |

### API 和服务

| 设置 | 类型 | 描述 | 示例 |
|------|------|------|------|
| `api.timeout` | 数字 | API 请求超时（秒） | `kiro-cli settings api.timeout 30` |

### 模型上下文协议（MCP）

| 设置 | 类型 | 描述 | 示例 |
|------|------|------|------|
| `mcp.initTimeout` | 数字 | MCP 服务器初始化超时 | `kiro-cli settings mcp.initTimeout 10` |
| `mcp.noInteractiveTimeout` | 数字 | 非交互式 MCP 超时 | `kiro-cli settings mcp.noInteractiveTimeout 5` |
| `mcp.loadedBefore` | 布尔值 | 跟踪之前加载的 MCP 服务器 | `kiro-cli settings mcp.loadedBefore true` |

## 常见配置示例

### 基本设置

```bash
# 启用遥测
kiro-cli settings telemetry.enabled true

# 设置默认聊天模型
kiro-cli settings chat.defaultModel "claude-3-sonnet"

# 禁用问候消息
kiro-cli settings chat.greeting.enabled false
```

### 知识库配置

```bash
# 启用知识库
kiro-cli settings chat.enableKnowledge true

# 设置包含的文件模式
kiro-cli settings knowledge.defaultIncludePatterns '["*.py", "*.js", "*.md", "*.txt"]'

# 设置排除的文件模式
kiro-cli settings knowledge.defaultExcludePatterns '["*.log", "node_modules", ".git", "*.pyc"]'

# 设置索引的最大文件数
kiro-cli settings knowledge.maxFiles 2000
```

### 启用实验性功能

```bash
# 启用思考工具
kiro-cli settings chat.enableThinking true

# 启用切线模式
kiro-cli settings chat.enableTangentMode true

# 启用待办事项列表
kiro-cli settings chat.enableTodoList true

# 启用检查点
kiro-cli settings chat.enableCheckpoint true

# 配置键盘绑定
kiro-cli settings chat.tangentModeKey "t"
kiro-cli settings chat.delegateModeKey "d"
```

### 性能调优

```bash
# 为慢速连接增加 API 超时
kiro-cli settings api.timeout 60

# 调整知识库块大小
kiro-cli settings knowledge.chunkSize 1024

# 为长对话禁用自动压缩
kiro-cli settings chat.disableAutoCompaction true
```

## 故障排除设置

### 无效的设置值

**布尔值：** 使用 `true` 或 `false`（小写）

```bash
kiro-cli settings telemetry.enabled true  # ✓ 正确
kiro-cli settings telemetry.enabled True  # ✗ 错误
```

**数组值：** 使用带单引号的 JSON 格式

```bash
kiro-cli settings knowledge.defaultIncludePatterns '["*.py", "*.js"]'  # ✓ 正确
```

**字符串值：** 对带空格的字符串使用引号

```bash
kiro-cli settings chat.defaultModel "claude-3-sonnet"  # ✓ 正确
```

### 重置设置

删除单个设置：

```bash
kiro-cli settings --delete setting.name
```

打开设置文件进行手动编辑：

```bash
kiro-cli settings open
```

查看当前设置以识别问题：

```bash
kiro-cli settings list --all
```

### 设置文件问题

如果设置文件损坏：
1. **备份当前设置：**
   
   ```bash
   kiro-cli settings list --format json > backup.json
   ```
2. **打开设置文件：**
   
   ```bash
   kiro-cli settings open
   ```
3. **验证 JSON 语法或从备份恢复**

## 设置文件位置

设置存储在 `~/.kiro/settings/cli.json` 中。

您可以直接编辑此文件，但建议使用 `kiro-cli settings` 命令进行验证。

## 环境变量

| 变量 | 描述 |
|------|------|
| `KIRO_HOME` | 覆盖用于全局代理、提示、技能、引导、设置和会话的 `~/.kiro` 目录。用于在同一台机器上保持多个独立的 Kiro 配置文件。 |
| `KIRO_LOG_NO_COLOR` | 设置为 `1` 以禁用彩色日志输出 |
| `NO_COLOR` | 设置为任何值以禁用终端 UI 中的所有颜色输出 |
| `KIRO_ACP_RECORD_PATH` | 用于记录 TUI ACP 线路流量的 JSONL 文件路径。用于调试代理通信协议问题。 |
| `KIRO_CLI_TOOL_SEARCH_MATCHING_THRESHOLD` | 工具搜索关键字结果的最低相关性分数（默认：`1.5`） |

## 下一步

* [配置自定义代理](../custom-agents/configuration-reference.md)
* [设置 MCP 服务器](../mcp/index.md)
* [配置自定义差异工具](../chat/diff-tools.md)
* [启用实验性功能](../experimental/index.md)
* [CLI 命令参考](./1_cli-commands.md)
