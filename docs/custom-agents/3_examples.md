# 代理示例

本页面提供了可用作自己工作流程起点的实用自定义代理示例。

## AWS 专家代理

此自定义代理针对 AWS 基础设施管理和开发任务进行了优化。它提供预先批准的 AWS 工具访问，同时包含相关文档。

```json
{
  "name": "aws-specialist-agent",
  "description": "专门用于 AWS 基础设施和开发任务的自定义代理",
  "prompt": "您是 AWS 基础设施专家，对云架构和最佳实践有深入了解",
  "tools": [
    "read",
    "write",
    "shell",
    "aws"
  ],
  "allowedTools": [
    "read",
    "aws"
  ],
  "toolsSettings": {
    "aws": {
      "allowedServices": [
        "s3",
        "lambda",
        "cloudformation",
        "ec2",
        "iam",
        "logs"
      ]
    },
    "write": {
      "allowedPaths": [
        "infrastructure/**",
        "scripts/**",
        "*.yaml",
        "*.yml",
        "*.json"
      ]
    }
  },
  "resources": [
    "file://README.md",
    "file://infrastructure/**/*.yaml",
    "file://infrastructure/**/*.yml",
    "file://docs/aws-setup.md",
    "file://scripts/deploy.sh"
  ],
  "hooks": {
    "agentSpawn": [
      {
        "command": "aws sts get-caller-identity",
        "timeout_ms": 10000,
        "cache_ttl_seconds": 300
      }
    ]
  },
  "model": "claude-sonnet-4"
}
```

**用例：**

- 部署 CloudFormation 堆栈
- 管理 S3 存储桶和 Lambda 函数
- 排查 AWS 服务问题
- 审查和更新基础设施即代码

## 开发工作流程代理

此自定义代理专为通用软件开发任务而设计，包括代码审查、测试和 Git 操作。

```json
{
  "name": "development-workflow-agent",
  "description": "具有 Git 集成的通用开发工作流程自定义代理",
  "prompt": "您是软件开发助手，精通 Git 工作流程和代码管理",
  "mcpServers": {
    "git": {
      "command": "git-mcp-server",
      "args": [],
      "timeout": 30000
    }
  },
  "tools": [
    "read",
    "write",
    "shell",
    "@git"
  ],
  "allowedTools": [
    "read",
    "@git/git_status",
    "@git/git_log",
    "@git/git_diff"
  ],
  "toolAliases": {
    "@git/git_status": "status",
    "@git/git_log": "log",
    "@git/git_diff": "diff"
  },
  "toolsSettings": {
    "write": {
      "allowedPaths": [
        "src/**",
        "tests/**",
        "docs/**",
        "*.md",
        "*.json",
        "package.json",
        "requirements.txt"
      ]
    }
  },
  "resources": [
    "file://README.md",
    "file://CONTRIBUTING.md",
    "file://docs/**/*.md",
    "file://package.json",
    "file://requirements.txt"
  ],
  "hooks": {
    "agentSpawn": [
      {
        "command": "git status --porcelain",
        "timeout_ms": 5000
      },
      {
        "command": "git branch --show-current",
        "timeout_ms": 3000
      }
    ]
  }
}
```

**用例：**

- 代码审查和分析
- 编写和更新测试
- Git 工作流程管理
- 文档更新
- 依赖管理


## 代码审查代理

此自定义代理专门针对代码审查任务，工具和上下文针对分析代码质量、安全性和最佳实践进行了优化。

```json
{
  "name": "code-review-agent",
  "description": "专门用于代码审查和质量分析的自定义代理",
  "prompt": "您是代码审查专家，专注于质量、安全性和最佳实践",
  "tools": [
    "read",
    "shell"
  ],
  "allowedTools": [
    "read",
    "shell"
  ],
  "toolsSettings": {
    "shell": {
      "allowedCommands": [
        "grep",
        "find",
        "wc",
        "head",
        "tail",
        "cat",
        "diff",
        "git diff",
        "git log",
        "eslint",
        "pylint",
        "rubocop"
      ]
    }
  },
  "resources": [
    "file://CONTRIBUTING.md",
    "file://docs/coding-standards.md",
    "file://docs/security-guidelines.md",
    "file://.eslintrc.json",
    "file://.pylintrc",
    "file://pyproject.toml"
  ],
  "hooks": {
    "agentSpawn": [
      {
        "command": "git diff --name-only HEAD~1",
        "timeout_ms": 5000,
        "max_output_size": 2048
      }
    ],
    "userPromptSubmit": [
      {
        "command": "find . -name '*.py' -o -name '*.js' -o -name '*.ts' | wc -l",
        "timeout_ms": 3000,
        "cache_ttl_seconds": 60
      }
    ]
  }
}
```

