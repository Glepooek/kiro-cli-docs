# 内置工具

Kiro CLI 包含一组内置工具，通过 AI 驱动的常见开发任务助手增强您的终端体验。

## 文件读取

**工具名称**：`read`

**别名**：`fs_read`、`fsRead`

**描述**：读取文件、文件夹和图像

```bash
> 我的应用程序有哪些依赖项

Reading file: src/snake/package.json, all lines (using tool: read)
 ✓ Successfully read 1417 bytes from src/snake/package.json
 - Completed in 0.86s

> 您的应用程序有：

运行时依赖项：
- typescript (^3.5.1) - TypeScript 编译器
- gh-pages (^2.0.1) - GitHub Pages 部署

开发依赖项：
- sass (^1.20.3) - CSS 预处理
- eslint (^5.16.0) + TypeScript 插件 - 代码检查
- jest (^29.5.0) + ts-jest - 测试框架

没有外部游戏库或框架。
```

您还可以在代理配置中配置 `read` 工具，使其可以访问项目中的特定文件和文件夹，从而对 Kiro 可以读取的内容进行细粒度控制。

```json
{
  "toolsSettings": {
    "read": {
      "allowedPaths": ["~/projects", "./src/**"],
      "deniedPaths": ["d1/denied/path/", "d2/denied/path/**/file.txt"]
    }
  }
}
```

### 配置选项

| 选项 | 类型 | 必需 | 描述 |
|------|------|------|------|
| `allowedPaths` | 路径数组 | 否 | 可以无需提示即可读取的路径 |
| `deniedPaths` | 路径数组 | 否 | 被拒绝的路径 |

路径值可以是类似于 gitignore 行为的 glob 模式。例如，`~/temp` 将匹配 `~/temp/child` 和 `~/temp/child/grandchild`

## Glob

**工具名称**：`glob`

**描述**：使用 glob 模式快速发现文件。遵守 `.gitignore`。优先于 bash 中的 `find` 命令使用。

```bash
> 在我的项目中查找所有 TypeScript 测试文件

Finding files matching pattern: **/*.test.ts (using tool: glob)

 ✓ Found 8 files
 - Completed in 0.12s

> 以下是 TypeScript 测试文件：

src/utils/helpers.test.ts
src/utils/validators.test.ts
src/components/button.test.ts
src/components/header.test.ts
src/services/auth.test.ts
src/services/api.test.ts
src/hooks/useAuth.test.ts
src/hooks/useForm.test.ts
```

您可以在代理配置中配置 `glob` 工具以控制可以搜索哪些路径。

```json
{
  "toolsSettings": {
    "glob": {
      "allowedPaths": ["~/projects", "./src/**"],
      "deniedPaths": ["/etc", "/var"],
      "allowReadOnly": true
    }
  }
}
```

### 配置选项

| 选项 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| `allowedPaths` | 字符串数组 | `[]` | 可以无需提示即可搜索的路径列表。支持 glob 模式 |
| `deniedPaths` | 字符串数组 | `[]` | 被拒绝的路径列表。拒绝规则在允许规则之前评估 |
| `allowReadOnly` | 布尔值 | `false` | 是否允许在任何地方搜索而无需提示 |

## Grep

**工具名称**：`grep`

**描述**：使用正则表达式快速搜索内容。遵守 `.gitignore`。使用此工具而不是 bash 中的 `grep`、`rg` 或 `ag` 命令。

```bash
> 在我的项目中查找所有 TODO 注释

Searching for pattern: TODO (using tool: grep)

 ✓ Found 12 matches across 5 files
 - Completed in 0.34s

> 以下是我找到的 TODO 注释：

src/utils/helpers.ts:23
  // TODO: Add error handling for edge cases

src/components/header.tsx:45
  {/* TODO: Implement mobile navigation */}

src/api/routes.ts:12
  // TODO: Add rate limiting
  // TODO: Implement caching

src/services/auth.ts:78
  // TODO: Add refresh token logic

...
```

您可以在代理配置中配置 `grep` 工具以控制可以搜索哪些路径。

