# 配置

### 配置文件路径

您可以配置 Kiro CLI 以匹配您的开发偏好和团队标准。您可以在以下三个作用域之一设置配置:
1. **全局** - 应用于使用 Kiro 的所有项目的配置 - `<user-home>/.kiro/`
2. **项目** - 特定于项目的配置 - `<project-root>/.kiro`
3. **代理** - 代理配置文件中定义的配置 - `<user-home | project-root>/.kiro/agents`

**使用 KIRO_HOME 覆盖全局目录**

设置 `KIRO_HOME` 环境变量将全局 `~/.kiro` 目录重定向到其他位置。设置后，代理、提示、技能、steering、设置和会话都根据 `KIRO_HOME` 解析，这对于在同一台机器上保持单独的 Kiro 配置文件很方便。

| 配置 | 全局作用域 | 项目作用域 |
|------|-----------|-----------|
| MCP 服务器 | `~/.kiro/settings/mcp.json` | `.kiro/settings/mcp.json` |
| 提示 | `~/.kiro/prompts` | `.kiro/prompts` |
| 自定义代理 | `~/.kiro/agents` | `.kiro/agents` |
| Steering | `~/.kiro/steering` | `.kiro/steering` |
| 设置 | `~/.kiro/settings/cli.json` | |

### 在这些作用域可以配置什么

| 配置 | 用户作用域 | 项目作用域 | 代理作用域 |
|------|-----------|-----------|-----------|
| MCP 服务器 | 是 | 是 | 是 |
| 提示 | 是 | 是 | 否 |
| 自定义代理 | 是 | 是 | N/A |
| Steering | 是 | 是 | 是 |
| 设置 | 是 | N/A | N/A |

### 解决配置冲突

通过选择最接近您与 Kiro CLI 交互位置的配置来解决配置冲突。例如，如果您在全局和项目 `mcp.json` 文件中都有 MCP 配置，当您在项目文件夹中与 Kiro 聊天时，将应用项目文件夹中的 MCP 配置。

由于您也可以在全局和项目作用域定义自定义代理，如果在同一级别与代理配置发生冲突，Kiro CLI 将选择代理中的配置。

以下是配置如何合理化的优先级顺序:

| 配置 | 优先级 |
|------|--------|
| MCP 服务器 | 代理 > 项目 > 全局 |
| 提示 | 项目 > 全局 |
| 自定义代理 | 项目 > 全局 |
| Steering | 项目 > 全局 |

由于 MCP 服务器可以在三个作用域中配置，且代理配置中有 `includeMcpJson` 设置，MCP 服务器的处理略有不同。请参考 [MCP 服务器加载优先级](/docs/cli/mcp/#mcp-server-loading-priority)

---

页面更新时间: 2026年5月12日