**用例：**

- 审查拉取请求的代码质量
- 识别安全漏洞
- 检查编码标准的遵守情况
- 分析代码复杂性和可维护性
- 建议改进和重构机会

## 项目特定代理

此示例展示如何创建针对特定项目定制的自定义代理，包括项目特定的工具、文档和构建流程。

```json
{
  "name": "mobile-app-agent",
  "description": "移动应用后端项目的自定义代理",
  "prompt": "您是移动应用后端开发专家，精通 Docker 和数据库管理",
  "mcpServers": {
    "docker": {
      "command": "docker-mcp-server",
      "args": ["--socket", "/var/run/docker.sock"]
    },
    "database": {
      "command": "postgres-mcp-server",
      "args": ["--connection", "postgresql://localhost:5432/myapp"],
      "env": {
        "PGPASSWORD": "$DATABASE_PASSWORD"
      }
    }
  },
  "tools": [
    "read",
    "write",
    "shell",
    "@docker",
    "@database"
  ],
  "allowedTools": [
    "read",
    "@docker/docker_ps",
    "@docker/docker_logs",
    "@database/query_read_only"
  ],
  "toolAliases": {
    "@docker/docker_ps": "containers",
    "@docker/docker_logs": "logs",
    "@database/query_read_only": "query"
  },
  "toolsSettings": {
    "write": {
      "allowedPaths": [
        "src/**",
        "tests/**",
        "migrations/**",
        "docker-compose.yml",
        "Dockerfile",
        "requirements.txt"
      ]
    },
    "shell": {
      "allowedCommands": [
        "npm test",
        "npm run build",
        "python manage.py test",
        "docker-compose up",
        "docker-compose down"
      ]
    }
  },
  "resources": [
    "file://README.md",
    "file://docs/api-documentation.md",
    "file://docs/database-schema.md",
    "file://docker-compose.yml",
    "file://requirements.txt",
    "file://src/config/settings.py"
  ],
  "hooks": {
    "agentSpawn": [
      {
        "command": "docker-compose ps",
        "timeout_ms": 10000,
        "cache_ttl_seconds": 30
      },
      {
        "command": "git status --porcelain",
        "timeout_ms": 5000
      }
    ]
  }
}
```

**用例：**

- 管理 Docker 容器和服务
- 运行数据库查询和迁移
- 构建和测试应用程序
- 调试生产问题
- 更新 API 文档



## 创建有效自定义代理的技巧

- **从简单开始** - 从基本工具配置开始，根据需要增加复杂性
- **使用描述性名称** - 选择清楚表明其用途的自定义代理名称
- **包含相关上下文** - 将项目文档和配置文件添加到资源中
- **预先批准安全工具** - 在 allowedTools 中包含常用的低风险工具
- **使用 hooks 获取动态上下文** - 通过命令 hooks 包含当前系统状态
- **限制工具范围** - 使用 toolsSettings 将工具访问限制为相关路径和服务
- **彻底测试** - 验证您的自定义代理配置是否按预期工作
- **记录您的自定义代理** - 使用清晰的描述帮助团队成员了解自定义代理用途

## 远程 MCP 服务器集成

此示例展示配置为使用远程 MCP 服务器的代理：

```json
{
  "name": "domain-finder",
  "description": "具有域名搜索功能的代理",
  "prompt": "您使用 find-a-domain 服务帮助用户查找和研究域名。",
  "mcpServers": {
    "find-a-domain": {
      "type": "http",
      "url": "https://api.findadomain.dev/mcp"
    }
  },
  "tools": ["@find-a-domain"],
  "allowedTools": ["@find-a-domain"]
}
```

此代理通过远程 MCP 服务器提供对域名搜索工具的访问。如果服务器需要 OAuth 身份验证，请在提示时使用 `/mcp` 命令进行身份验证。

### 带 OAuth 配置

对于需要特定范围的 OAuth 服务器：

```json
{
  "name": "github-agent",
  "description": "具有 GitHub API 访问权限的代理",
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.github.com/mcp",
      "oauth": {
        "oauthScopes": ["repo", "user", "read:org"]
      }
    }
  },
  "tools": ["@github"],
  "allowedTools": ["@github"]
}
```

如果遇到 OAuth 范围错误，可以使用空数组绕过范围要求：

```json
{
  "name": "github-agent",
  "description": "具有 GitHub API 访问权限的代理",
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.github.com/mcp",
      "oauth": {
        "oauthScopes": []
      }
    }
  },
  "tools": ["@github"],
  "allowedTools": ["@github"]
}
```

---

## 下一步

- [配置参考](2_configuration-reference.md)
