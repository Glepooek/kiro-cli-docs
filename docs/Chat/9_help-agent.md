# 帮助代理

帮助代理是一个内置代理，回答关于 Kiro CLI 功能、命令、工具和配置的问题。与通用 AI 响应不同，答案来自实际的 Kiro CLI 文档。它还可以在您的 `.kiro/` 目录中创建代理、提示和 LSP 配置。

> **注意**
>
> 在终端 UI 中，帮助代理已被[指南代理](/docs/cli/chat/guide)取代。使用 `/guide` 而不是 `/help` 来获得交互式的、基于文档的帮助。终端 UI 中的 `/help` 命令打开可搜索的命令面板，而不是切换到代理。

## 快速入门

切换到帮助代理:

```
> /help
✔ 已切换到代理: kiro_help

[help] > 
```

或直接提问:

```
> /help 如何配置 MCP 服务器？
```

使用 `/help --legacy` 获取经典命令列表。

## 您可以问什么

帮助代理可以访问全面的 Kiro CLI 文档:
- **命令**: 斜杠命令（`/chat`、`/agent`、`/context`）和 CLI 命令（`kiro-cli chat`、`kiro-cli settings`）
- **工具**: 内置工具如 `fs_read`、`code`、`grep`、`glob`
- **设置**: 通过 `kiro-cli settings` 可用的任何设置
- **功能**: Tangent Mode、Hooks、MCP、代码智能、子代理
- **快捷键**: 键盘快捷键及其使用方法

## 创建配置

帮助代理可以在 `.kiro/` 目录中创建和修改文件:

```
[help] > 创建一个用于编写测试的代理

✔ 已创建 .kiro/agents/test-writer.yaml

我创建了一个测试编写代理。使用以下命令切换到它：
/agent swap test-writer
```

它可以创建:
- `.kiro/agents/` 中的**代理**
- `.kiro/prompts/` 中的**提示**
- `.kiro/` 中的 **LSP 配置**

## 示例

以下是一些常见问题及帮助代理的响应方式。

### 询问命令

```
[help] > 如何保存对话？

使用 `/chat save` 保存您当前的对话：

/chat save ~/my-session.json   # 保存到特定路径

保存的对话可以稍后用 /chat load 加载。
```

### 询问工具

```
[help] > code 工具做什么？

code 工具提供代码智能：

• search_symbols - 按名称查找符号定义
• lookup_symbols - 获取特定符号的详情  
• get_document_symbols - 列出文件中的所有符号
• pattern_search - 基于 AST 的结构搜索

它使用 AST 解析实现跨语言的准确代码理解。
```

### 询问配置

```
[help] > 如何启用 tangent mode？

使用以下命令启用 Tangent Mode：

kiro-cli settings chat.enableTangentMode true

或在聊天会话中使用 /tangent 切换它。
```

## 返回您之前的代理

在帮助代理中再次运行 `/help` 可返回您之前的代理:

```
[help] > /help
✔ 已切换到代理: kiro_default
```

或使用 `/agent swap <name>` 切换到特定代理。

## 相关

- [指南代理](/docs/cli/chat/guide) - 终端 UI 中帮助代理的替代品
- [斜杠命令](/docs/cli/reference/slash-commands) - 所有可用的斜杠命令
- [自定义代理](/docs/cli/custom-agents) - 创建您自己的代理
- [设置参考](/docs/cli/reference/settings) - 所有配置选项

---

页面更新时间: 2026年5月15日
