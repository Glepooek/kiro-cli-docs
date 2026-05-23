# 退出代码

Kiro CLI 返回特定的退出代码以指示操作状态。在脚本和 CI/CD 管道中使用这些代码来检测成功、失败和特定的错误条件。

## 退出代码参考

| 代码 | 名称 | 描述 |
|------|------|------|
| 0 | Success | 命令成功完成 |
| 1 | Failure | 一般失败（认证错误、无效参数、操作失败） |
| 3 | MCP Startup Failure | MCP 服务器启动失败（需要 `--require-mcp-startup`） |

## 要求 MCP 服务器

默认情况下，MCP 服务器失败记录为警告，但不会影响退出代码。当 MCP 服务器对您的工作流至关重要时，使用 `--require-mcp-startup` 快速失败：

```bash
kiro-cli chat --require-mcp-startup --no-interactive "运行任务"
```

如果任何配置的 MCP 服务器启动失败，CLI 将立即以代码 3 退出。

**提示**

在 MCP 工具必不可少的 CI/CD 管道中使用 `--require-mcp-startup`。这可以防止任务在没有预期工具的情况下静默完成的静默失败。

## 脚本示例

在自动化中处理不同的退出代码以采取适当的行动：

### Bash 脚本

```bash
#!/bin/bash
kiro-cli chat --require-mcp-startup --no-interactive --trust-all-tools "运行分析"
exit_code=$?

case $exit_code in
    0) echo "成功" ;;
    3) echo "MCP 服务器启动失败"; exit 1 ;;
    *) echo "失败，代码为 $exit_code"; exit $exit_code ;;
esac
```

### CI/CD 管道

```yaml
- name: 运行 Kiro 任务
  run: |
    kiro-cli chat --require-mcp-startup --no-interactive --trust-all-tools "分析代码"
  continue-on-error: false
```

## 钩子退出代码

[钩子](../hooks/index.md)使用单独的一组退出代码来控制工具执行：

| 代码 | 行为 |
|------|------|
| 0 | 钩子成功 |
| 2 | （仅 PreToolUse）阻止工具执行；STDERR 返回给 LLM |
| 其他 | 钩子失败；STDERR 显示为警告 |

## 最佳实践

* **在 CI/CD 中使用 `--require-mcp-startup`**，当您的任务依赖 MCP 工具时
* **添加详细日志**（`-v` 或 `-vv`）在调试退出代码问题时
* **显式检查退出代码**，而不是依赖隐式 shell 行为
* **将 MCP 失败与一般失败分开**，以便向用户提供更好的错误消息
