# URL 管理

## URL 文件体系

### 文件列表

项目采用分类拆分的 URL 管理方式，所有 URL 文件统一放置在 `url-list/` 目录下：

- `url-list/url_list_chat.txt` - Chat 功能文档（19 个 URL）
- `url-list/url_list_custom-agents.txt` - 自定义代理文档（5 个 URL）
- `url-list/url_list_mcp.txt` - MCP 配置文档（6 个 URL）
- `url-list/url_list_experimental.txt` - 实验性功能文档（7 个 URL）
- `url-list/url_list_reference.txt` - 参考文档（5 个 URL）
- `url-list/url_list_others.txt` - 其他文档（7 个 URL）
- `url-list/url_list.txt` - 总索引（保留作为参考）

### 设计优势

1. **执行效率** - 单次处理 5-20 个 URL，时间可控
2. **增量更新** - 按需选择特定模块更新
3. **容错性强** - 单个模块失败不影响其他模块
4. **结构清晰** - 与 docs/ 目录结构一一对应

## 文件格式

### 基本结构

```
# 保存路径
URL 文件名
URL 文件名
```

### 格式规则

1. 使用 `#` 开头的行标记保存路径（如 `# docs/Chat`）
2. 每行一个 URL 和对应文件名，用空格分隔
3. URL 格式：`https://kiro.dev/docs/cli/...`
4. 文件名格式：`编号_文件名.md` 或 `文件名.md`

### 示例

```
# docs/Chat
https://kiro.dev/docs/cli/chat/ 0_chat_overview.md
https://kiro.dev/docs/cli/chat/session-management/ 1_session-management.md
```

## 使用建议

### 按模块更新

只需更新特定模块时，使用 `url-list/` 目录下对应的 URL 文件：
- 更新聊天功能：使用 `url-list/url_list_chat.txt`
- 更新自定义代理：使用 `url-list/url_list_custom-agents.txt`
- 依此类推

### 全量更新

如需更新所有文档，可依次执行 `url-list/` 目录下各模块的 URL 文件，避免单次处理时间过长

## 新增文档流程

1. 在对应的 `url-list/url_list_*.txt` 文件中添加记录：
   - 选择合适的文件（根据文档模块）
   - 添加保存路径注释（如 `# docs/Chat`）
   - 添加 URL 和文件名
2. 使用 web-to-markdown 技能抓取网页
3. 保存到对应的 docs/ 子目录

## 更新文档流程

1. 从对应的 `url-list/url_list_*.txt` 文件找到目标 URL
2. 使用 web-to-markdown 重新抓取
3. 对比差异并合并更新
4. 保持 URL 记录不变

## 维护规范

### 定期检查

- 验证 URL 是否有效
- 确认文件是否存在
- 检查分类是否正确

### 清理规则

- 删除文档时同时删除 URL 记录
- 移动文档时更新文件名
- 合并分类时重新组织注释

## URL 命名模式

### 官方文档结构

```
https://kiro.dev/docs/cli/{模块}/
https://kiro.dev/docs/cli/{模块}/{功能}/
```

### 对应本地文件

```
docs/{模块}/{编号}_{功能}.md
```

## 注意事项

1. URL 必须完整包含 `https://` 协议
2. 文件名必须与实际文件完全一致
3. 分类注释帮助快速定位，不要删除
4. 保持文件整洁，避免重复记录