```json
{
  "toolsSettings": {
    "grep": {
      "allowedPaths": ["~/projects", "./src/**"],
      "deniedPaths": ["/etc", "/var"],
      "allowReadOnly": true
    }
  }
}
```

### 配置选项

| 选项 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| `allowedPaths` | 字符串数组 | `[]` | 可以无需提示即可搜索的路径列表。支持 glob 模式 |
| `deniedPaths` | 字符串数组 | `[]` | 被拒绝的路径列表。拒绝规则在允许规则之前评估 |
| `allowReadOnly` | 布尔值 | `false` | 是否允许在任何地方搜索而无需提示 |

## 文件写入

**工具名称**：`write`

**别名**：`fs_write`、`fsWrite`

**描述**：用于创建和编辑文件的工具

```bash
> 收集编写 TypeScript 代码的最佳实践，并将其作为 codingpractices.md 保存到 kiro steering 文件夹

I'll create the following file: src/snake/.kiro/steering/codingpractices.md (using tool: write)
Purpose: Create TypeScript coding best practices document

+     1: # TypeScript Coding Best Practices
+     2: 
+     3: ## Type Safety
+     4: - Enable strict mode in `tsconfig.json` (`strict: true`)
+     5: - Avoid `any` type - use `unknown` for truly dynamic types
+     6: - Use explicit return types for functions
+     7: - Leverage type inference where it improves readability
+     8: - Use union types instead of enums when possible
+     9: - Prefer `interface` for object shapes, `type` for unions/intersections
+    10: 
+    11: ## Code Organization
+    12: - One class/interface per file (exceptions for small related types)
+    13: - Group related functionality into modules
+    14: - Use barrel exports (`index.ts`) for clean imports
+    15: - Keep functions small and focused (single responsibility)
+    16: - Separate business logic from presentation logic
```

您还可以在代理配置中配置 `write` 工具，使其可以访问项目中的特定文件和文件夹，从而对 Kiro 可以写入的内容进行细粒度控制。

```json
{
  "toolsSettings": {
    "write": {
      "allowedPaths": ["~/projects/output.txt", "./src/**"],
      "deniedPaths": ["/d1/denied/path/", "/d2/denied/path/**/file.txt"]
    }
  }
}
```

### 自定义差异工具

默认情况下，write 工具使用内置的内联差异显示代码更改。您可以配置外部差异工具来查看更改。

### 配置选项

| 选项 | 类型 | 必需 | 描述 |
|------|------|------|------|
| `allowedPaths` | 路径数组 | 否 | 可以无需提示即可写入的路径 |
| `deniedPaths` | 路径数组 | 否 | 被拒绝的路径 |

路径值可以是类似于 gitignore 行为的 glob 模式。例如，`~/temp` 将匹配 `~/temp/child` 和 `~/temp/child/grandchild`

## 执行 Shell 命令

**工具名称**：`shell`

**别名**：`execute_bash`、`execute_cmd`

**描述**：用于执行指定 bash 命令的工具。

您还可以在代理配置中配置 `shell` 工具以控制 Kiro 可以执行哪些命令。

```json
{
  "toolsSettings": {
    "shell": {
      "allowedCommands": ["git status", "git fetch"],
      "deniedCommands": ["git commit .*", "git push .*"],
      "autoAllowReadonly": true
    }
  }
}
```

### 配置选项

| 选项 | 类型 | 默认值 | 描述 |
|------|------|--------|------|
| allowedCommands | 字符串数组 | [] | 无需提示即可允许的命令列表 |
| deniedCommands | 字符串数组 | [] | 被拒绝的命令列表。拒绝规则在允许规则之前评估 |
| autoAllowReadonly | 布尔值 | false | 启用时，只读命令无需提示即可允许。此参数不限制写操作。 |
| denyByDefault | 布尔值 | false | 为 true 时，拒绝 allowedCommands 之外且未被 `autoAllowReadonly` 自动批准的任何命令，而不是提示批准 |

**注意**：`allowedCommands` 和 `deniedCommands` 支持正则表达式格式。输入的正则表达式用 \A 和 \z 锚定，不支持 look-around，包括 look-ahead 和 look-behind。

