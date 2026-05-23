# 示例

本指南提供了几个模型上下文协议 (MCP) 服务器的示例信息、它们的功能以及如何在 Kiro 中设置它们。

**警告**

只从可信来源添加 MCP 服务器，并查看所有适用的服务器许可信息和文档。Kiro 不对任何第三方 MCP 服务器或其他包负责。

## AWS 文档服务器

AWS 文档服务器提供对 AWS 文档的访问、搜索功能和内容推荐。

**功能**
- 跨所有服务搜索 AWS 文档
- 以 markdown 格式阅读文档页面
- 获取与特定文档页面相关的内容推荐

### 设置说明

#### 先决条件

1. 从 Astral 安装 uv：

```bash
# 在 macOS/Linux 上
curl -LsSf https://astral.sh/uv/install.sh | sh

# 在 Windows PowerShell 上
irm https://astral.sh/uv/install.ps1 | iex
```

2. 安装 Python 3.10 或更新版本：

```bash
uv python install 3.10
```

#### 配置

对于 macOS/Linux：

```json
{
  "mcpServers": {
    "aws-docs": {
      "command": "uvx",
      "args": ["awslabs.aws-documentation-mcp-server@latest"],
      "env": {
        "FASTMCP_LOG_LEVEL": "ERROR"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

对于 Windows：

```json
{
  "mcpServers": {
    "aws-docs": {
      "command": "uv",
      "args": [
        "tool",
        "run",
        "--from",
        "awslabs.aws-documentation-mcp-server@latest",
        "awslabs.aws-documentation-mcp-server.exe"
      ],
      "env": {
        "FASTMCP_LOG_LEVEL": "ERROR"
      }
    }
  }
}
```

### 可用工具

| 工具名称 | 描述 |
|----------|------|
| mcp_aws_docs_search_documentation | 搜索特定主题的 AWS 文档 |
| mcp_aws_docs_read_documentation | 以 markdown 格式阅读 AWS 文档页面 |
| mcp_aws_docs_recommend | 获取与文档页面相关的内容推荐 |

### 使用示例

```
# 搜索有关 S3 存储桶策略的信息
Search AWS documentation for S3 bucket policies

# 阅读特定文档
Read the AWS Lambda function URLs documentation

# 获取推荐
Find related content to AWS ECS task definitions
```

## GitHub MCP 服务器

GitHub MCP 服务器允许 Kiro 与 GitHub 仓库、问题和拉取请求进行交互。

### 功能

- 访问仓库信息，包括文件、提交和分支
- 创建和管理问题和拉取请求
- 在仓库中搜索特定内容

### 设置说明

**警告**

之前推荐的 @modelcontextprotocol/server-github 包已被归档。GitHub 现在推荐使用其官方基于 Docker 的 MCP 服务器。

### 先决条件

1. 如果尚未安装 Docker：
   - macOS 和 Windows 使用 Docker Desktop
   - Linux 使用 Docker Engine
2. 创建 GitHub 个人访问令牌：
   - 进入 GitHub Settings > Developer settings > Personal access tokens (fine-grained)
   - 根据您需要的工具权限生成新令牌

### 配置

按照官方 GitHub 文档中的以下步骤：
1. 在您的工作区目录中创建 `.kiro/settings/mcp.json` 文件（或编辑现有文件）
2. 添加此配置：

```json
{
  "mcpServers": {
    "github": {
      "command": "docker",
      "args": [
        "run", 
        "-i", 
        "--rm", 
        "-e", 
        "GITHUB_PERSONAL_ACCESS_TOKEN",
        "ghcr.io/github/github-mcp-server"
      ],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "your-token-here"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

有关更详细的安装说明，请参阅官方 GitHub MCP Server 文档。

### 常用工具

GitHub MCP 服务器提供了一套全面的工具用于与 GitHub 交互。以下是一些最常用的工具，按类别组织：

| 类别 | 工具名称 | 描述 |
|------|----------|------|
| Repository Tools | search_repositories | 搜索 GitHub 仓库 |
| Repository Tools | list_branches | 列出仓库中的分支 |
| Issue Tools | list_issues | 列出仓库中的问题 |
| Issue Tools | update_issue | 更新现有问题 |
| Issue Tools | add_issue_comment | 向问题添加评论 |
| Pull Request Tools | create_pull_request | 创建新的拉取请求 |

### 可用工具集

GitHub MCP 服务器将其功能组织成可以按需启用或禁用的工具集。默认情况下，所有工具集都启用。您可以在配置 GitHub MCP 服务器时指定要启用的工具集。这允许您控制 AI 工具可以使用哪些 GitHub API 功能。

使用 Docker 配置工具集：

```bash
docker run -i --rm \
  -e GITHUB_PERSONAL_ACCESS_TOKEN=<your-token> \
  -e GITHUB_TOOLSETS="repos,issues,pull_requests,actions,code_security,experiments" \
  ghcr.io/github/github-mcp-server
```

### 使用示例

```
# 获取仓库信息
Show me information about the tensorflow/tensorflow repository

# 搜索代码
Find examples of React hooks in facebook/react

# 创建问题
Create an issue in my repository about the login bug
```

## 自定义 MCP 服务器

您可以创建自己的 MCP 服务器来扩展 Kiro 的能力以满足特定需求。

### 创建自定义服务器

1. 选择编程语言（Python、Node.js 等）
2. 使用可用的库实现 MCP 协议
3. 定义您的工具及其功能
4. 打包并分发您的服务器

#### 自定义服务器开发资源

- [MCP 协议规范](https://modelcontextprotocol.io/specification/2025-06-18)
- [MCP 服务器模板 (Python)]()
- [MCP 服务器模板 (Node.js)]()

### 其他 MCP 服务器

#### 数据库服务器

- **PostgreSQL MCP Server**：查询和管理 PostgreSQL 数据库
- **MongoDB MCP Server**：与 MongoDB 数据库交互

#### 开发工具

- **Docker MCP Server**：管理 Docker 容器和镜像
- **Kubernetes MCP Server**：与 Kubernetes 集群交互

### 发现更多 MCP 服务器

要发现更多 MCP 服务器：
- 访问 [MCP 注册表](https://github.com/modelcontextprotocol/registry)
- 查看 [GitHub MCP 组织](https://github.com/modelcontextprotocol)
- 在 npm 或 PyPI 上搜索 **mcp-server**

## 下一步

- 查看 [安全最佳实践](/docs/cli/mcp/security)
- 返回 [MCP 概述](/docs/cli/mcp)

---

页面更新：2025 年 11 月 26 日
