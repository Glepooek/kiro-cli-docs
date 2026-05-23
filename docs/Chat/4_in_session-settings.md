# 会话内设置

运行 `/settings` 可以更改 Kiro 的外观和行为 — 颜色、键盘快捷键、多行输入和辅助功能开关 — 无需离开聊天会话。

## 子命令

### `/settings theme`

自定义用于提示输入和代理响应文本的颜色。打开实时预览，您可以在提交更改之前查看效果。

```bash
/settings theme
```

主题系统使用命名的 ANSI 颜色，因此您的自定义设置可以在不同调色板的终端中正确渲染。当终端颜色检测置信度较低时，会回退到安全的 ANSI 颜色。

### `/settings keybindings`

查看当前的键盘快捷键配置。这是一个只读参考，显示所有可配置的绑定及其当前值。

```bash
/settings keybindings
```

要更改键盘绑定，请使用 `kiro-cli settings` 命令:

```bash
kiro-cli settings chat.keybindings.cancelStream "ctrl+x"
kiro-cli settings chat.keybindings.closeMenu "ctrl+["
kiro-cli settings chat.keybindings.quit "ctrl+shift+q"
```

参见[键盘绑定 (终端 UI)](/docs/cli/reference/settings/#key-bindings-terminal-ui) 获取完整参考。

### `/settings terminal`

启用 `Shift+Enter` 和 `Option+Enter` 作为终端中的换行快捷键。如果按 Shift+Enter 当前是提交提示而不是插入换行，此命令会通过自动检测您的终端并应用适当的键盘绑定配置来修复它。

```bash
/settings terminal
```

**需要配置的终端**（由 `/settings terminal` 自动应用）:
- VS Code 集成终端
- Alacritty
- Zed
- Apple Terminal

**原生支持的终端**（无需配置）:
- iTerm2
- Kitty
- Ghostty
- WezTerm
- Warp

对于不支持自动配置的终端，Kiro 会显示手动解决方案说明。

> **注意**
>
> 在修改任何终端配置文件之前会创建 `.bak` 备份。如果出现问题，您可以从备份恢复。

**tmux 用户:** 将以下两行添加到您的 `tmux.conf` 以使 `Shift+Enter` 正确传递:

```bash
set -s extended-keys on
set -as terminal-features 'xterm*:extkeys'
```

### `/settings display`

切换视觉功能以适应辅助功能或个人偏好。

```bash
/settings display
```

可用开关:
- **动画** — 启用或禁用流式动画和旋转图标
- **ASCII 艺术** — 切换问候语和面板中的装饰性 ASCII 艺术
- **图标** — 在图标和纯文本指示器之间切换

## 持久化

通过 `/settings` 所做的所有更改都保存在 `~/.kiro/settings/cli.json` 中，并应用于后续会话。您也可以使用 `kiro-cli settings open` 直接编辑此文件。

## 相关

- [设置参考](/docs/cli/reference/settings/) — 所有 CLI 设置的完整列表
- [终端 UI](/docs/cli/terminal-ui/) — 终端 UI 功能和配置
- [配置](/docs/cli/chat/configuration/) — 配置文件路径和作用域

---

页面更新时间: 2026年5月19日
