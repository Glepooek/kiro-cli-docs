# 指南代理

指南代理为您提供关于 Kiro CLI 的即时、准确答案 — 基于官方文档而非通用模型知识。询问命令、工具、设置或工作流程，它会搜索索引文档来响应与您安装版本匹配的信息。

当您需要关于 Kiro CLI 本身的权威答案时使用指南代理 — 命令如何工作、有哪些设置可用或如何配置功能。您的主代理依赖通用知识；指南代理专门依赖索引的 CLI 文档，因此其答案是最新且特定于版本的。

## 快速入门

切换到指南代理:

```bash
> /guide
```

您会在提示符中看到代理切换指示器，确认您现在正在与指南代理聊天。询问关于 Kiro CLI 的任何问题:

```bash
[guide] > 如何配置 MCP 服务器？
```

> **提示**
>
> 要离开指南代理：按 `Shift+Tab` 返回您之前的代理，使用 `/agent swap <name>` 切换到特定代理，或使用 `/chat new` 重新开始。

## 您可以问什么

指南代理可以访问全面的索引文档，涵盖:
- **斜杠命令**: `/chat`、`/context`、`/agent`、`/model`、`/spawn` 和所有其他会话内命令
- **内置工具**: `read`、`write`、`shell`、`grep`、`glob`、`code`、`web_search` 及其配置选项
- **设置**: 通过 `kiro-cli settings` 可用的每个设置，包括描述和有效值
- **功能**: MCP 集成、hooks、steering、自定义代理、子代理、代码智能、知识管理
- **CLI 命令**: `kiro-cli chat`、`kiro-cli settings`、`kiro-cli doctor` 和其他终端命令
- **创建 Kiro 项目文件**: 根据文档模式在 `.kiro/` 中生成代理、提示和 steering 文件

## 工作原理

指南代理在后台使用 **introspect** 工具。当您提问时，它会:
1. 使用混合搜索（语义 + BM25）搜索索引文档语料库
2. 检索最相关的文档部分
3. 综合基于官方文档的答案

由于答案来自编译时嵌入的文档而非通用模型知识，它们反映了您安装的 Kiro CLI 版本的实际行为。

## 示例

### 询问工具

```bash
[guide] > code 工具做什么？

code 工具提供代码智能能力：

• search_symbols — 按名称查找符号定义
• lookup_symbols — 获取特定符号的详情
• get_document_symbols — 列出文件中的所有符号
• pattern_search — 基于 AST 的结构搜索

它使用 AST 解析实现跨语言的准确代码理解。
```

### 询问设置

```bash
[guide] > 如何更改默认模型？

使用以下命令设置默认模型：

kiro-cli settings chat.defaultModel claude-opus-4.7

或在会话中使用 /model 交互式切换模型。
要将当前会话模型保存为默认：

/model set-current-as-default
```

### 询问工作流程

```bash
[guide] > 子代理如何工作？

子代理是在隔离上下文中并行运行的专门代理。
主代理同时生成最多 4 个子代理进行并行任务执行。
每个代理使用自己的上下文操作，防止主对话膨胀。

使用 Ctrl+G 打开 crew 监控器监控它们。
使用 subagent 工具的 availableAgents 设置配置可以生成哪些代理。
```

### 创建 Kiro 项目文件

```bash
[guide] > 创建一个用于编写测试的代理

✔ 已创建 .kiro/agents/test-writer.yaml

我创建了一个测试编写代理。使用以下命令切换到它：
/agent swap test-writer
```

指南代理可以在 `.kiro/agents/` 中创建代理，在 `.kiro/prompts/` 中创建提示，在 `.kiro/steering/` 中创建 steering 文件。

## 指南代理 vs /help

在终端 UI 中，`/help` 打开可搜索的命令面板（列出所有可用命令的覆盖层）。`/guide` 命令将您切换到一个交互式代理，可以就 Kiro CLI 功能进行对话、回答后续问题并为您创建项目文件。

| | `/help` (终端 UI) | `/guide` |
|---|---|---|
| 界面 | 可搜索的覆盖面板 | 交互式聊天代理 |
| 交互 | 浏览和搜索命令 | 用自然语言提问 |
| 后续问题 | 不支持 | 完整的对话上下文 |
| 创建文件 | 否 | 是（代理、提示、steering） |
| 来源 | 静态命令列表 | 索引文档语料库 |

> **注意**
>
> 在经典界面中，`/help` 仍然启动交互式帮助代理。`/guide` 命令仅在终端 UI 中可用。

## 相关

- [斜杠命令](/docs/cli/reference/slash-commands) - 所有可用的斜杠命令
- [自定义代理](/docs/cli/custom-agents) - 创建您自己的代理
- [设置参考](/docs/cli/reference/settings) - 所有配置选项
- [终端 UI](/docs/cli/terminal-ui) - `/guide` 可用的默认聊天界面

---

页面更新时间: 2026年5月19日
