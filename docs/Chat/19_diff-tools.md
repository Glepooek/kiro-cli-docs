# 自定义差异工具

当 Kiro 提出文件更改时，它使用内置差异工具显示它们。如果您喜欢不同的差异体验 — 无论是语法高亮、并排视图还是您喜欢的 GUI 工具 — 您可以配置 Kiro 使用外部差异工具。

## 配置

使用 `chat.diffTool` 设置配置您首选的差异工具:

```bash
kiro-cli settings chat.diffTool <tool-name>
```

例如，使用 delta:

```bash
kiro-cli settings chat.diffTool delta
```

要重置为内置差异:

```bash
kiro-cli settings -d chat.diffTool
```

## 终端工具

这些工具直接在终端中显示差异，让您保持在工作流程中:

| 工具 | 配置值 | 最适合 |
|------|--------|--------|
| [delta](https://github.com/dandavison/delta) | `delta` | 想要语法高亮和行号的 Git 用户 |
| [difftastic](https://github.com/Wilfred/difftastic) | `difft` | 忽略格式的语言感知结构差异 |
| [icdiff](https://github.com/jeffkaufman/icdiff) | `icdiff` | 快速并排彩色比较 |
| [diff-so-fancy](https://github.com/so-fancy/diff-so-fancy) | `diff-so-fancy` | 干净、人类可读的输出 |
| [colordiff](https://www.colordiff.org/) | `colordiff` | 简单的彩色差异 |
| [diff-highlight](https://github.com/git/git/tree/master/contrib/diff-highlight) | `diff-highlight` | 单词级别高亮（随 Git 提供） |
| [ydiff](https://github.com/ymattw/ydiff) | `ydiff` | 带单词级别高亮的并排视图 |
| [bat](https://github.com/sharkdp/bat) | `bat` | 带 Git 集成的语法高亮 |

## GUI 工具

这些工具打开单独的窗口来审查更改:

| 工具 | 配置值 |
|------|--------|
| VS Code | `code` |
| VSCodium | `codium` |
| Meld | `meld` |
| KDiff3 | `kdiff3` |
| FileMerge (macOS) | `opendiff` |
| Vim | `vimdiff` 或 `vim` |
| Neovim | `nvim` |

> **警告**
>
> GUI 差异工具仅为查看打开临时文件。您在 GUI 工具中所做的任何编辑都不会保存或应用于 Kiro 提议的更改。

## 自定义参数

您可以通过在引号中包含参数来自定义工具行为:

```bash
# 在 delta 中启用并排视图
kiro-cli settings chat.diffTool "delta --side-by-side"
```

## 其他工具

Kiro 可以使用上面未列出的差异工具。当您配置工具时，Kiro 尝试两种方法:
1. 通过 stdin 将统一差异管道传输到工具
2. 使用两个临时文件路径作为参数调用工具

如果两种方法都不起作用，Kiro 会回退到内置内联差异。

## 故障排除

如果您看到错误 "Couldn't find the diff tool"，则该工具未安装或不在您的 PATH 中。验证工具是否可访问:

```bash
which delta
```

如果没有返回任何内容，请先安装工具。例如，安装 delta:

```bash
# macOS
brew install git-delta

# Ubuntu/Debian
sudo apt install git-delta
```

对于其他工具，请查看工具的文档获取安装说明。

---

页面更新时间: 2026年1月15日
