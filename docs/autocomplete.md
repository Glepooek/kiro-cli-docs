# 补全和自动完成

Kiro CLI 提供两个 AI 驱动的辅助功能,帮助您在终端中更高效地工作:
- **自动完成下拉菜单**: 显示可用命令选项的图形菜单
- **行内建议**: 输入时出现的灰色"幽灵文本"

这些功能独立工作,支持数百个流行的命令行工具,包括 `git`、`npm`、`docker` 和 `aws`。

## 自动完成下拉菜单

自动完成下拉菜单在您输入命令时出现在光标右侧,显示可用选项、子命令和参数,您可以使用箭头键选择。

### 使用自动完成

安装 Kiro CLI 后,自动完成下拉菜单会自动启用:
1. 打开终端或命令提示符
2. 开始输入命令
3. 将出现一个图形菜单,显示可用选项
4. 使用箭头键导航建议
5. 按 **Tab** 或 **Enter** 选择选项

### 配置

自定义自动完成行为:

```bash
# 启用/禁用自动完成
kiro-cli settings autocomplete.disable false  # 启用
kiro-cli settings autocomplete.disable true   # 禁用

# 更改主题
kiro-cli theme dark
kiro-cli theme light  
kiro-cli theme system

# 查看当前主题
kiro-cli theme

# 列出可用主题
kiro-cli theme --list
```

## 行内建议

行内建议在您输入时直接在命令行上显示为灰色"幽灵文本"。此功能独立于下拉菜单工作。

### 使用行内建议

行内建议默认启用:
1. 开始输入命令
2. 将出现灰色幽灵文本,显示潜在的补全
3. 按 **右箭头键** 或 **Tab** 接受
4. 继续输入以忽略建议

### 管理行内建议

使用 `kiro-cli inline` 命令控制行内建议:

```bash
# 启用行内建议
kiro-cli inline enable

# 禁用行内建议  
kiro-cli inline disable

# 检查当前状态
kiro-cli inline status

# 设置自定义
kiro-cli inline set-customization [ARN]

# 显示可用的自定义
kiro-cli inline show-customizations
```

## 支持的工具

自动完成系统支持数百个命令行工具:

### 流行工具

- **Git**: 分支名称、提交哈希、文件路径
- **Docker**: 容器名称、镜像标签、命令
- **npm/yarn**: 包名称、脚本、依赖项
- **kubectl**: 资源、命名空间、上下文
- **terraform**: 资源、提供者、变量
- **aws**: 服务、区域、资源名称

### 语言工具

- **Python**: `pip`、`poetry`、`conda`
- **Node.js**: `npm`、`yarn`、`pnpm`
- **Ruby**: `gem`、`bundle`
- **Go**: `go mod`、`go build`

### 系统工具

- 标准 Unix/Linux 命令
- 包管理器(`apt`、`brew`、`yum`)
- 文件操作(`ls`、`find`、`grep`)

## 故障排除

### 自动完成不工作

如果自动完成未出现:
1. 验证安装: `kiro-cli --version`
2. 检查是否禁用: `kiro-cli settings autocomplete.disable`
3. 重启终端
4. 尝试不同的 shell(bash、zsh、fish)

### 行内建议问题

如果行内建议不工作:
1. 检查状态: `kiro-cli inline status`
2. 如果禁用则启用: `kiro-cli inline enable`
3. 验证 shell 兼容性
4. 检查终端模拟器支持
