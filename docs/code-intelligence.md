# Code Intelligence

Code Intelligence 提供两个互补的代码理解层：

**Tree-sitter（内置）** - 开箱即用的 18 种语言代码智能。通过模糊匹配搜索符号、获取文档符号、查找定义，无需安装 LSP。支持增量加载和数百万 token 的索引内容，代理可以高效搜索大型代码库。

**LSP 集成（可选）** - 增强的精确功能，包括查找引用、转到定义、悬停文档、重命名重构和诊断。需要安装语言服务器。

## 支持的语言

Bash, C, C++, C#, Elixir, Go, Java, JavaScript, Kotlin, Lua, PHP, Python, Ruby, Rust, Scala, Swift, TSX, TypeScript

## 内置功能

Code Intelligence 提供以下操作（无需 LSP）：
- **符号搜索** - 按名称查找函数、类、方法（模糊匹配）
- **文档符号** - 列出文件中的所有符号
- **符号查找** - 按精确名称查找特定符号
- **模式搜索** - 基于 AST 的结构化代码搜索
- **模式重写** - 使用 AST 模式的自动化代码转换
- **代码库概览** - 高层代码库结构概览
- **代码库映射** - 探索目录结构，理解代码组织

启用 LSP（可选）后，还可使用：
- **查找引用** - 定位某个位置符号的所有用法
- **转到定义** - 导航到符号定义位置
- **重命名符号** - 在代码库中重命名符号
- **获取诊断** - 获取文件的错误和警告
- **悬停文档** - 获取位置的类型信息和文档
- **补全** - 获取位置的补全建议

## 代码库概览

几秒钟内获取任何工作区的完整概览：

```bash
/code overview
```

指定路径以聚焦特定目录：

```bash
/code overview ./src/components
```

使用 `--silent` 在深入研究包时获得更清晰的输出：

```bash
/code overview --silent
```

适用于：
- 新代码库入职
- 关于项目结构的问答
- 快速理解不熟悉的包

## 文档生成

通过交互式会话为代码库生成文档：

```bash
/code summary
```

这将启动交互式会话，您可以选择输出格式：
- **AGENTS.md** - 用于 AI 代理处理代码库的文档
- **README.md** - 标准项目文档
- **CONTRIBUTING.md** - 贡献者指南

生成的文档基于对代码库结构、依赖项和代码模式的分析。

## 模式搜索与重写

基于 AST 的结构化代码搜索和转换。按结构而非文本查找和修改代码。

### 元变量

- `$VAR` - 匹配单个节点（标识符、表达式）
- `$$$` - 匹配零个或多个节点（语句、参数）

### 模式搜索示例

```javascript
// 查找所有 console.log 调用
pattern: console.log($ARG)
language: javascript

// 查找所有 async 函数
pattern: async function $NAME($$$PARAMS) { $$$ }
language: typescript

// 查找所有 .unwrap() 调用
pattern: $E.unwrap()
language: rust
```

### 模式重写示例

```javascript
// 将 var 转换为 const
pattern: var $N = $V
replacement: const $N = $V
language: javascript

// 现代化 hasOwnProperty
pattern: $O.hasOwnProperty($P)
replacement: Object.hasOwn($O, $P)
language: javascript

// 将 unwrap 转换为 expect
pattern: $E.unwrap()
replacement: $E.expect("unexpected None")
language: rust
```

### 重写工作流程

1. 首先使用 `pattern_search` 验证匹配
2. 检查匹配以确保正确性
3. 使用 `dry_run: true` 运行 `pattern_rewrite` 进行预览
4. 使用 `dry_run: false` 应用更改

## LSP 集成（可选）

**LSP 是可选的且按工作区范围设置**

内置的 tree-sitter 功能开箱即用，无需初始化。只有在需要本节所述的增强 LSP 功能时，才需要运行 `/code init`。代码智能按工作区配置，而非全局。每个项目独立维护其 LSP 设置。

运行 `/code init` 解锁完整的 LSP 驱动代码智能，获得查找引用、悬停文档和重命名重构等增强功能。

### 工作原理

Kiro CLI 在后台生成 LSP 服务器进程，通过 stdio 上的 JSON-RPC 进行通信。初始化工作区时，它会从项目标记（如 `package.json`、`Cargo.toml`）和文件扩展名检测语言，然后启动相应的语言服务器。这些服务器持续分析代码并维护符号、类型和引用的索引。当您进行查询时，Kiro 将您的自然语言转换为 LSP 协议请求，发送到相关服务器，并将响应格式化为可读输出。

### 安装语言服务器

默认包含以下语言的 LSP 配置：C/C++, Go, Java, Kotlin, Python, Ruby, Rust, TypeScript/JavaScript

**TypeScript/JavaScript**

```bash
npm install -g typescript-language-server typescript
```

