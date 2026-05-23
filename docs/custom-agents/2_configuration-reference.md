# 代理配置参考

每个代理配置文件可以包含以下部分：

- [`name`](#name-字段) — 代理的名称（可选，如果未指定则从文件名派生）。
- [`description`](#description-字段) — 代理的描述。
- [`prompt`](#prompt-字段) — 代理的高级上下文。
- [`mcpServers`](#mcpservers-字段) — 代理有权访问的 MCP 服务器。
- [`tools`](#tools-字段) — 代理可用的工具。
- [`toolAliases`](#toolaliases-字段) — 用于处理命名冲突的工具名称重映射。
- [`allowedTools`](#allowedtools-字段) — 可以在不提示的情况下使用的工具。
- [`toolsSettings`](#toolssettings-字段) — 特定工具的配置。
- [`resources`](#resources-字段) — 代理可用的资源。
- [`hooks`](#hooks-字段) — 在特定触发点运行的命令。
- [`includeMcpJson`](#includemcpjson-字段) — 是否包含 mcp.json 文件中的 MCP 服务器。
- [`model`](#model-字段) — 此代理使用的模型 ID。
- [`keyboardShortcut`](#keyboardshortcut-字段) — 用于快速切换到此代理的键盘快捷键。
- [`welcomeMessage`](#welcomemessage-字段) — 切换到此代理时显示的消息。

## Name 字段

`name` 字段指定代理的名称。这用于标识和显示目的。

```json
{
  "name": "aws-expert"
}
```

## Description 字段

`description` 字段提供代理功能的描述。这主要用于人类可读性，帮助用户区分不同的代理。

```json
{
  "description": "专门用于 AWS 基础设施任务的代理"
}
```

## Prompt 字段

`prompt` 字段旨在为代理提供高级上下文，类似于系统提示。它支持内联文本和 file:// URI 来引用外部文件。

### 内联提示

```json
{
  "prompt": "您是 AWS 基础设施专家"
}
```

### 文件 URI 提示

您可以使用 `file://` URI 引用外部文件。这允许您在单独的文件中维护长而复杂的提示，以便更好地组织和版本控制，同时保持代理配置整洁可读。

```json
{
  "prompt": "file://./my-agent-prompt.md"
}
```

#### 文件 URI 路径解析

- **相对路径**: 相对于代理配置文件的目录解析
  - `"file://./prompt.md"` → 代理配置所在目录中的 prompt.md
  - `"file://../shared/prompt.md"` → 父目录中的 prompt.md
- **绝对路径**: 按原样使用
  - `"file:///home/user/prompts/agent.md"` → 文件的绝对路径

#### 文件 URI 示例

```json
{
  "prompt": "file://./prompts/aws-expert.md"
}
```

```json
{
  "prompt": "file:///Users/developer/shared-prompts/rust-specialist.md"
}
```

## McpServers 字段

`mcpServers` 字段指定代理有权访问哪些模型上下文协议（MCP）服务器。每个服务器使用命令和可选参数定义。

```json
{
  "mcpServers": {
    "fetch": {
      "command": "fetch3.1",
      "args": []
    },
    "git": {
      "command": "git-mcp",
      "args": [],
      "env": {
        "GIT_CONFIG_GLOBAL": "/dev/null"
      },
      "timeout": 120000
    }
  }
}
```

每个 MCP 服务器配置可以包含：

- `command`（必需）: 启动 MCP 服务器的命令
- `args`（可选）: 传递给命令的参数
- `env`（可选）: 为服务器设置的环境变量
- `timeout`（可选）: 每个 MCP 请求的超时时间（毫秒）（默认: 120000）
- `oauth`（可选）: 基于 HTTP 的 MCP 服务器的 OAuth 配置
  - `clientId`（可选）: 当动态客户端注册（DCR）失败时用作回退的预注册 OAuth 客户端 ID
  - `redirectUri`（可选）: OAuth 流程的自定义重定向 URI
  - `oauthScopes`（可选）: 要请求的 OAuth 范围数组

### OAuth 配置

对于需要 OAuth 身份验证的基于 HTTP 的 MCP 服务器，您可以配置 OAuth 范围：

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.github.com/mcp",
      "oauth": {
        "redirectUri": "127.0.0.1:8080",
        "oauthScopes": ["repo", "user"]
      }
    }
  }
}
```

如果遇到 OAuth 范围相关的错误，您可以在 MCP 服务器配置中配置空数组来绕过范围要求：

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.github.com/mcp",
      "oauth": {
        "redirectUri": "127.0.0.1:8080",
        "oauthScopes": []
      }
    }
  }
}
```

对于需要预注册 OAuth 应用的服务，将 `oauth.clientId` 设置为您的应用 ID：

```json
{
  "mcpServers": {
    "slack": {
      "type": "http",
      "url": "https://mcp.slack.com/mcp",
      "oauth": {
        "clientId": "your-slack-app-client-id",
        "oauthScopes": ["search:read", "channels:read"]
      }
    }
  }
}
```

## Tools 字段

`tools` 字段列出代理可能使用的所有工具。工具包括内置工具和来自 MCP 服务器的工具。

- 内置工具通过其名称指定（例如 `read`, `shell`）
- MCP 服务器工具以 `@` 为前缀，后跟服务器名称（例如 `@git`）
- 要指定 MCP 服务器的特定工具，请使用 `@server_name/tool_name`
- 使用 `*` 作为特殊通配符包含所有可用工具（内置和来自 MCP 服务器）
- 使用 `@builtin` 包含所有内置工具
- 使用 `@server_name` 包含特定 MCP 服务器的所有工具

```json
{
  "tools": [
    "read",
    "write",
    "shell",
    "@git",
    "@rust-analyzer/check_code"
  ]
}
```

要包含所有可用工具，您可以简单地使用：

```json
{
  "tools": ["*"]
}
```


## ToolAliases 字段

`toolAliases` 字段是一个高级功能，允许您重新映射工具名称。这主要用于解决来自不同 MCP 服务器的工具之间的命名冲突，或为特定工具创建更直观的名称。

例如，如果 `@github-mcp` 和 `@gitlab-mcp` 服务器都提供名为 `get_issues` 的工具，您将遇到命名冲突。您可以使用 `toolAliases` 来消除歧义：

```json
{
  "toolAliases": {
    "@github-mcp/get_issues": "github_issues",
    "@gitlab-mcp/get_issues": "gitlab_issues"
  }
}
```

使用此配置，工具将作为 `github_issues` 和 `gitlab_issues` 提供给代理，而不是在 `get_issues` 上发生冲突。

您还可以使用别名为经常使用的工具创建更短或更直观的名称：

```json
{
  "toolAliases": {
    "@aws-cloud-formation/deploy_stack_with_parameters": "deploy_cf",
    "@kubernetes-tools/get_pod_logs_with_namespace": "pod_logs"
  }
}
```

键是原始工具名称（包括 MCP 工具的服务器前缀），值是要使用的新名称。

## AllowedTools 字段

`allowedTools` 字段指定哪些工具可以在不提示用户许可的情况下使用。这是一个安全功能，有助于防止未经授权的工具使用。

```json
{
  "allowedTools": [
    "read",
    "write",
    "@git/git_status",
    "@server/read_*",
    "@fetch"
  ]
}
```

您可以使用几种模式来允许工具：

### 精确匹配

- **内置工具**: `"read"`, `"shell"`, `"knowledge"`
- **特定 MCP 工具**: `"@server_name/tool_name"`（例如 `"@git/git_status"`）
- **MCP 服务器的所有工具**: `"@server_name"`（例如 `"@fetch"`）

### 通配符模式

`allowedTools` 字段支持使用 `*` 和 `?` 的 glob 风格通配符模式：

#### MCP 工具模式

- **工具前缀**: `"@server/read_*"` → 匹配 `@server/read_file`, `@server/read_config`
- **工具后缀**: `"@server/*_get"` → 匹配 `@server/issue_get`, `@server/data_get`
- **服务器模式**: `"@*-mcp/read_*"` → 匹配 `@git-mcp/read_file`, `@db-mcp/read_data`
- **模式服务器的任何工具**: `"@git-*/*"` → 匹配来自 `git-*` 服务器的任何工具

### 示例

```json
{
  "allowedTools": [
    "read",
    "knowledge",
    "@server/specific_tool",
    "r*",
    "w*",
    "@builtin",
    "@server/api_*",
    "@server/read_*",
    "@git-server/get_*_info",
    "@*/status",
    "@fetch",
    "@git-*"
  ]
}
```

### 模式匹配规则

- **`*`** 匹配任何字符序列（包括无）
- **`?`** 精确匹配一个字符
- **精确匹配** 优先于模式
- **服务器级权限**（`@server_name`）允许该服务器的所有工具
- **区分大小写** 匹配

与 `tools` 字段不同，`allowedTools` 字段不支持 `"*"` 通配符来允许所有工具。要允许工具，您必须使用特定模式或服务器级权限。

> **信息**: 当您允许写入工具（如 `write`、`shell` 或具有写入访问权限的 MCP 工具）时，代理可以修改工具有权访问的任何文件 —— 包括 `~/.kiro` 下的所有资源，如技能、引导文件、MCP 配置和其他代理配置。所有安装的技能和资源与代理以相同的权限运行。在启用写入工具之前，请查看安全最佳实践部分。

## ToolsSettings 字段

`toolsSettings` 字段提供特定工具的配置。每个工具可以有自己的独特配置选项。请注意，如果工具也包含在 `allowedTools` 中，则配置可接受模式的规范将被覆盖。

```json
{
  "toolsSettings": {
    "write": {
      "allowedPaths": ["~/**"]
    },
    "shell": {
      "allowedCommands": ["git status", "git fetch"],
      "deniedCommands": ["git commit .*", "git push .*"],
      "autoAllowReadonly": true
    },
    "@git/git_status": {
      "git_user": "$GIT_USER"
    }
  }
}
```

有关工具特定的选项，请参阅[内置工具文档](../reference/3_built-in-tools.md)。



## Resources 字段

`resources` 字段赋予代理访问本地资源的权限。资源可以是文件、技能或知识库。

```json
{
  "resources": [
    "file://README.md",
    "file://.kiro/steering/**/*.md",
    "skill://.kiro/skills/**/SKILL.md"
  ]
}
```

资源通过 URI 方案支持不同类型：

- `file://` — 启动时直接加载到上下文中的文件
- `skill://` — 启动时加载元数据，按需加载完整内容的技能

两者都支持：

- 特定路径: `file://README.md` 或 `skill://my-skill.md`
- Glob 模式: `file://.kiro/**/*.md` 或 `skill://.kiro/skills/**/SKILL.md`
- 绝对或相对路径

### 文件资源

文件资源在代理启动时直接加载到代理的上下文中。将这些用于代理始终需要的内容。

```json
{
  "resources": [
    "file://README.md",
    "file://docs/**/*.md"
  ]
}
```

### 技能资源

技能是渐进式加载的 —— 启动时只加载元数据（名称和描述），当代理确定需要时按需加载完整内容。这使上下文保持精简，同时让代理能够访问大量文档。

技能文件必须以包含 `name` 和 `description` 的 YAML 前言开头：

```markdown
---
name: dynamodb-data-modeling
description: DynamoDB 数据建模最佳实践指南。在设计或分析 DynamoDB 架构时使用。
---

# DynamoDB 数据建模

... 完整内容在这里 ...
```

```json
{
  "resources": [
    "skill://.kiro/skills/**/SKILL.md"
  ]
}
```

编写具体的描述，以便代理能够可靠地确定何时加载完整内容。

### 知识库资源

知识库资源允许代理搜索索引的文档和内容。支持数百万 token 的索引内容和增量加载，代理可以高效地搜索大型文档集。

```json
{
  "resources": [
    {
      "type": "knowledgeBase",
      "source": "file://./docs",
      "name": "ProjectDocs",
      "description": "项目文档和指南",
      "indexType": "best",
      "autoUpdate": true
    }
  ]
}
```

**字段：**

| 字段 | 必需 | 描述 |
|------|------|------|
| `type` | 是 | 必须是 `"knowledgeBase"` |
| `source` | 是 | 索引路径。本地路径使用 `file://` 前缀 |
| `name` | 是 | 知识库的显示名称 |
| `description` | 否 | 内容的简要描述 |
| `indexType` | 否 | 索引策略: `"best"`（默认，质量更高）或 `"fast"`（更快索引） |
| `autoUpdate` | 否 | 代理生成时重新索引。默认: `false` |

**用例：**

- 跨代理共享团队文档
- 为代理提供项目特定的上下文（规范、决策、会议记录）
- 索引大型代码库和文档
- 使用 `autoUpdate: true` 保持代理知识最新



## Hooks 字段

`hooks` 字段定义在代理生命周期和工具执行期间的特定触发点运行的命令。

```json
{
  "hooks": {
    "agentSpawn": [
      {
        "command": "git status"
      }
    ],
    "userPromptSubmit": [
      {
        "command": "ls -la"
      }
    ],
    "preToolUse": [
      {
        "matcher": "execute_bash",
        "command": "{ echo \"$(date) - Bash command:\"; cat; echo; } >> /tmp/bash_audit_log"
      },
      {
        "matcher": "use_aws",
        "command": "{ echo \"$(date) - AWS CLI call:\"; cat; echo; } >> /tmp/aws_audit_log"
      }
    ],
    "postToolUse": [
      {
        "matcher": "fs_write",
        "command": "cargo fmt --all"
      }
    ]
  }
}
```

每个 hook 定义包含：

- `command`（必需）: 要执行的命令
- `matcher`（可选）: 用于 `preToolUse` 和 `postToolUse` hook 匹配工具名称的模式。Hook matcher 使用内部工具名称（`fs_read`、`fs_write`、`execute_bash`、`use_aws`）而不是简化名称。有关可用的工具名称，请参阅[内置工具文档](../reference/3_built-in-tools.md)。

可用的 hook 触发器：

- `agentSpawn`: 代理初始化时触发
- `userPromptSubmit`: 用户提交消息时触发
- `preToolUse`: 工具执行前触发。可以阻止工具使用
- `postToolUse`: 工具执行后触发
- `stop`: 助手完成响应时触发

有关 hook 行为、输入/输出格式和示例的详细信息，请参阅 [Hooks 文档](../hooks.md)。

## includeMcpJson 字段

`includeMcpJson` 字段确定是否包含 MCP 配置文件中定义的 MCP 服务器（全局为 `~/.kiro/settings/mcp.json`，工作区为 `<cwd>/.kiro/settings/mcp.json`）。

```json
{
  "includeMcpJson": true
}
```

设置为 `true` 时，除了代理 `mcpServers` 字段中定义的服务器外，代理还可以访问全局和本地配置中定义的所有 MCP 服务器。

## Model 字段

`model` 字段指定此代理使用的模型 ID。如果未指定，代理将使用默认模型。

```json
{
  "model": "claude-sonnet-4"
}
```

模型 ID 必须与 Kiro CLI 模型服务返回的可用模型之一匹配。您可以在活动聊天会话中使用 `/model` 命令查看可用模型。

如果指定的模型不可用，代理将回退到默认模型并显示警告。

## KeyboardShortcut 字段

`keyboardShortcut` 字段配置一个键盘快捷键，用于在聊天会话期间快速切换到此代理。

```json
{
  "keyboardShortcut": "ctrl+a"
}
```

快捷键由修饰符和键组成，用 `+` 分隔：

**修饰符**（可选）:

- `ctrl` - Control 键
- `shift` - Shift 键

**键**:

- 单个字母: `a-z`（不区分大小写）
- 单个数字: `0-9`

**示例：**

```json
"keyboardShortcut": "ctrl+a"           // Control + A
"keyboardShortcut": "shift+b"          // Shift + B
```

**切换行为：**

当您按下键盘快捷键时：

- 如果您在不同的代理上：切换到此代理
- 如果您已经在此代理上：切换回您之前的代理

**冲突处理：**

如果多个代理具有相同的键盘快捷键，则会记录警告并禁用该快捷键。在这种情况下，使用 `/agent swap` 手动切换。

## WelcomeMessage 字段

`welcomeMessage` 字段指定切换到此代理时显示的消息。

```json
{
  "welcomeMessage": "您今天想构建什么？"
}
```

此消息在代理切换确认后出现，帮助用户了解代理的用途。



## 完整示例

这是一个代理配置文件的完整示例：

```json
{
  "name": "aws-rust-agent",
  "description": "专门用于 AWS 和 Rust 开发任务的代理",
  "mcpServers": {
    "fetch": {
      "command": "fetch3.1",
      "args": []
    },
    "git": {
      "command": "git-mcp",
      "args": []
    }
  },
  "tools": [
    "read",
    "write",
    "shell",
    "aws",
    "@git",
    "@fetch/fetch_url"
  ],
  "toolAliases": {
    "@git/git_status": "status",
    "@fetch/fetch_url": "get"
  },
  "allowedTools": [
    "read",
    "@git/git_status"
  ],
  "toolsSettings": {
    "write": {
      "allowedPaths": ["src/**", "tests/**", "Cargo.toml"]
    },
    "aws": {
      "allowedServices": ["s3", "lambda"]
    }
  },
  "resources": [
    "file://README.md",
    "file://docs/**/*.md"
  ],
  "hooks": {
    "agentSpawn": [
      {
        "command": "git status"
      }
    ],
    "userPromptSubmit": [
      {
        "command": "ls -la"
      }
    ]
  },
  "useLegacyMcpJson": true,
  "model": "claude-sonnet-4",
  "keyboardShortcut": "ctrl+r",
  "welcomeMessage": "准备好帮助您进行 AWS 和 Rust 开发！"
}
```

---

## 文件位置

您可以定义本地代理和全局代理。

### 本地代理（项目特定）

```
.kiro/agents/
```

本地代理特定于当前工作区，仅在从该目录或其子目录运行 Kiro CLI 时可用。

**示例：**

```
my-project/
├── .kiro/
│   └── agents/
│       ├── dev-agent.json
│       └── aws-specialist.json
└── src/
    └── main.py
```

### 全局代理（用户范围）

```
~/.kiro/agents/
```

全局代理可从任何目录使用。

**示例：**

```
~/.kiro/agents/
├── general-assistant.json
├── code-reviewer.json
└── documentation-writer.json
```

### 代理优先级

当 Kiro CLI 查找代理时：

1. **本地优先**: 检查当前目录的 `.kiro/agents/`
2. **全局回退**: 检查 HOME 目录的 `~/.kiro/agents/`

如果两个位置都有同名代理，本地代理优先并显示警告消息。

---

## 最佳实践

1. **从限制开始**: 从最小的工具访问开始，根据需要扩展
2. **清晰命名**: 使用指示代理用途的描述性名称
3. **记录用法**: 添加清晰的描述以帮助团队成员了解代理
4. **版本控制**: 将代理配置存储在项目存储库中
5. **彻底测试**: 在共享之前验证工具权限是否按预期工作

### 本地与全局代理

**本地代理用于：**

- 项目特定的配置
- 需要项目文件/工具的代理
- 具有独特要求的开发环境
- 通过版本控制与团队共享

**全局代理用于：**

- 跨项目的通用代理
- 个人生产力代理
- 没有项目特定上下文的代理
- 常用的工具和工作流程

### 安全

- 仔细审查 `allowedTools`
- 使用特定模式而不是通配符
- 为敏感操作配置 `toolsSettings`
- 首先在安全环境中测试代理

#### 写入工具权限

默认情况下，Kiro CLI 代理只能访问只读工具。除非您在 `allowedTools` 中明确启用或在运行时批准，否则不允许写入操作。

当您启用写入工具（如 `write`、`shell` 或具有写入功能的 MCP 工具）时，代理以与您的用户账户相同的文件系统权限运行。这意味着：

- 代理可以读取和修改 `~/.kiro` 下的所有文件，包括技能上下文文件、引导文件、MCP 服务器配置（`mcp.json`）和其他代理配置
- 所有安装的技能和资源共享相同的权限。各个技能之间没有隔离 —— 当启用写入工具时，代理可以读取或修改任何已加载技能的上下文
- 技能无法自行执行代码。技能是为代理提供指令的文本文件。但是，如果允许 `shell` 等写入工具，代理可以执行任何已加载技能中引用的命令

使用写入工具时降低风险：

- 只启用您需要的特定写入工具（例如 `"write"` 但不是 `"shell"`）
- 使用 `toolsSettings` 通过 `allowedPaths` 将写入操作限制为特定路径
- 在安装之前审查技能和资源，特别是来自不受信任来源的
- 使用 `preToolUse` hook 审计或阻止敏感操作

---

## 下一步

- [创建自定义代理](1_creating.md)
- [内置工具参考](../reference/3_built-in-tools.md)
- [Hooks 文档](../hooks.md)
- [代理示例](3_examples.md)

---

页面更新时间: 2026年5月8日
