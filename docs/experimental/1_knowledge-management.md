# 知识管理

`/knowledge` 命令为 Kiro CLI 提供持久化知识库功能，允许您存储、搜索和管理跨聊天会话持久化的上下文信息。

## 入门

### 启用知识功能

知识管理是实验性的，使用前必须启用：

```bash
kiro-cli settings chat.enableKnowledge true
```

### 基本用法

启用后，在聊天会话中使用 `/knowledge` 命令：

```bash
/knowledge add --name myproject --path /path/to/project
/knowledge show
```

## 命令

### /knowledge show

显示知识库中的所有条目及详细信息，包括创建日期、项目数量和持久化状态。还显示任何活动的后台索引操作及其进度和预计完成时间。

```bash
/knowledge show
```

### /knowledge add

将文件或目录添加到知识库。系统会递归索引目录中所有支持的文件。

**语法：**

```bash
/knowledge add --name <name> --path <path> [--include pattern] [--exclude pattern] [--index-type Fast|Best]
```

**必需参数：**
- `--name` 或 `-n`：知识条目的描述性名称
- `--path` 或 `-p`：要索引的文件或目录路径

**示例：**

```bash
/knowledge add --name "project-docs" --path /path/to/documentation
/knowledge add -n "config-files" -p /path/to/config.json
/knowledge add --name "fast-search" --path /path/to/logs --index-type Fast
/knowledge add -n "semantic-search" -p /path/to/docs --index-type Best
```

### 索引类型

选择最适合您需求的索引方式：

#### Fast（词法 - bm25）

**优点：**
- ✅ 闪电般快速的索引 - 快速处理文件
- ✅ 即时搜索 - 基于关键字，立即得到结果
- ✅ 低资源使用 - 最少的 CPU 和内存
- ✅ 完美适用于日志、配置和大型代码库

**缺点：**
- ❌ 不够智能 - 需要精确的关键字匹配

#### Best（语义 - all-minilm-l6-v2）

**优点：**
- ✅ 智能搜索 - 理解上下文和含义
- ✅ 自然语言查询 - 用完整句子搜索
- ✅ 找到相关概念 - 即使没有精确关键字
- ✅ 完美适用于文档和研究

**缺点：**
- ❌ 索引较慢 - 需要 AI 模型处理
- ❌ 更高的资源使用 - 更多 CPU 和内存消耗

### 何时使用每种类型

| 使用场景 | 推荐类型 | 原因 |
|---------|---------|------|
| 日志文件、错误消息 | Fast | 快速关键字搜索，大容量 |
| 配置文件 | Fast | 精确的参数/值查找 |
| 大型代码库 | Fast | 快速符号和函数搜索 |
| 文档 | Best | 自然语言理解 |
| 研究论文 | Best | 基于概念的搜索 |
| 混合内容 | Best | 更好的整体搜索体验 |

### 默认行为

如果不指定 `--index-type`，系统使用您配置的默认值：

```bash
# 设置您的首选默认值
kiro-cli settings knowledge.indexType Fast   # 或 Best
```

### 模式过滤

使用 include 和 exclude 模式控制要索引的文件：

```bash
/knowledge add "rust-code" /path/to/project --include "*.rs" --exclude "target/**"
/knowledge add "docs" /path/to/project --include "**/*.md" --include "**/*.txt" --exclude "node_modules/**"
```

**模式示例：**
- `*.rs` - 递归匹配所有 Rust 文件
- `**/*.py` - 递归匹配所有 Python 文件
- `target/**` - target 目录中的所有内容
- `node_modules/**` - node_modules 中的所有内容

### 支持的文件类型

**文本文件：** .txt, .log, .rtf, .tex, .rst

**Markdown：** .md, .markdown, .mdx

**JSON：** .json（作为文本处理以便搜索）

**配置：** .ini, .conf, .cfg, .properties, .env

**数据文件：** .csv, .tsv

