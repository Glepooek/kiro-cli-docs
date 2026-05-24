# 项目结构

## 目录组织

```
kiro-cli-docs/
├── .kiro/              # Kiro 配置目录
│   └── steering/       # Steering 文件
├── .remember/          # Kiro 记忆目录
├── docs/               # 主文档目录
│   ├── Chat/          # 聊天功能文档
│   ├── custom-agents/ # 自定义代理文档
│   ├── mcp/           # MCP 配置文档
│   ├── experimental/  # 实验性功能
│   └── reference/     # 参考文档
├── url_list.txt        # URL 来源列表
├── README.md          # 项目说明
└── LICENSE            # 许可证
```

## 文件命名约定

### 编号规则

- 根据官方网站中文章的先后顺序，使用数字前缀进行排序：`0_`, `1_`, `2_`...
- 示例：`0_chat_overview.md`, `1_session-management.md`

### 文件名格式

- 全部小写字母
- 使用连字符分隔单词
- 避免空格和特殊字符
- 示例：`code-intelligence.md`, `api-standards.md`

## 目录分类规则

### docs/Chat/

聊天功能相关文档，包括会话管理、子代理、上下文处理等

### docs/custom-agents/

自定义代理的创建、配置、示例和故障排除

### docs/mcp/

模型上下文协议 (MCP) 相关文档

### docs/experimental/

实验性功能，如知识管理、思考模式、检查点等

### docs/reference/

参考文档，包括 CLI 命令、斜杠命令、内置工具等

## URL 管理

`url_list.txt` 记录文档来源：

- 每行格式：`URL 文件名`
- 按目录分组，使用注释标记
- 示例：`https://kiro.dev/docs/cli/chat/ 0_chat_overview.md`

## 导入模式

- 文档按功能模块组织
- 每个子目录包含该模块的完整文档集
- 使用 README 或编号 0 的文件作为模块入口
