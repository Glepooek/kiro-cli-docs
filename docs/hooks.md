# Hooks

Hooks 允许您在 Agent 生命周期和工具执行的特定点执行自定义命令。这可以实现安全验证、日志记录、格式化、上下文收集和其他自定义行为。

## 定义 Hooks

Hooks 在 Agent 配置文件中定义。有关完整的语法和示例,请参阅 Agent 配置参考。

## Hook 事件

Hooks 通过 STDIN 接收 JSON 格式的 hook 事件:

```json
{
  "hook_event_name": "agentSpawn",
  "cwd": "/current/working/directory",
  "session_id": "abc123-def456-789"
}
```

对于工具相关的 hooks,包含额外的字段:
- `session_id`: 当前会话 UUID(在所有 hook 事件中可用)
- `tool_name`: 正在执行的工具名称
- `tool_input`: 工具特定的参数(请参阅各个工具文档)
- `tool_response`: 工具执行结果(仅 PostToolUse)

## Hook 输出

- **退出代码 0**: Hook 成功。STDOUT 被捕获但不显示给用户。
- **退出代码 2**: (仅 PreToolUse) 阻止工具执行。STDERR 返回给 LLM。
- **其他退出代码**: Hook 失败。STDERR 作为警告显示给用户。

## 工具匹配

使用 `matcher` 字段指定 hook 应用于哪些工具。您可以使用规范工具名称或其别名。

### 示例

- `"fs_write"` 或 `"write"` - 匹配 write 工具
- `"fs_read"` 或 `"read"` - 匹配 read 工具
- `"execute_bash"` 或 `"shell"` - 匹配 shell 命令执行
- `"use_aws"` 或 `"aws"` - 匹配 AWS CLI 工具
- `"@git"` - 来自 git MCP 服务器的所有工具
- `"@git/status"` - 来自 git MCP 服务器的特定工具
- `"*"` - 所有工具(内置和 MCP)
- `"@builtin"` - 仅所有内置工具
- 无 matcher - 应用于所有工具

**工具名称别名**

Hook matcher 支持规范名称(`fs_read`、`fs_write`、`execute_bash`、`use_aws`)及其别名(`read`、`write`、`shell`、`aws`)。使用您喜欢的任何一个。

## Hook 类型

### AgentSpawn

当 Agent 激活时运行。不提供工具上下文。

**Hook 事件**

```json
{
  "hook_event_name": "agentSpawn",
  "cwd": "/current/working/directory",
  "session_id": "abc123-def456-789"
}
```

**退出代码行为:**
- **0**: Hook 成功,STDOUT 添加到 Agent 的上下文
- **其他**: 向用户显示 STDERR 警告

### UserPromptSubmit

当用户提交提示时运行。输出添加到对话上下文。

**Hook 事件**

```json
{
  "hook_event_name": "userPromptSubmit",
  "cwd": "/current/working/directory",
  "session_id": "abc123-def456-789",
  "prompt": "user's input prompt"
}
```

**退出代码行为:**
- **0**: Hook 成功,STDOUT 添加到 Agent 的上下文
- **其他**: 向用户显示 STDERR 警告

### PreToolUse

在工具执行之前运行。可以验证和阻止工具使用。

**Hook 事件**

```json
{
  "hook_event_name": "preToolUse",
  "cwd": "/current/working/directory",
  "session_id": "abc123-def456-789",
  "tool_name": "read",
  "tool_input": {
    "operations": [
      {
        "mode": "Line",
        "path": "/current/working/directory/docs/hooks.md"
      }
    ]
  }
}
```

**退出代码行为:**
- **0**: 允许工具执行。
- **2**: 阻止工具执行,将 STDERR 返回给 LLM。
- **其他**: 向用户显示 STDERR 警告,允许工具执行。

### PostToolUse

在工具执行之后运行,可以访问工具结果。

**退出代码行为:**
- **0**: Hook 成功。
- **其他**: 向用户显示 STDERR 警告。工具已运行。

### Stop

当助手完成响应用户时运行(在每个轮次结束时)。这对于运行后处理任务(如代码编译、测试、格式化或助手响应后的清理)很有用。

**注意**: Stop hooks 不使用 matcher,因为它们与特定工具无关。

## 超时

默认超时为 30 秒(30,000ms)。使用 `timeout_ms` 字段配置。

## 缓存

成功的 hook 结果基于 `cache_ttl_seconds` 缓存:
- `0`: 不缓存(默认)
- `> 0`: 缓存成功结果指定的秒数
- AgentSpawn hooks 永远不会缓存
