# MCP 注册表

企业 MCP 服务器治理，允许管理员控制用户可以访问哪些服务器。

**面向管理员**

如果您是设置 MCP 注册表的管理员，请参阅 [MCP 治理](/docs/cli/enterprise/governance/mcp) 获取配置说明。

## 概述

使用 IAM Identity Center 的 Pro 级客户可以通过 MCP 注册表控制 MCP 服务器访问。由管理员配置后，用户只能使用注册表中明确允许的 MCP 服务器。为企业部署提供集中治理。

## 工作原理

**无注册表**（默认）：
- 用户可以添加任何 MCP 服务器
- 服务器在 agent 文件或通过 CLI 配置
- 无集中控制

**有注册表**（企业）：
- 管理员配置允许的服务器
- 用户从注册表列表中选择
- 不能添加自定义服务器
- 集中治理和安全

## 添加服务器

### 在聊天中

```bash
/mcp add
```

显示来自组织注册表的服务器交互列表。

### 从命令行

```bash
# 添加特定服务器
kiro-cli mcp add --name myserver

# 添加到工作区
kiro-cli mcp add --scope workspace

# 添加到特定 agent
kiro-cli mcp add --agent myagent

# 交互式选择
kiro-cli mcp add
```

**注册表模式**

服务器名称必须与注册表匹配。在注册表模式下无法添加自定义服务器。

## 移除服务器

### 在聊天中

```bash
/mcp remove
```

交互式菜单选择要移除的服务器。

### 从命令行

```bash
kiro-cli mcp remove --name <server-name>
```

## 查看可用服务器

### 在聊天中

```bash
/mcp list
```

显示：
- 所有本地配置的 MCP 服务器
- 服务器状态和配置
- 每个服务器的可用工具

## 自定义

即使启用了注册表模式，您也可以在 `agent.json` 中使用注册表覆盖来自定义服务器。您的值会合并到注册表默认值之上 — 例如，如果注册表设置 `NODE_ENV=development` 而您的 agent 设置 `NODE_ENV=production`，则服务器以 `NODE_ENV=production` 运行。`env` 和 `headers` 对象按键合并，因此您未覆盖的注册表默认值会被保留。

### 注册表服务器覆盖

使用 `"type": "registry"` 及可选的 `env`、`headers` 或 `timeout` 覆盖：

```json
{
  "mcpServers": {
    "github": {
      "type": "registry",
      "env": {
        "GITHUB_TOKEN": "$GITHUB_TOKEN",
        "GITHUB_ORG": "my-org"
      },
      "timeout": 60000
    }
  }
}
```

覆盖字段：
- **本地 (stdio) 服务器** — `env`（环境变量）、`timeout`（请求超时，单位毫秒）
- **远程 (HTTP) 服务器** — `headers`（HTTP 标头）、`timeout`（请求超时，单位毫秒）

## 持久化

通过 `/mcp add` 和 `/mcp remove` 所做的更改会自动写回您的 agent 配置文件。添加的服务器在重启 CLI 后仍然可用，移除的服务器在会话间保持移除状态 — 无需手动编辑。

## 示例

### 添加注册表服务器

```bash
/mcp add
```

**输出**：

```
Select MCP server from registry:
  git-server (Git operations)
  github-server (GitHub integration)
  aws-tools (AWS operations)
```

### 添加到特定 agent

```bash
kiro-cli mcp add --name git-server --agent rust-dev
```

将 git-server 添加到 rust-dev agent 配置。

### 自定义远程注册表服务器

对于远程 (HTTP) 注册表服务器，以相同方式覆盖 `headers` 和 `timeout`：

```json
{
  "mcpServers": {
    "github": {
      "type": "registry",
      "headers": {
        "X-Team-Id": "platform"
      },
      "timeout": 30000
    }
  }
}
```

服务器使用注册表的 URL 和 OAuth 配置，加上您的自定义标头和超时。

## 故障排除

### MCP 功能被禁用

**症状**：完全无法使用 MCP
**原因**：组织完全禁用了 MCP
**解决方案**：联系管理员获取 MCP 访问权限

### 无法获取 MCP 设置

**症状**：获取 MCP 配置时出错
**原因**：网络问题或服务器错误
**解决方案**：临时问题 — 稍后重试或联系管理员

### 无法添加自定义服务器

**症状**：服务器不在列表中
**原因**：注册表模式只允许注册表服务器
**解决方案**：请求管理员将服务器添加到注册表

### 服务器不在注册表中

**症状**：所需服务器不可用
**原因**：未添加到组织的注册表
**解决方案**：联系管理员请求添加服务器

## 限制

- 注册表模式仅适用于使用 IAM Identity Center 的 Pro 级用户
- 无法添加不在注册表中的服务器
- 管理员控制可用服务器
- 注册表模式下不允许自定义服务器

## 技术详情

**注册表来源**：由管理员在组织级别配置

**作用域选项**：
- 全局：`~/.kiro/mcp.json`
- 工作区：`.kiro/mcp.json`
- 特定 Agent：在 agent 配置中

**自定义**：即使在注册表模式下也可以自定义环境变量和 HTTP 标头

**回退**：如果注册表不可用，MCP 功能将被禁用

## 下一步

- 了解 [MCP 配置](/docs/cli/mcp/configuration)
- 查看 [MCP 治理](/docs/cli/enterprise/governance/mcp)（面向管理员）
- 参阅 [MCP 示例](/docs/cli/mcp/examples)
- 了解 [MCP 安全](/docs/cli/mcp/security)

---

页面更新：2026 年 5 月 8 日
