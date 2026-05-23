# 配置

本指南提供了在 Kiro CLI 中配置模型上下文协议 (MCP) 服务器的详细信息，包括配置文件结构、服务器设置和管理。

## 配置文件结构

MCP 配置文件使用 JSON 格式，结构如下：

```json
{
  "mcpServers": {
    "local-server-name": {
      "command": "command-to-run-server",
      "args": ["arg1", "arg2"],
      "env": {
        "ENV_VAR1": "hard-coded-variable",
        "ENV_VAR2": "${EXPANDED_VARIABLE}"
      },
      "disabled": false,
      "disabledTools": ["tool_name3"]
    },
    "remote-server-name": {
      "url": "https://endpoint.to.connect.to",
      "headers": {
        "HEADER1": "value1",
        "HEADER2": "value2"
      },
      "disabled": false,
      "disabledTools": ["tool_name3"]
    }
  }
}
```

### 配置属性

#### 本地服务器

| 属性 | 类型 | 必需 | 描述 |
|------|------|------|------|
| `command` | String | 是 | 运行 MCP 服务器的命令 |
| `args` | Array | 否 | 传递给命令的参数 |
| `env` | Object | 否 | 服务器进程的环境变量 |
| `disabled` | Boolean | 否 | 服务器是否被禁用（默认：false） |
| `autoApprove` | Array | 否 | 自动批准而无需提示的工具名称 |
| `disabledTools` | Array | 否 | 调用 Agent 时要省略的工具名称 |

#### 远程服务器

| 属性 | 类型 | 必需 | 描述 |
|------|------|------|------|
| `url` | String | 是 | 远程 MCP 服务器的 HTTPS 端点（或 localhost 的 HTTP 端点） |
| `headers` | Object | 否 | 连接期间传递给 MCP 服务器的标头 |
| `env` | Object | 否 | 服务器进程的环境变量 |
| `disabled` | Boolean | 否 | 服务器是否被禁用（默认：false） |
| `autoApprove` | Array | 否 | 自动批准而无需提示的工具名称 |
| `disabledTools` | Array | 否 | 调用 Agent 时要省略的工具名称 |

## 配置示例

### 带环境变量的本地服务器

```json
{
  "mcpServers": {
    "web-search": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-bravesearch"
      ],
      "env": {
        "BRAVE_API_KEY": "${BRAVE_API_KEY}"
      }
    }
  }
}
```

### 带标头的远程服务器

```json
{
  "mcpServers": {
    "api-server": {
      "url": "https://api.example.com/mcp",
      "headers": {
        "Authorization": "Bearer ${API_TOKEN}",
        "X-Custom-Header": "value"
      }
    }
  }
}
```

### 多个服务器

```json
{
  "mcpServers": {
    "fetch": {
      "command": "uvx",
      "args": ["mcp-server-fetch"]
    },
    "git": {
      "command": "uvx",
      "args": ["mcp-server-git"],
      "env": {
        "GIT_CONFIG_GLOBAL": "/dev/null"
      }
    },
    "aws-docs": {
      "command": "npx",
      "args": ["-y", "@aws/aws-documentation-mcp-server"]
    }
  }
}
```

## MCP 服务器加载优先级

当多个配置定义同一个 MCP 服务器时，它们按以下层次结构加载（从高到低优先级）：
1. **Agent 配置** - agent JSON 中的 `mcpServers` 字段
2. **工作区 MCP JSON** - `.kiro/settings/mcp.json`
3. **全局 MCP JSON** - `~/.kiro/settings/mcp.json`

### 示例场景

**完全覆盖：**

```
Agent 配置:     { "fetch": { command: "fetch-v2" } }
工作区配置: { "fetch": { command: "fetch-v1" } }
全局配置:    { "fetch": { command: "fetch-old" } }

结果: 只使用 agent 配置中的 "fetch-v2"
```

**叠加（不同名称）：**

```
Agent 配置:     { "fetch": {...} }
工作区配置: { "git": {...} }
全局配置:    { "aws": {...} }

结果: 使用所有三个服务器（fetch、git、aws）
```

**通过覆盖禁用：**

```
Agent 配置:     { "fetch": { command: "...", disabled: true } }
工作区配置: { "fetch": { command: "..." } }

结果: 不启动 fetch 服务器
```

## 环境变量

许多 MCP 服务器需要环境变量进行身份验证或配置。使用 `${VARIABLE_NAME}` 语法引用环境变量：

```json
{
  "mcpServers": {
    "server-name": {
      "env": {
        "API_KEY": "${YOUR_API_KEY}",
        "DEBUG": "true",
        "TIMEOUT": "30000"
      }
    }
  }
}
```

确保在运行 Kiro CLI 之前在 shell 中设置这些环境变量：

```bash
export YOUR_API_KEY="your-actual-key"
export DEBUG="true"
```

## 禁用服务器

要临时禁用 MCP 服务器而不删除其配置：

```json
{
  "mcpServers": {
    "server-name": {
      "disabled": true
    }
  }
}
```

## 禁用特定工具

要阻止 agent 使用 MCP 服务器的特定工具：

```json
{
  "mcpServers": {
    "server-name": {
      "disabledTools": ["delete_file", "execute_command"]
    }
  }
}
```

## 查看已加载的服务器

要在交互式聊天会话中查看当前加载的 MCP 服务器：

```bash
/mcp
```

这将显示所有活动的 MCP 服务器、其可用工具以及通过 MCP 注册表管理的服务器的注册表状态。

## OAuth 认证

支持需要 OAuth 认证的远程 MCP 服务器。当连接到受 OAuth 保护的服务器时，Kiro CLI 会自动处理基于浏览器的 OAuth 流程。

### 会话中令牌刷新

当 OAuth 令牌在会话期间过期且没有可用的刷新令牌时，Kiro CLI 会自动触发新的基于浏览器的认证流程。您不需要重启会话 — 重新认证会透明地进行，MCP 服务器将使用新令牌重新连接。

这对于颁发短期令牌但不提供刷新令牌的身份提供程序特别有用。

## 故障排除配置

1. **验证 JSON 语法**
   - 确保您的 JSON 有效，没有语法错误：
   - 检查缺失的逗号、引号或括号
   - 使用 JSON 验证器或 linter

2. **验证命令路径**
   - 确保指定的命令存在于您的 PATH 中
   - 尝试在终端中直接运行该命令

3. **检查环境变量**
   - 验证所有必需的环境变量是否已设置
   - 检查环境变量名称中的拼写错误

4. **查看配置加载**
   - 检查正在加载哪些配置文件及其优先级：
   
   ```bash
   # 检查工作区配置
   cat .kiro/settings/mcp.json
   
   # 检查用户配置
   cat ~/.kiro/settings/mcp.json
   ```

## 安全注意事项

配置 MCP 服务器时，请遵循以下安全最佳实践：
- 使用环境变量引用（例如 `${API_TOKEN}`）而不是硬编码敏感值
- 切勿将包含凭据的配置文件提交到版本控制
- 仅连接到可信的远程服务器
- 使用 `disabledTools` 限制对危险操作的访问

有关全面的安全指导，请参阅 [MCP 安全最佳实践](/docs/cli/mcp/security) 页面。

---

页面更新：2026 年 4 月 13 日