**Rust**

```bash
rustup component add rust-analyzer
```

**Python**

```bash
pip install pyright
# 或使用 pipx（推荐用于隔离）
pipx install pyright
```

**Go**

```bash
go install golang.org/x/tools/gopls@latest
```

**Java**

```bash
# macOS
brew install jdtls

# Linux - 从 https://download.eclipse.org/jdtls/snapshots/ 下载
# 解压并添加到 PATH
```

**Ruby**

```bash
gem install solargraph
```

**C/C++**

```bash
# macOS
brew install llvm
# 或
brew install clangd

# Linux (Debian/Ubuntu)
sudo apt install clangd

# Linux (Arch)
sudo pacman -S clang
```

**Kotlin**

```bash
brew install kotlin-language-server
```

### 初始化 LSP

在项目根目录运行此斜杠命令：

```
/code init
```

这会创建 `lsp.json` 配置并启动语言服务器。

您将看到：

```
✓ Workspace initialization started

Workspace: /path/to/your/project
Detected Languages: ["python", "rust", "typescript"]
Project Markers: ["Cargo.toml", "package.json"]

Available LSPs:
○ clangd (cpp) - available
○ gopls (go) - not installed
◐ jdtls (java) - initializing...
✓ pyright (python) - initialized (687ms)
✓ rust-analyzer (rust) - initialized (488ms)
○ solargraph (ruby) - not installed
✓ typescript-language-server (typescript) - initialized (214ms)
```

**状态指示器：**
- ✓ - 已初始化并就绪
- ◐ - 正在初始化
- ○ available - 已安装但检测到的语言不需要
- ○ not installed - 系统上未安装

**重启 LSP 服务器：** 如果语言服务器关闭或无响应，使用 `/code init -f`。

**自动初始化：** 首次 `/code init` 后，当工作区存在 `lsp.json` 时，Kiro CLI 会在启动时自动初始化代码智能。

**禁用代码智能：** 从项目根目录删除 `lsp.json` 以禁用。随时可使用 `/code init` 重新启用。

### 使用语言服务器

语言服务器通过自然语言查询提供语义代码智能。您可以搜索符号、导航定义、查找引用、跨文件重命名、获取诊断、查看方法文档以及发现类和对象上可用的 API。

**查找符号：**

```
> Find the UserRepository class

Searching for symbols matching: "UserRepository"
  1. Class UserRepository at src/repositories/user.repository.ts:15:1
```

**查找所有引用：**

```
> Find references of Person class

Finding all references at: auth.ts:42:10
  1. src/auth.ts:42:10 - export function authenticate(...)
  2. src/handlers/login.ts:15:5 - authenticate(credentials)
  3. src/handlers/api.ts:89:12 - await authenticate(token)
```

**转到定义：**

```
> Find the definition of UserService

src/services/user.service.ts:42:1: export class UserService { ...
```

**获取文件符号：**

```
> What symbols are in auth.service.ts?

Getting symbols from: auth.service.ts
  1. Class AuthService at auth.service.ts:12:1
  2. Function login at auth.service.ts:25:3
  3. Function logout at auth.service.ts:45:3
  4. Function validateToken at auth.service.ts:62:3
```

**重命名（预演）：**

```
> Dry run: rename the method "FetchUser" to "fetchUserData"

Dry run: Would rename 12 occurrences in 5 files
```

**获取诊断：**

```
> Get diagnostics for main.ts

  1. Error line 15:10: Cannot find name 'undefined_var'
  2. Warning line 42:5: 'result' is declared but never used
```

**获取悬停文档：**

```
> What's the documentation for the authenticate method in AuthService?

Type: (credentials: Credentials) => Promise<AuthResult>

Documentation: Authenticates a user with the provided credentials.
Returns an AuthResult containing the user token and profile.

@param credentials - User login credentials
@throws AuthenticationError if credentials are invalid
```

**发现可用方法：**

```
> What methods are available on the s3Client instance?

Available completions:
  1. putObject - Function: (params: PutObjectRequest) => Promise<PutObjectOutput>
  2. getObject - Function: (params: GetObjectRequest) => Promise<GetObjectOutput>
  3. deleteObject - Function: (params: DeleteObjectRequest) => Promise<DeleteObjectOutput>
  4. listObjects - Function: (params: ListObjectsRequest) => Promise<ListObjectsOutput>
  5. headObject - Function: (params: HeadObjectRequest) => Promise<HeadObjectOutput>
```

### 自定义语言服务器

通过编辑项目根目录的 `lsp.json` 添加自定义语言服务器：

