# 模型上下文协议 (MCP)

模型上下文协议 (MCP) 通过连接到提供额外工具和上下文的专用服务器来扩展 Kiro 的能力。本指南帮助您在 Kiro CLI 中设置、配置和使用 MCP 服务器。

**提示**

在交互式聊天会话中，您可以使用 `/mcp` 斜杠命令查看当前加载了哪些 MCP 服务器。有关更多详细信息，请参阅 [斜杠命令](/docs/cli/reference/slash-commands)。

## 什么是 MCP？

MCP 是一种协议，允许 Kiro 与外部服务器通信以访问专用工具和信息。例如，AWS 文档 MCP 服务器提供了在 Kiro 中直接搜索、阅读和获取 AWS 文档推荐的工具。

通过 MCP，您可以：
- 访问专业知识库和文档
- 与外部服务和 API 集成
- 使用特定领域的工具扩展 Kiro 的能力
- 为您的特定工作流程创建自定义工具

## 设置 MCP

在使用 MCP 之前，请确保您已具备所需 MCP 服务器的特定先决条件（列在每个服务器的文档中）

在 Kiro CLI 中有两种配置 MCP 服务器的方式：

### 命令行

```bash
# 添加新的 MCP 服务器
kiro-cli mcp add \
  --name "awslabs.aws-documentation-mcp-server" \
  --scope global \
  --command "uvx" \
  --args "awslabs.aws-documentation-mcp-server@latest" \
  --env "FASTMCP_LOG_LEVEL=ERROR" \
  --env "AWS_DOCUMENTATION_PARTITION=aws"
```

### mcp.json 文件

MCP 服务器可以从工作区的 MCP 配置文件（`<project-root>/.kiro/settings/mcp.json`）或用户级别设置（`~/.kiro/settings/mcp.json`）加载

```json
{
  "mcpServers": {
    "local-server-name": {
      "command": "command-to-run-server",
      "args": ["arg1", "arg2"],
      "env": {
        "ENV_VAR1": "value1",
        "ENV_VAR2": "value2"
      },
      "disabled": false
    },
    "http-server-with-oauth": {
      "type": "http",
      "url": "https://api.example.com/mcp",
      "oauth": {
        "redirectUri": "127.0.0.1:8080",
        "oauthScopes": ["read", "write"]
      }
    }
  }  
}
```

对于需要 OAuth 认证的基于 HTTP 的 MCP 服务器，您可以配置：
- `oauth.redirectUri`：OAuth 流程的自定义重定向 URI（可选）
- `oauth.oauthScopes`：要请求的 OAuth 范围数组（可选）

如果您遇到 OAuth 范围错误，请使用空数组：`"oauthScopes": []`

### Agent 配置

`mcpServers` 字段指定 agent 有权访问哪些 MCP 服务器。每个服务器都通过命令和可选参数来定义。

```json
{
  "name": "myagent",
  "description": "My special agent",
  "mcpServers": {
    "fetch": {
      "command": "fetch3.1",
      "args": []
    }
  },
  "includeMcpJson": false
}
```

`includeMcpJson` 字段决定是否包含工作区和用户级别 MCP 配置文件中定义的 MCP 服务器。设置为 `true` 时，除了 agent 的 `mcpServers` 字段中定义的服务器外，agent 还可以访问用户和工作区级别配置中定义的所有 MCP 服务器。

## 故障排除

### 常见问题及解决方案

| 问题 | 解决方案 |
|------|----------|
| 连接失败 | 验证先决条件是否正确安装 |
| 权限错误 | 检查令牌和 API 密钥是否有效 |
| 工具无响应 | 查看 MCP 日志中的具体错误消息 |
| 配置未加载 | 验证 JSON 语法并保存配置文件 |

### 工具验证错误

如果您看到"以下工具因验证错误已被排除"，则该工具不符合以下要求之一：
- 工具名称超过 64 个字符（包括服务器前缀）
- 工具名称包含无效字符（必须匹配 `^[a-zA-Z][a-zA-Z0-9_]*$`）
- 工具描述为空

请联系 MCP 服务器维护者修复工具规范。

### 大描述警告

如果您看到"以下工具有大描述，可能会影响 agent 性能"，则工具描述超过 10,000 个字符。该工具仍然可以工作，但可能会减慢响应速度。请考虑让服务器维护者缩短描述。

## 其他资源

- [官方 MCP 文档](https://modelcontextprotocol.io/introduction)

## 下一步

现在您已经了解了 MCP 基础知识，请探索以下资源：
- **[示例](/docs/cli/mcp/examples/)** - 在 Kiro CLI 中使用 MCP 服务器的实际示例
- **[安全最佳实践](/docs/cli/mcp/security/)** - 安全使用 MCP 的最佳实践

---

页面更新：2026 年 3 月 3 日
