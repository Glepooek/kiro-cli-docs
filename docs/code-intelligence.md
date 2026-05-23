# 代码智能

代码智能提供两个互补的代码理解层:

**Tree-sitter (内置)** - 开箱即用的 18 种语言的代码智能。无需安装 LSP 即可使用模糊匹配搜索符号、获取文档符号和查找定义。通过增量加载和支持数百万 token 的索引内容,Agent 可以高效搜索大型代码库。

**LSP 集成 (可选)** - 通过查找引用、转到定义、悬停文档、重命名重构和诊断来增强精度。需要安装语言服务器。

## 支持的语言

Bash, C, C++, C#, Elixir, Go, Java, JavaScript, Kotlin, Lua, PHP, Python, Ruby, Rust, Scala, Swift, TSX, TypeScript

## 内置功能

代码智能提供这些操作(无需 LSP):
- **符号搜索** - 按名称查找函数、类、方法(模糊匹配)
- **文档符号** - 列出文件中的所有符号
- **符号查找** - 按确切名称查找特定符号
- **模式搜索** - 基于 AST 的结构化代码搜索
- **模式重写** - 使用 AST 模式的自动化代码转换
- **代码库概述** - 高级代码库结构概述
- **代码库映射** - 探索目录结构并理解代码组织

启用 LSP 后(可选),额外的操作可用:
- **查找引用** - 定位位置处符号的所有用法
- **转到定义** - 导航到符号定义的位置
- **重命名符号** - 在代码库中重命名符号
- **获取诊断** - 获取文件的错误和警告
- **悬停文档** - 获取位置处的类型信息和文档
- **补全** - 获取位置处的补全建议

## 代码库概述

在几秒钟内获取任何工作区的完整概述:

```bash
/code overview
```

指定路径以专注于特定目录:

```bash
/code overview ./src/components
```

深入包时使用 `--silent` 获得更清晰的输出:

```bash
/code overview --silent
```

适用于:
- 入门新代码库
- 关于项目结构的问答会话
- 快速理解不熟悉的包

## 文档生成

通过交互式会话为您的代码库生成文档:

```bash
/code summary
```

这将启动一个交互式会话,您可以选择输出格式:
- **AGENTS.md** - 为使用您的代码库的 AI Agent 提供文档
- **README.md** - 标准项目文档
- **CONTRIBUTING.md** - 贡献者指南

生成的文档基于对代码库结构、依赖项和代码模式的分析。

## 模式搜索和重写

基于 AST 的结构化代码搜索和转换。通过结构而非仅文本查找和修改代码。

### 元变量

- `$VAR` - 匹配单个节点(标识符、表达式)
- `$$$` - 匹配零个或多个节点(语句、参数)

### 模式搜索示例

```javascript
// 查找所有 console.log 调用
pattern: console.log($ARG)
language: javascript

// 查找所有异步函数
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

### 重写工作流

1. 首先使用 `pattern_search` 验证匹配
2. 查看匹配项以确保正确性
3. 使用 `dry_run: true` 运行 `pattern_rewrite` 进行预览
4. 使用 `dry_run: false` 应用更改

## LSP 集成 (可选)

**LSP 是可选的且工作区范围限定**

内置的 tree-sitter 功能开箱即用,无需初始化。只有在需要增强的 LSP 功能(如查找引用、悬停文档和重命名重构)时,才需要运行 `/code init`。代码智能按工作区配置,而非全局。每个项目独立维护其 LSP 设置。

运行 `/code init` 以解锁完整的 LSP 驱动的代码智能,具有查找引用、悬停文档和重命名重构等增强功能。