### 包装脚本的侧通道

当代理运行 shell 命令时，Kiro CLI 导出两个环境变量，指向每次调用的 FIFO。包装脚本可以向它们写入以独立于 stdout 路由输出：

| 变量 | 在工具结果中捕获？ | 流式传输到 TUI？ | 用途 |
|------|------------------|-----------------|------|
| `AGENT_DISPLAY_OUT` | 否 | 是 | 面向用户的输出，不应该充斥代理的上下文，比如冗长的构建日志，其摘要已经在 stdout 上 |
| `AGENT_CONTEXT_OUT` | 是（作为 `agent_notes`） | 是 | 代理需要看到但通过 stdout 无法可靠到达的消息，例如当用户通过 `grep` 或 `tail` 管道 stdout 时 |

写入 `$AGENT_CONTEXT_OUT` 的行出现在工具结果的 `agent_notes` 字段中，因此代理可以与 stdout 和 stderr 一起读取它们。写入 `$AGENT_DISPLAY_OUT` 的行仅到达 TUI。

包装器示例：

```bash
#!/usr/bin/env bash
# 运行冗长的构建，向用户显示日志，并告诉代理它最终在哪里。
./gradlew build 2>&1 | tee "$AGENT_DISPLAY_OUT"
echo "Build finished. Full log at ./build/reports/build.log" > "$AGENT_CONTEXT_OUT"
echo "ok"  # 纯 stdout — 正常捕获
```

这两个变量仅在代理驱动命令时设置；如果变量为空，包装器应该优雅地回退（例如，用 `[ -n "${AGENT_CONTEXT_OUT:-}" ]` 测试）。

## 执行 AWS 命令

**工具名称**：`aws`

**别名**：`use_aws`

**描述**：用于使用指定的服务、操作和参数进行 AWS CLI 调用的工具

```bash
> 列出我的 s3 存储桶

Running aws cli command (using tool: aws):

Service name: s3api
Operation name: list-buckets
Parameters: 
Region: us-east-1
Label: List all S3 buckets
```

您还可以在代理配置中配置 `aws` 工具以仅执行特定的 AWS 操作。

```json
{
  "toolsSettings": {
    "aws": {
      "allowedServices": ["s3", "lambda", "ec2"],
      "deniedServices": ["eks", "rds"],
      "autoAllowReadonly": true
    }
  }
}
```

## Web 搜索和获取

Web 访问功能使 Kiro 代理能够实时访问互联网上的当前信息。此功能使您能够获取有关自模型训练数据创建以来可能已更改的主题的最新答案。

| 工具 | 描述 |
|------|------|
| `web_search` | 用于搜索网络的工具 |
| `web_fetch` | 用于从 URL 获取内容的工具 |

**重要提示**

