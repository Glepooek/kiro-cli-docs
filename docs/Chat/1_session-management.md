# 会话管理

Kiro CLI 会在每次对话时自动保存所有聊天会话。会话按目录存储在数据库中，允许您从任意之前的会话恢复、导出到文件或集成自定义存储方案。

## 自动保存

- **自动**: 每次对话都保存到数据库
- **范围**: 按目录（每个项目有自己的会话）
- **存储位置**: 本地数据库（`~/.kiro/`）
- **会话 ID**: 每个会话的 UUID

## 管理会话

### 从命令行

```bash
# 恢复最近的会话
kiro-cli chat --resume

# 交互式选择器
kiro-cli chat --resume-picker

# 通过 ID 恢复特定会话
kiro-cli chat --resume-id <SESSION_ID>

# 列出所有会话
kiro-cli chat --list-sessions

# 删除会话
kiro-cli chat --delete-session <SESSION_ID>
```

### 从聊天中

```bash
# 开始新对话（自动保存当前会话）
/chat new

# 以初始提示开始新对话
/chat new how do I set up a React project

# 恢复会话（交互式）
/chat resume

# 打印当前会话 ID
/session-id

# 保存到文件
/chat save <path>

# 从文件加载
/chat load <path>
```

**退出时的恢复提示**

当您退出会话时，Kiro CLI 会打印会话 ID 以及恢复它的命令（`kiro-cli chat --resume-id <ID>`）。使用 `/session-id` 可在会话中按需打印相同的 ID。

**文件扩展名**

加载会话时 `.json` 扩展名是可选的。

## 通过脚本自定义存储

使用自定义脚本从版本控制、云存储或数据库保存/加载会话。

### 通过脚本保存

```bash
/chat save-via-script <script-path>
```

脚本通过 stdin 接收会话 JSON。

**示例: 保存到 Git Notes**

```bash
#!/bin/bash
COMMIT=$(git rev-parse HEAD)
TEMP=$(mktemp)
cat > "$TEMP"
git notes --ref=kiro/notes add -F "$TEMP" "$COMMIT" --force
rm "$TEMP"
echo "Saved to commit ${COMMIT:0:8}" >&2
```

### 通过脚本加载

```bash
/chat load-via-script <script-path>
```

脚本将会话 JSON 输出到 stdout。

**示例: 从 Git Notes 加载**

```bash
#!/bin/bash
COMMIT=$(git rev-parse HEAD)
git notes --ref=kiro/notes show "$COMMIT"
```

## 会话存储

- **数据库**: 会话按目录自动保存
- **文件**: 通过 `/chat save` 手动导出
- **自定义**: 基于脚本的集成

**会话 ID**: UUID 格式（例如 `f2946a26-3735-4b08-8d05-c928010302d5`）

## 示例

### 恢复上次会话

```bash
kiro-cli chat --resume
```

继续最近的对话。

### 交互式选择会话

```bash
kiro-cli chat --resume-picker
```

显示会话列表供选择。

### 导出到文件

```bash
/chat save backup.json
```

将当前会话导出到文件。

### 版本控制集成

```bash
# 保存到 git notes
/chat save-via-script ./scripts/save-to-git.sh

# 从 git notes 加载
/chat load-via-script ./scripts/load-from-git.sh
```

## 故障排除

### 没有可恢复的会话

- **症状**: "No saved chat sessions"
- **原因**: 当前目录没有会话
- **解决方案**: 会话是按目录存储的。请导航到正确的目录。

### 脚本保存失败

- **症状**: 脚本退出并报错
- **原因**: 脚本返回了非零退出码
- **解决方案**: 手动测试脚本。确保成功时退出码为 0。

### 脚本加载失败

- **症状**: 无法加载会话
- **原因**: 脚本未输出有效的 JSON
- **解决方案**: 测试脚本是否向 stdout 输出有效的会话 JSON。

## 限制

- 会话按目录存储
- 仅自动保存到数据库（非文件）
- 会话 ID 是 UUID（不可读）
- 无云同步（使用脚本进行自定义存储）
- 无法按内容搜索会话

## 技术细节

- **存储**: `~/.kiro/` 中的 SQLite 数据库
- **范围**: 会话按目录路径索引
- **自动保存**: 每次对话后
- **脚本接口**:
  - 保存: 通过 stdin 传入 JSON，成功时退出码为 0
  - 加载: 通过 stdout 输出 JSON，成功时退出码为 0

## 下一步

- 了解[聊天命令](/docs/cli/reference/slash-commands#chat)
- 查看[交互式聊天模式](/docs/cli/chat)
- 查看[上下文管理](/docs/cli/chat/context)
- 使用[回溯功能](/docs/cli/chat/rewind/)在较早的对话轮次分叉对话

---

页面更新时间: 2026年5月19日
