# Steering

## 什么是 Steering？

Steering 通过 `.kiro/steering/` 目录中的 markdown 文件为 Kiro 提供项目的持久化知识。无需在每次对话中解释您的约定，steering 文件确保 Kiro 始终遵循您已建立的模式、库和标准。

## 主要优势

- **一致的代码生成** - 每个组件、API 端点或测试都遵循团队已建立的模式和约定
- **减少重复** - 无需在每次对话中解释项目标准，Kiro 会记住您的偏好
- **团队对齐** - 所有开发者都使用相同的标准，无论是项目新人还是资深贡献者
- **可扩展的项目知识** - 随代码库增长的文档，在项目演进时捕获决策和模式

## Steering 文件作用域

Steering 文件可以创建为工作区作用域或全局作用域。

### 工作区 Steering

工作区 steering 文件位于工作区根目录的 `.kiro/steering/` 文件夹中，仅应用于该特定工作区。工作区 steering 文件可用于告知 Kiro 适用于单个工作区的模式、库和标准。

### 全局 Steering

全局 steering 文件位于主目录的 `~/.kiro/steering/` 中，应用于所有工作区。全局 steering 文件可用于告知 Kiro 适用于*所有*工作区的约定。

当全局和工作区 steering 之间存在冲突指令时，Kiro 将优先使用工作区 steering 指令。这允许您指定通常适用于所有工作区的全局指令，同时保留为特定工作区覆盖这些指令的能力。

### 团队 Steering

全局 steering 功能可用于定义适用于整个团队的集中式 steering 文件。团队 steering 文件可以通过 MDM 解决方案或组策略推送到用户的 PC，或者由用户从中央仓库下载到他们的 PC，并放入 `~/.kiro/steering` 文件夹。

## 基础 Steering 文件

创建基础 steering 文件以建立核心项目上下文。

1. 在项目根目录创建 `.kiro/steering/` 文件夹（工作区作用域）或 `~/.kiro/steering` 文件夹（全局作用域）
2. 为项目标准添加 markdown 文件
3. Kiro 将在聊天会话中自动加载这些文件

基础 steering 文件包括：

**产品概述** (`product.md`) - 定义产品的目的、目标用户、关键功能和业务目标。这有助于 Kiro 理解技术决策背后的"原因"，并提出与产品目标一致的解决方案。

**技术栈** (`tech.md`) - 记录您选择的框架、库、开发工具和技术约束。当 Kiro 建议实现时，它会优先使用您已建立的堆栈而非替代方案。

**项目结构** (`structure.md`) - 概述文件组织、命名约定、导入模式和架构决策。这确保生成的代码无缝融入现有代码库。

这些基础文件默认包含在每次交互中，构成 Kiro 项目理解的基线。

## 创建自定义 Steering 文件

根据项目独特需求扩展 Kiro 的理解：

1. 在 `.kiro/steering/` 中创建新的 `.md` 文件
2. 选择描述性文件名（例如 `api-standards.md`）
3. 使用标准 markdown 语法编写指导内容
4. 使用自然语言描述您的需求

## 在自定义代理中使用 Steering

使用[自定义代理](/docs/cli/custom-agents/creating)时，steering 文件不会自动包含。您必须将其显式添加到代理的 `resources` 配置中以加载 steering 上下文。

要在自定义代理中包含所有 steering 文件，请将以下内容添加到代理配置中：

```json
{
  "resources": ["file://.kiro/steering/**/*.md"]
}
```

此 glob 模式确保使用代理时加载 steering 目录中的所有 markdown 文件。有关完整的配置示例，请参阅[自定义代理文档](/docs/cli/custom-agents/creating/#agent-configuration-file)。

## AGENTS.md

Kiro 支持通过 [AGENTS.md](https://agents.md/) 标准提供 steering 指令。AGENTS.md 文件采用 markdown 格式，类似于 Kiro steering 文件；但是，AGENTS.md 文件始终会被包含。

您可以将 AGENTS.md 文件添加到全局 steering 文件位置（`~/.kiro/steering/`）或工作区的根文件夹，Kiro 会自动识别它们。

## 最佳实践

**保持文件专注** - 每个文件一个领域 - API 设计、测试或部署流程。

**使用清晰的命名**
- `api-rest-conventions.md` - REST API 标准
- `testing-unit-patterns.md` - 单元测试方法
- `components-form-validation.md` - 表单组件标准

**包含上下文** - 解释做出决策的原因，而不仅仅是标准是什么。

**提供示例** - 使用代码片段和前后对比来演示标准。

**安全优先** - 切勿包含 API 密钥、密码或敏感数据。Steering 文件是代码库的一部分。

**定期维护**
- 在冲刺规划和架构变更期间进行审查
- 重构后测试文件引用
- 像代码变更一样对待 steering 变更 - 需要审查

## 常见 Steering 文件策略

**API 标准** (`api-standards.md`) - 定义 REST 约定、错误响应格式、身份验证流程和版本控制策略。包括端点命名模式、HTTP 状态码使用和请求/响应示例。

**测试方法** (`testing-standards.md`) - 建立单元测试模式、集成测试策略、模拟方法和覆盖率期望。记录首选测试库、断言风格和测试文件组织。

**代码风格** (`code-conventions.md`) - 指定命名模式、文件组织、导入顺序和架构决策。包括首选代码结构、组件模式和应避免的反模式示例。

**安全指南** (`security-policies.md`) - 记录身份验证要求、数据验证规则、输入清理标准和漏洞预防措施。包括特定于应用程序的安全编码实践。

**部署流程** (`deployment-workflow.md`) - 概述构建流程、环境配置、部署步骤和回滚策略。包括 CI/CD 管道详细信息和特定环境要求。

自定义 steering 文件存储在 `.kiro/steering/` 中，并在所有 Kiro CLI 聊天会话中立即可用。

---

页面更新时间: 2026年1月8日
