# 工具搜索

工具搜索按需加载 MCP 工具，而不是随每个请求发送所有工具定义。这样可以在配置了许多 MCP 服务器时保持上下文窗口清晰，并让 agent 自动发现正确的工具。

## 何时启用工具搜索

如果您有以下情况，请启用工具搜索：
- 配置了 5 个以上的 MCP 服务器
- 在长时间对话中看到上下文窗口溢出错误
- `/tools` 命令显示 MCP 工具总共消耗超过 50,000 个令牌

如果只使用少量 MCP 工具，工具搜索的开销可能不值得。

**信息**

工具搜索需要至少配置一个 [MCP 服务器](/docs/cli/mcp/configuration/)。如果只使用内置工具，则无效。

## 启用工具搜索

工具搜索默认禁用。通过设置启用：

```bash
kiro-cli settings toolSearch.enabled true
```

启用后，当 MCP 工具规范足够大以从延迟中受益时，工具搜索会自动激活。默认阈值（上下文窗口的 5% 或 50,000 个令牌）涵盖大多数情况。要强制在任何 MCP 工具存在时激活，请将阈值设置为 `0`：

```bash
kiro-cli settings toolSearch.minPct 0
kiro-cli settings toolSearch.minTokens 0
```

### 设置

使用以下设置配置工具搜索行为：

| 设置 | 默认值 | 描述 |
|------|--------|------|
| `toolSearch.enabled` | `false` | 工具搜索的主开关 |
| `toolSearch.minPct` | `5` | 当 MCP 工具规范超过上下文窗口的此百分比时激活 |
| `toolSearch.minTokens` | `50000` | 当 MCP 工具规范超过此令牌数时激活 |

当两个阈值都设置时，只要超过其中任何一个就会激活工具搜索（OR 逻辑）。当两个阈值都设置为 `0` 时，无论 MCP 工具数量如何，工具搜索始终处于活动状态。

## 验证工具搜索是否活动

当工具搜索处于活动状态时：
- `/tools` 命令显示的 MCP 工具令牌计数比禁用时减少
- 当 agent 按需发现工具时，您会在其工具使用中看到 `tool_search` 调用

## 工作原理

1. **索引**：当 MCP 服务器连接时，所有工具规范都会被索引以进行关键字搜索。每个工具的名称、服务器名称、描述和参数描述都会被分词。
2. **延迟工具列表**：模型接收紧凑的 `server_name::tool_name: description` 条目列表，而不是完整的 JSON 模式。描述被截断为 1KB。
3. **按需加载**：当模型需要工具时，它使用精确的 `tool_id`（例如 `builder-mcp::InternalSearch`）或关键字 `query`（例如 `"search documents"`）调用 `tool_search`。
4. **激活**：匹配的工具被加载，其完整模式包含在后续请求中。

## tool_search 内置工具

**工具名称**：`tool_search`

按需查找和加载 MCP 工具。自动允许无需用户权限提示（只读）。

### 参数

`tool_search` 工具接受以下参数：

| 参数 | 类型 | 必需 | 描述 |
|------|------|------|------|
| `tool_id` | string | `tool_id` 或 `query` 之一 | `server_name::tool_name` 格式的精确工具标识符 |
| `query` | string | `tool_id` 或 `query` 之一 | 用于搜索匹配工具的关键字 |
| `max_results` | integer | 否 | 返回的最大结果数（默认：5） |

提供 `tool_id` 或 `query` 中的一个，不能同时提供两者。匹配的工具会立即激活并可供调用。

### 关键字匹配

关键字搜索使用 BM25 相关性评分。工具名称按大小写边界拆分（例如 `ReadFile` → `read file`，`read_file` → `read file`）以改善匹配。只返回高于匹配阈值的结果。

默认阈值为 `1.5`，可通过 `KIRO_CLI_TOOL_SEARCH_MATCHING_THRESHOLD` 环境变量配置。

## 下一步

- [内置工具](/docs/cli/reference/built-in-tools/)：包括 `tool_search` 在内的所有内置工具
- [设置参考](/docs/cli/reference/settings/)：`toolSearch.*` 设置
- [MCP 配置](/docs/cli/mcp/configuration/)：配置 MCP 服务器
- [终端 UI](/docs/cli/terminal-ui/)：实时 shell 输出和工具显示

---

页面更新：2026 年 4 月 24 日