您对使用包含网络搜索或基础信息的输出负责。当您的输出包含来自引用或源材料链接的基础信息时，您将知道。如果您向下游用户显示基础输出，必须在输出中保留并显示这些引用和链接。如果您不希望 Kiro 使用这些工具，请创建一个[排除](../custom-agents/configuration-reference.md#tools-field)这些工具的自定义代理。为包含网络搜索或基础信息的输出提供引用。您可以按照提供的引用访问源页面。

```bash
# 示例 1
> EC2 实例的最新情况是什么？

> 我将搜索有关 EC2 实例的最新信息。
Searching the web for: AWS EC2 instances latest 2025 (using tool: web_search)

 ✓ Found 10 search results
 - Completed in 2.12s

> 以下是我找到的内容：

最近的 EC2 实例发布（2025）：

新实例系列：
- **M8a 实例** - 采用第 5 代 AMD EPYC（Turin）处理器的通用实例，运行频率为 4.5GHz。与 M7a 实例相比，性能提高高达 30%，性价比提高 19%。

...

# 示例 2
> t3 medium 实例定价的最新情况如何。

Searching the web for: AWS EC2 t3.medium instance pricing 2025 (using tool: web_search)

 ✓ Found 10 search results
 - Completed in 1.596s

Fetching content from: https://aws.amazon.com/ec2/pricing/on-demand/ (searching for: t3.medium price cost) [mode: selective] (using tool: web_fetch)

 ✓ Fetched 11395 bytes (selective) from URL
 - Completed in 0.718s

> 页面在摘录中没有显示具体定价。让我获取实际定价信息：
Fetching content from: https://instances.vantage.sh/aws/ec2/t3.medium (searching for: t3.medium $0.04) [mode: selective] (using tool: web_fetch)

 ✓ Fetched 3971 bytes (selective) from URL
 - Completed in 0.780s

> 以下是我找到的内容：

AWS EC2 t3.medium 实例定价（2025）：

...
```

为了优化从 URL 检索内容并更好地管理上下文窗口，`web_fetch` 工具有"Selective"和"Truncated"模式。

### 获取模式

| 模式 | 行为 | 用例 |
|------|------|------|
| `selective`（默认） | 返回搜索词匹配前后各 10 个句子；无匹配时返回 20 个句子 | 针对性提取 |
| `truncated` | 前 8000 个字符 | 快速预览 |
| `full` | 完整内容（最多 10MB） | 全面分析 |

### 配置

使用 `toolsSettings` 配置基于 URL 的权限：

```json
{
  "toolsSettings": {
    "web_fetch": {
      "trusted": [".*docs\\.aws\\.amazon\\.com.*", ".*github\\.com.*"],
      "blocked": [".*pastebin\\.com.*"]
    }
  }
}
```

| 选项 | 类型 | 描述 |
|------|------|------|
| `trusted` | 正则数组 | 自动允许无需提示的 URL 模式 |
| `blocked` | 正则数组 | 拒绝的 URL 模式（优先于 trusted） |

**模式行为：**
* 模式是正则表达式，自动用 `^` 和 `$` 锚定
* `blocked` 优先于 `trusted`
* `blocked` 中的无效正则拒绝所有 URL（故障安全）
* `trusted` 中的无效正则被跳过

### 限制

* **大小**：每页最多 10MB
* **超时**：每次请求 30 秒
* **重定向**：最多跟随 10 个重定向
* **内容类型**：仅支持 text/html 页面
* **重试**：失败时自动重试 3 次

### 故障排除

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| 获取失败 | 页面 >10MB、超时、重定向过多或二进制内容 | 尝试不同的 URL 或检查页面可访问性 |
| 空内容 | 搜索词不匹配页面内容 | 使用不同的搜索词或 `truncated`/`full` 模式 |
| URL 被阻止 | URL 匹配 `blocked` 模式 | 从 `toolsSettings` 中移除模式 |
| 工具需要批准 | URL 不在 `trusted` 模式中，且 `web_fetch` 不在 `allowedTools` 中 | 将 URL 模式添加到 `trusted` 或将 `web_fetch` 添加到 `allowedTools` |
| `web_search` 和 `web_fetch` 不可用 | 企业管理员禁用了 Web 工具 | 联系您的管理员启用 Web 工具 |

## 内省 Kiro CLI 功能

**工具名称**：`introspect`

**描述**：通过使用官方文档回答有关其功能、命令和功能的问题，为 Kiro CLI 提供自我意识。

当您向 Kiro CLI 询问有关自身的问题时，内省工具会自动激活。它搜索内置文档以提供有关命令、设置、工具和功能的准确答案。

```bash
> 如何保存对话？

Introspecting to get you the right information (using tool: introspect) - Completed in 0.68s

> 您可以使用 `/chat save <PATH>` 保存对话：

- `/chat save ~/conversation.json` - 保存到特定路径

使用 `/chat load <PATH>` 稍后加载已保存的对话。
```

### 工作原理

默认情况下，内省使用语义搜索：
1. 下载嵌入模型
2. 使用语义搜索查找相关文档
3. 直接返回匹配的文档

对于可能阻止模型下载的企业环境，启用渐进模式：

```bash
kiro-cli settings set introspect.progressiveMode true
```

渐进模式跳过模型下载，改为返回文档索引。然后 LLM 根据需要获取特定文档。

**注意**

渐进模式可能需要多次内省调用才能检索所有相关信息，这可能比语义搜索慢。

### 它提供什么

* **命令帮助**：所有斜杠命令的实时文档（`/chat`、`/context`、`/agent` 等）
* **功能指南**：有关 MCP、钩子、引导和自定义代理等功能的信息
* **设置参考**：所有配置选项及其更改方法
* **工具文档**：有关内置工具及其配置的详细信息

### 示例问题

```bash
> Kiro CLI 有哪些实验性功能？
> Kiro CLI 可以读写文件吗？
> 如何配置 MCP 服务器？
> 有哪些设置可用？
```

### 配置

为内省问题启用自动[切线模式](../experimental/tangent-mode.md)，以将帮助对话与您的主要工作分开：

```bash
kiro-cli settings set introspect.tangentMode true
```

**注意**

在询问有关 Kiro CLI 的问题时要明确 — 问 "Kiro CLI 如何处理文件？" 而不是 "你如何处理文件？" 以获得更准确的响应。尽管有保护措施，AI 可能偶尔提供不准确的信息，因此始终使用响应中提供的文档链接验证重要详细信息。

## 代码智能

**工具名称**：`code`

**描述**：提供代码智能功能，包括符号搜索、LSP 集成和基于模式的代码搜索和重写。

```bash
> 查找 UserRepository 类

Searching for symbols matching: "UserRepository" (using tool: code)

 ✓ Found 1 match
 - Completed in 0.45s

> Found:
  Class UserRepository at src/repositories/user.repository.ts:15:1
```

此工具没有配置选项。

### 权限

在当前工作区内进行的符号查找和代码编辑无需提示即可运行。任何针对**工作区外**文件的操作都需要您先批准，这可以防止意外读取或写入凭据、系统配置或磁盘上的其他项目。

## 工具搜索

**工具名称**：`tool_search`

**描述**：按需查找和加载 MCP 工具，而不是随每个请求发送每个工具定义。

工具搜索自动允许无需用户权限提示（只读操作）。有关启用、设置和工作原理，请参阅[工具搜索](../mcp/tool-search.md)。

### 参数

`tool_search` 工具接受以下参数：

| 参数 | 类型 | 必需 | 描述 |
|------|------|------|------|
| `tool_id` | 字符串 | `tool_id` 或 `query` 之一 | `server_name::tool_name` 格式的确切工具标识符 |
| `query` | 字符串 | `tool_id` 或 `query` 之一 | 用于搜索匹配工具的关键字 |
| `max_results` | 整数 | 否 | 返回的最大结果数（默认：5） |

提供 `tool_id` 或 `query` 中的一个，不能同时提供两者。匹配的工具立即激活并可供代理调用。

## 委派任务

**工具名称**：`delegate`

**描述**：将任务委派给异步运行的后台代理。用于不需要立即结果的长运行任务。

```bash
> 分析所有 TypeScript 文件以查找潜在错误并创建报告

Delegating task to background agent (using tool: delegate)

 ✓ Task delegated successfully
 - Agent ID: agent-abc123
 - Check status with /delegate status

> 我已经在后台开始分析您的 TypeScript文件。
  使用 /delegate status 检查进度。
```

此工具没有配置选项。

## 提交问题或功能请求

**工具名称**：`report`

**描述**：打开浏览器到预填充的 GitHub 问题模板，以报告聊天问题、错误或功能请求。

此工具没有配置选项。

## 知识工具（实验性）

**工具名称**：`knowledge`

**描述**：在聊天会话之间的知识库中存储和检索信息。为文件、目录和文本内容提供语义搜索功能。

此工具没有配置选项。

## 思考工具（实验性）

**工具名称**：`thinking`

**描述**：一种内部推理机制，通过将复杂任务分解为原子操作来提高任务质量。

此工具没有配置选项。

## 待办事项列表工具（实验性）

**工具名称**：`todo`

**描述**：创建和管理待办事项列表以跟踪多步骤任务。

此工具没有配置选项。

## 会话设置工具

**工具名称**：`session`

**描述**：为当前会话临时覆盖 CLI 设置，而无需修改配置文件。所有会话覆盖都存储在内存中，并在会话结束时重置。

您可以以对话方式要求代理调整设置：
* "为此会话禁用 markdown 渲染"
* "切换到紧凑 UI 模式"
* "启用思考工具"

代理可以执行四个操作：

| 操作 | 描述 |
|------|------|
| `list` | 显示所有具有非默认值的设置 |
| `get` | 读取特定设置的当前值 |
| `set` | 为当前会话覆盖设置 |
| `reset` | 重置单个设置或所有会话覆盖 |

只有标记为会话安全的设置可以通过此工具修改。`set` 和 `reset` 操作在应用之前始终需要您的确认。

此工具没有配置选项。

## 子代理工具

**工具名称**：`subagent`

**别名**：`use_subagent`

**描述**：将复杂任务委派给在隔离上下文中并行运行的专业子代理。用于将多步骤任务分解为并行子任务、防止上下文窗口膨胀、同时运行独立研究或委派给不同的代理配置。

**自定义代理配置**

此工具包含在默认代理中。对于自定义代理，您需要显式将 `subagent` 添加到您的 `tools` 数组或通过 `@builtin` 标志包含它。

**功能：**
* 同时生成最多 4 个子代理以进行并行任务执行
* 每个子代理使用自己的隔离上下文操作，以防止主对话膨胀
* 实时可视化指示器显示所有运行子代理的状态
* 支持每个子代理使用不同的代理配置
* 自动执行摘要，包含工具使用和持续时间指标

### 配置

`subagent` 工具支持以下 `toolsSettings`：

| 设置 | 类型 | 描述 |
|------|------|------|
| `availableAgents` | `string[]` | 限制哪些代理可以作为子代理生成。支持 glob 模式（例如 `docs-*`）。 |
| `trustedAgents` | `string[]` | 允许特定代理无需权限提示即可运行。支持 glob 模式。 |

```json
{
  "toolsSettings": {
    "subagent": {
      "availableAgents": ["reviewer", "tester", "analyzer", "docs-*"],
      "trustedAgents": ["reviewer", "tester"]
    }
  }
}
```

子代理还可以使用不同的代理配置：
* **默认子代理**：使用内置默认代理配置
* **自定义子代理**：委派任务时可以按名称引用自定义代理配置

子代理从其分配使用的代理配置继承其工具访问、权限和行为。

### 示例工作流

```bash
> 研究前 3 个 JavaScript 框架并比较它们的性能

# 主代理生成 3 个子代理：
# - 子代理 1：研究 React 性能指标
# - 子代理 2：研究 Vue.js 性能指标
# - 子代理 3：研究 Angular 性能指标

# 每个子代理：
# - 进行独立研究
# - 收集相关数据
# - 使用摘要工具报告发现

# 主代理接收所有摘要并综合比较
```

## 在代理配置中使用工具设置

工具设置在代理配置文件的 `toolsSettings` 部分中指定。每个工具的设置使用工具名称作为键指定。

对于 MCP 服务器工具，使用格式 `@server_name/tool_name` 作为键：

```json
{
  "toolsSettings": {
    "write": {
      "allowedPaths": ["~/projects"]
    },
    "@git/git_status": {
      "git_user": "$GIT_USER"
    }
  }
}
```

## 工具权限

工具可以在代理配置的 `allowedTools` 部分中显式允许：

```json
{
  "allowedTools": [
    "read",
    "knowledge",
    "@git/git_status"
  ]
}
```

如果工具不在 `allowedTools` 列表中，则在工具使用时将提示用户许可，除非设置了允许的 `toolSettings` 配置。

某些工具具有默认权限行为：
* `report` 默认受信任
* `read`、`grep` 和 `glob` 在当前工作目录中受信任
* `shell`、`write` 和 `aws` 默认提示许可，但可以配置为允许特定命令/路径/服务

## 下一步

* **[自定义代理](../custom-agents/index.md)** - 在自定义代理中使用工具
* **[MCP 集成](../mcp/index.md)** - 通过 MCP 连接外部工具
* **[设置](./5_settings.md)** - 配置工具偏好
* **[故障排除](../custom-agents/troubleshooting.md)** - 常见工具问题