```json
{ 
  "languages": { 
    "mylang": { 
      "name": "my-language-server", 
      "command": "my-lsp-binary", 
      "args": ["--stdio"], 
      "file_extensions": ["mylang", "ml"], 
      "file_patterns": ["Mylangfile", "mylang.config.*"],
      "project_patterns": ["mylang.config"], 
      "exclude_patterns": ["**/build/**"], 
      "multi_workspace": false, 
      "initialization_options": { "custom": "options" },
      "request_timeout_secs": 60
    } 
  } 
}
```

**字段：**
- **name** - 语言服务器的显示名称
- **command** - 要执行的二进制/命令
- **args** - 命令行参数（通常是 ["--stdio"]）
- **file_extensions** - 此服务器处理的文件扩展名
- **file_patterns** - 与完整文件名匹配的 glob 模式可选列表。用于处理没有标准扩展名的特定文件名的语言服务器，如 `Dockerfile`、`Dockerfile.*` 或 `docker-compose*.yml`。精确匹配优先于 glob，更具体的 glob 优先于更广泛的 glob，无论声明顺序如何
- **project_patterns** - 指示项目根目录的文件（如 package.json）
- **exclude_patterns** - 要从分析中排除的 glob 模式
- **multi_workspace** - 如果 LSP 支持多个工作区文件夹，设置为 true（默认：false）
- **initialization_options** - 初始化期间传递的 LSP 特定配置
- **request_timeout_secs** - LSP 请求的超时秒数。默认为 60

编辑后，重启 Kiro CLI 以加载新配置。

## 斜杠命令

### `/code init`

在当前目录初始化代码智能。

### `/code init -f`

强制重新初始化（重启所有 LSP 服务器）。

### `/code status`

显示工作区状态和 LSP 服务器状态。

### `/code logs`

显示 LSP 日志以进行故障排除。

```bash
/code logs                    # 显示最近 20 条 ERROR 日志
/code logs -l INFO            # 显示 INFO 级别及以上
/code logs -n 50              # 显示最近 50 条
/code logs -l DEBUG -n 100    # 显示最近 100 条 DEBUG+ 日志
/code logs -p ./lsp-logs.json # 导出日志到 JSON 文件
```

**选项：**
- `-l, --level <LEVEL>` - 日志级别过滤器（ERROR, WARN, INFO, DEBUG, TRACE）。默认：ERROR
- `-n, --lines <N>` - 要显示的日志行数。默认：20
- `-p, --path <PATH>` - 导出日志到 JSON 文件

## 支持的 LSP 服务器

| 语言 | 扩展名 | 服务器 | 安装命令 |
|------|--------|--------|----------|
| TypeScript/JavaScript | `.ts`, `.js`, `.tsx`, `.jsx` | `typescript-language-server` | `npm install -g typescript-language-server typescript` |
| Rust | `.rs` | `rust-analyzer` | `rustup component add rust-analyzer` |
| Python | `.py` | `pyright` | `pip install pyright` |
| Go | `.go` | `gopls` | `go install golang.org/x/tools/gopls@latest` |
| Java | `.java` | `jdtls` | `brew install jdtls` (macOS) |
| Ruby | `.rb` | `solargraph` | `gem install solargraph` |
| C/C++ | `.c`, `.cpp`, `.h`, `.hpp` | `clangd` | `brew install llvm` (macOS) 或 `apt install clangd` (Linux) |
| Kotlin | `.kt`, `.kts` | `kotlin-language-server` | `brew install kotlin-language-server` |

## 故障排除

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| Code tool is not enabled for this agent | 代理的工具列表中没有 code 工具 | 在代理的 tools 数组中添加 `"code"`，或使用 `@builtin` 包含所有内置工具，或使用 `@builtin/code` |
| Workspace is still initializing | LSP 服务器正在启动 | 等待并重试。如果服务器崩溃，使用 `/code init -f` 重启 |
| LSP initialization failed | | 检查日志详情：`/code logs -l ERROR` |
| No symbols found | 语言服务器仍在索引或文件有语法错误或符号名称不匹配 | 检查文件错误，尝试更广泛的搜索词 |
| No definition found | 位置未指向符号 | 验证行和列号指向符号名称 |

## 最佳实践

1. **每个项目初始化一次 LSP** - 如果需要查找引用和重命名等增强 LSP 功能，在项目根目录运行 `/code init`。内置 tree-sitter 代码智能无需初始化即可工作
2. **使用精确位置** - 行和列必须指向符号
3. **重命名使用 dry_run** - 应用前预览更改
4. **先检查诊断** - 语法错误可能阻止分析
5. **搜索要具体** - "UserService" > "user"
6. **自然地请求文档** - "login 方法做什么？" 而不是指定坐标
7. **对话式发现 API** - "s3Client 有哪些方法？" 以探索外部库功能

## 限制

1. LSP 功能支持因语言服务器而异 - 并非所有服务器都支持每个操作（例如，某些可能不支持重命名或格式化）
2. 大型代码库初始索引可能较慢

---

页面更新时间: 2026年5月12日