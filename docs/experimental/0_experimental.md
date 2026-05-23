# 实验性功能

Kiro CLI 包含实验性功能，提供高级功能。这些功能正在积极开发中，可以使用 `/experiment` 命令开启或关闭。

**警告**

这些功能可能随时更改或移除。体验可能不够完美。在生产工作流中使用时请自行斟酌。

提供这些功能是为了收集反馈和测试新能力。请通过 `kiro issue` 命令报告问题。

## 管理实验

使用 `/experiment` 命令切换实验性功能：

```bash
/experiment
```

这将显示一个交互式菜单，您可以：
- 查看每个实验的当前状态（开启/关闭）
- 通过选择来切换实验
- 查看每个实验的功能描述

## 可用的实验

### 知识管理

**命令：** `/knowledge`

启用跨聊天会话的持久化上下文存储和检索，具有语义搜索能力。

**功能：**
- 存储和搜索文件、目录和文本内容
- 语义搜索以获得更好的上下文检索
- 跨会话的持久化知识库
- 特定代理的知识隔离

**启用：**

```bash
kiro-cli settings chat.enableKnowledge true
```

[了解更多关于知识管理 →](./1_knowledge-management.md)

### 切线模式

**命令：** `/tangent` 或 **Ctrl+T**

创建对话检查点，探索副主题而不打断主对话流程。

**功能：**
- 创建对话检查点
- 探索切线主题
- 返回主对话线程
- 保留对话上下文

**启用：**

```bash
kiro-cli settings chat.enableTangentMode true
```

[了解更多关于切线模式 →](./2_tangent-mode.md)

### TODO 列表

**工具：** `todo` | **命令：** `/todo`

使 Kiro 能够自动创建和修改 TODO 列表，并提供命令供您查看和管理。

**功能：**
- Kiro 在适当时自动创建 TODO 列表
- 查看、管理和删除 TODO
- 恢复现有的 TODO 列表
- 跨聊天会话持久化

**启用：**

```bash
kiro-cli settings chat.enableTodoList true
```

[了解更多关于 TODO 列表 →](./3_todo-lists.md)

### 思考工具

为复杂问题显示 AI 推理过程，展示逐步思考过程。

**功能：**
- 透明的决策过程
- 逐步推理展示
- 对调试和学习很有用
- 更好地理解结论

**启用：**

```bash
kiro-cli settings chat.enableThinking true
```

[了解更多关于思考工具 →](./4_thinking.md)

### 检查点

**命令：** `/checkpoint`

启用会话范围的检查点，使用类似 Git 的命令跟踪文件更改。

**功能：**
- 将文件更改快照到影子 git 仓库
- 列出、展开、比较和恢复检查点
- 恢复时对话历史会回退
- 在 git 仓库中自动启用
- 非 git 目录需手动初始化

**启用：**

```bash
kiro-cli settings chat.enableCheckpoint true
```

**基本用法：**

```bash
/checkpoint list                    # 显示检查点
/checkpoint expand <tag>            # 显示详细的检查点信息
/checkpoint diff <tag1> [tag2]      # 比较检查点
/checkpoint restore [<tag>]         # 恢复到检查点
/checkpoint clean                   # 删除会话影子仓库
```

[了解更多关于检查点 →](./5_checkpointing.md)

### 上下文使用百分比

在聊天提示中显示上下文窗口使用百分比，带有颜色指示器。

**功能：**
- 在提示中显示百分比（例如 "[rust-agent] 6% >"）
- 颜色指示器：
  - 绿色：使用率低于 50%
  - 黄色：使用率 50-89%
  - 红色：使用率 90-100%
- 帮助监控上下文消耗

**启用：**

```bash
kiro-cli settings chat.enableContextUsageIndicator true
```

### 委托

启动和管理异步任务进程，并行运行具有特定代理的 Kiro 聊天会话。

**功能：**
- 使用自然语言启动后台任务
- 使用特定代理并行运行聊天会话
- 独立监控任务进度
- 代理审批流程以确保安全

**启用：**

```bash
kiro-cli settings chat.enableDelegate true
```

**用法：** 使用自然语言让 Kiro 启动后台任务：

`你能创建一个后台任务来分析我们 API 端点的性能吗？`

然后检查结果：

`检查我的 API 分析任务状态`
`显示后台分析的结果`

[了解更多关于委托 →](./6_delegate.md)

## 设置集成

实验存储为设置并跨会话持久化：

```bash
# 查看所有实验性设置
kiro-cli settings list | grep -i enable

# 启用/禁用特定实验
kiro-cli settings chat.enableKnowledge true
kiro-cli settings chat.enableTangentMode true
kiro-cli settings chat.enableTodoList true
kiro-cli settings chat.enableThinking true
kiro-cli settings chat.enableCheckpoint true
kiro-cli settings chat.enableContextUsageIndicator true
kiro-cli settings chat.enableDelegate true
```

## 模糊搜索支持

所有实验性命令都在模糊搜索中可用（Ctrl+S）：
- `/experiment` - 管理实验性功能
- `/knowledge` - 知识库命令（启用后）
- `/todo` - TODO 列表命令（启用后）
- `/tangent` - 切线模式切换（启用后）
- `/checkpoint` - 检查点命令（启用后）

## 最佳实践

1. **在安全环境中测试**：首先在非关键项目上尝试实验性功能
2. **提供反馈**：使用 `kiro issue` 报告问题和建议
3. **保持更新**：查看发布说明了解实验性功能的更改
4. **了解限制**：阅读各个功能文档了解已知问题
5. **备份**：某些功能会修改文件（检查点、TODO 列表）

## 故障排除

### 功能不起作用

1. 验证功能已启用：
   
   ```bash
   kiro-cli settings list | grep -i enable
   ```
2. 检查聊天中的错误消息
3. 尝试禁用并重新启用：
   
   ```bash
   kiro-cli settings chat.enableFeatureName false
   kiro-cli settings chat.enableFeatureName true
   ```
4. 重启 Kiro CLI

### 命令不可用

在使用命令之前确保功能已启用。例如，`/knowledge` 只有在知识管理启用后才起作用。

## 下一步

- [知识管理](./1_knowledge-management.md)
- [切线模式](./2_tangent-mode.md)
- [TODO 列表](./3_todo-lists.md)
- [设置配置](../settings.md)