**网页格式：** .svg（基于文本）

**代码文件：** .rs, .py, .js, .jsx, .ts, .tsx, .java, .c, .cpp, .h, .hpp, .go, .rb, .php, .swift, .kt, .kts, .cs, .sh, .bash, .zsh, .html, .htm, .xml, .css, .scss, .sass, .less, .sql, .yaml, .yml, .toml

**特殊文件：** Dockerfile, Makefile, LICENSE, CHANGELOG, README

### /knowledge remove

通过名称、路径或上下文 ID 从知识库中删除条目。

```bash
/knowledge remove "project-docs"  # 按名称删除
/knowledge remove /path/to/old/project  # 按路径删除
```

### /knowledge update

用新内容更新现有的知识库条目。

```bash
/knowledge update /path/to/updated/project
```

### /knowledge clear

从知识库中删除所有条目。需要确认且无法撤销。

```bash
/knowledge clear
```

### /knowledge cancel

取消后台操作。

```bash
/knowledge cancel abc12345  # 取消特定操作
/knowledge cancel all       # 取消所有操作
```

## 配置

配置知识库行为：

```bash
# 每个知识库的最大文件数
kiro-cli settings knowledge.maxFiles 10000

# 处理的文本块大小
kiro-cli settings knowledge.chunkSize 1024

# 块之间的重叠
kiro-cli settings knowledge.chunkOverlap 256

# 默认索引类型
kiro-cli settings knowledge.indexType Fast
```

## 特定代理的知识库

每个代理维护自己隔离的知识库，确保知识上下文范围限定在您正在使用的特定代理。

### 文件夹结构

知识库存储在系统的本地数据目录中：
- **macOS**: `~/Library/Application Support/kiro-cli/knowledge_bases/`
- **Linux**: `~/.local/share/kiro-cli/knowledge_bases/`
- **Windows**: `%LOCALAPPDATA%\kiro-cli\knowledge_bases\`

## 工作原理

### 索引过程

1. **模式过滤**：按 include/exclude 模式过滤文件
2. **文件发现**：递归扫描支持的文件类型
3. **内容提取**：从每个文件提取文本
4. **分块**：大文件分割成可搜索的块
5. **后台处理**：异步索引
6. **语义嵌入**：内容处理用于语义搜索

### 搜索能力

知识库使用语义搜索：
- 自然语言查询
- 结果按相关性排序，而不仅仅是关键字
- 即使没有精确的字匹配也能找到相关概念

## 最佳实践

### 组织知识库

- 使用描述性名称："api-documentation" 而不是 "docs"
- 在添加之前将相关文件分组到目录中
- 使用 include/exclude 模式专注于相关文件
- 定期检查和更新过时的上下文

### 有效搜索

- 使用自然语言："如何使用知识工具处理认证错误"
- 要具体："数据库连接配置"
- 如果初始搜索不起作用，尝试不同的措辞
- 提示 Kiro 使用工具："使用你的知识库查找数据库连接配置"

## 限制

### 文件类型支持

- 二进制文件在索引时被忽略
- 非常大的文件可能被分块，可能分割相关内容

### 性能考虑

- 大目录可能需要大量时间来索引
- 后台操作受并发处理限制
- 搜索性能因知识库大小而异

## 故障排除

### 文件未被索引

1. **检查模式**：确保 include 模式匹配您的文件
2. **验证排除模式**：确保它们没有过滤掉所需的文件
3. **检查文件类型**：确保文件有支持的扩展名
4. **监控进度**：使用 `/knowledge show` 检查索引状态

### 搜索找不到预期结果

1. **等待索引完成**：使用 `/knowledge show` 确保完成
2. **尝试不同的查询**：使用各种措辞和关键字
3. **验证内容**：用 `/knowledge show` 确认内容已添加

## 下一步

- [上下文管理](../chat/context.md)
- [自定义代理](../custom-agents.md)
- [实验性功能](./0_experimental.md)
