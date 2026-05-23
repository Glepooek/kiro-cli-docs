# Steering

## 什么是 Steering?

Steering 通过 `.kiro/steering/` 中的 markdown 文件为 Kiro 提供项目的持久知识。无需在每次聊天中解释您的约定,Steering 文件确保 Kiro 始终遵循您既定的模式、库和标准。

## 主要优势

**一致的代码生成** - 每个组件、API 端点或测试都遵循您团队既定的模式和约定。

**减少重复** - 无需在每次对话中解释项目标准。Kiro 会记住您的偏好。

**团队一致性** - 所有开发人员都使用相同的标准,无论他们是项目新手还是资深贡献者。

**可扩展的项目知识** - 随代码库增长的文档,捕获决策和模式。

## Steering 文件作用域

Steering 文件可以创建为工作区作用域或全局作用域。

### 工作区 Steering

工作区 Steering 文件位于工作区根文件夹的 `.kiro/steering/` 下,仅适用于该特定工作区。工作区 Steering 文件可用于告知 Kiro 适用于单个工作区的模式、库和标准。

### 全局 Steering

全局 Steering 文件位于主目录的 `~/.kiro/steering/` 下,适用于所有工作区。全局 Steering 文件可用于告知 Kiro 适用于*所有*工作区的约定。

如果全局和工作区 Steering 之间存在冲突的指令,Kiro 将优先考虑工作区 Steering 指令。这允许您指定通常适用于所有工作区的全局指令,同时保留为特定工作区覆盖这些指令的能力。

### 团队 Steering

全局 Steering 功能可用于定义适用于整个团队的集中式 Steering 文件。团队 Steering 文件可以通过 MDM 解决方案或组策略推送到用户的 PC,或由用户从中央仓库下载到他们的 PC,并放入 `~/.kiro/steering` 文件夹。

## 基础 Steering 文件

创建基础 Steering 文件以建立核心项目上下文。
1. 在项目根目录创建 `.kiro/steering/` 文件夹(工作区作用域)或在主目录创建 `~/.kiro/steering` 文件夹(全局作用域)。
2. 为您的项目标准添加 markdown 文件
3. Kiro 将在聊天会话中自动加载这些文件

基础 Steering 文件包括:

**产品概述** (`product.md`) - 定义产品的目的、目标用户、关键功能和业务目标。这有助于 Kiro 理解技术决策背后的"原因",并提出与产品目标一致的解决方案。

**技术栈** (`tech.md`) - 记录您选择的框架、库、开发工具和技术约束。当 Kiro 建议实现时,它会优先考虑您既定的技术栈而不是替代方案。

**项目结构** (`structure.md`) - 概述文件组织、命名约定、导入模式和架构决策。这确保生成的代码无缝融入您现有的代码库。

这些基础文件默认包含在每次交互中,构成 Kiro 项目理解的基线。

## 创建自定义 Steering 文件

使用针对项目独特需求的专业指导扩展 Kiro 的理解:
1. 在 `.kiro/steering/` 中创建新的 `.md` 文件
2. 选择描述性的文件名(例如 `api-standards.md`)
3. 使用标准 markdown 语法编写您的指导
4. 使用自然语言描述您的要求

## 在自定义 Agent 中使用 Steering

使用自定义 Agent 时,Steering 文件不会自动包含。您必须将它们显式添加到 Agent 的 `resources` 配置中以加载 Steering 上下文。

要在自定义 Agent 中包含所有 Steering 文件,请将以下内容添加到您的 Agent 配置中:

```json
{
  "resources": ["file://.kiro/steering/**/*.md"]
}
```

此 glob 模式确保在使用 Agent 时加载 Steering 目录中的所有 markdown 文件。

## AGENTS.md

Kiro 支持通过 AGENTS.md 标准提供 Steering 指令。AGENTS.md 文件采用 markdown 格式,类似于 Kiro Steering 文件;然而,AGENTS.md 文件始终会被包含。

您可以将 AGENTS.md 文件添加到全局 Steering 文件位置(`~/.kiro/steering/`)或工作区的根文件夹,Kiro 会自动获取它们。
