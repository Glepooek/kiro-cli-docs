# 无头模式

无头模式让您可以将 Kiro CLI 作为 CI/CD 管道的一部分运行,以自动化代码审查、生成测试或排除构建故障 - 无需交互式终端。使用 API 密钥进行身份验证,传递提示,Kiro 会端到端执行它。

## 身份验证

无头模式需要设置为 `KIRO_API_KEY` 环境变量的 API 密钥。如果您还没有创建,请按照生成 API 密钥中的步骤操作。

> API 密钥身份验证仅适用于 Kiro Pro、Pro+ 和 Power 订阅者。如果您的订阅由管理员管理,他们需要先启用 API 密钥生成。

> API 密钥与您的用户账户关联。您的 Kiro 管理员配置的任何治理规则 - 包括 MCP 服务器限制、模型访问策略和 Web 获取权限 - 都适用于无头会话,就像适用于交互式会话一样。

## 运行无头命令

传递 `--no-interactive` 以及您的提示:

```bash
kiro-cli chat --no-interactive "your prompt here"
```

由于没有用户批准工具调用,请使用 `--trust-all-tools` 或 `--trust-tools` 预先授予权限:

```bash
# 信任所有工具
kiro-cli chat --no-interactive --trust-all-tools "为 auth 模块编写测试并运行它们"

# 仅信任特定工具类别
kiro-cli chat --no-interactive --trust-tools=read,grep "在 src/ 中查找所有 TODO 注释"
```

## CI/CD 示例

### GitHub Actions

```yaml
name: Kiro Code Review
on: [pull_request]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install Kiro CLI
        run: curl -fsSL https://cli.kiro.dev/install | bash
      - name: Review PR changes
        env:
          KIRO_API_KEY: ${{ secrets.KIRO_API_KEY }}
        run: kiro-cli chat --no-interactive --trust-tools=read,grep "审查此 PR 中的更改是否有安全问题"
```

### 其他模式

```bash
# 生成并运行测试
kiro-cli chat --no-interactive --trust-all-tools "为 auth 模块编写测试并运行它们"

# 排除失败的构建
cat build-error.log | kiro-cli chat --no-interactive "解释此构建失败并建议修复"
```

使用 `--require-mcp-startup` 在 MCP 服务器无法连接时快速失败 - 对于依赖外部工具的管道很有用。

## 标志参考

| 标志 | 描述 |
|----------------------------------|---------------------------------------------------------------------|
| `--no-interactive` | 在没有交互式会话的情况下运行。需要提示作为参数 |
| `--trust-all-tools` | 自动批准所有工具调用而无需提示 |
| `--trust-tools=<categories>` | 自动批准特定工具类别(例如 `read`、`grep`、`write`) |
| `--require-mcp-startup` | 如果任何 MCP 服务器无法连接,立即失败 |

## 最佳实践

- 将 `KIRO_API_KEY` 存储为 CI/CD 平台中的密钥 - 切勿在管道配置中硬编码或提交到源代码控制。
- 使用带有特定类别的 `--trust-tools` 而不是 `--trust-all-tools` 来遵循最小权限原则。
- 当您的管道依赖 MCP 服务器时添加 `--require-mcp-startup` 以快速失败而不是挂起。
- 将上下文管道传输到您的提示中以获得更丰富的结果 - 例如 `git diff | kiro-cli chat --no-interactive "审查这些更改"`。
- 在管道中检查退出代码以优雅地处理故障。
- 定期轮换 API 密钥,并从 Kiro 门户撤销不再使用的密钥。

## 限制

- 您必须提供初始提示作为参数。
- 无法进行会话中用户输入。
- 交互式斜杠命令(`/model` 选择器、`/agent` 选择器)不可用。
- 终端 UI 功能已禁用。
