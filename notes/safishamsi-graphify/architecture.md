# graphify 架构总览

> 基于 GitHub 仓库 (safishamsi/graphify) 和公开资料整理。最后更新: 2026-07

## 零、项目基本信息

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/safishamsi/graphify |
| 作者 | Safi Shamsi |
| Stars | 30K+ (22K in 10 days) |
| PyPI | graphifyy (双 y) |
| 发布 | April 2026 |
| 技术栈 | NetworkX + Leiden + tree-sitter + Claude + vis.js |
| 特点 | 无 Neo4j, 无服务器, 本地运行 |

## 一、整体架构

### 三阶段流水线

```
┌─────────────────────────────────────────────────────────┐
│  Stage 1: EXTRACTION (提取)                              │
│                                                         │
│  输入文件 → 解析器选择 → 实体/关系提取                    │
│                                                         │
│  .py/.ts/.js/.go → tree-sitter (AST 解析)               │
│  .md/.txt/.rst   → Claude (语义提取)                    │
│  .pdf            → Claude (引用挖掘)                    │
│  .png/.jpg       → Claude Vision (视觉识别)             │
└───────────────────────────┬─────────────────────────────┘
                            │ 实体 + 关系
                            ▼
┌─────────────────────────────────────────────────────────┐
│  Stage 2: CONSTRUCTION (构建)                            │
│                                                         │
│  NetworkX 图构建 → Leiden 社区检测 → 边类型标注           │
│                                                         │
│  节点: 实体 (函数/类/模块/概念/人物/引用)                │
│  边: 关系 (调用/继承/引用/关联/相似)                     │
│  边类型: EXTRACTED / INFERRED / AMBIGUOUS                │
│  社区: Leiden 算法自动分组                                │
└───────────────────────────┬─────────────────────────────┘
                            │ 构建完成的图
                            ▼
┌─────────────────────────────────────────────────────────┐
│  Stage 3: OUTPUT (输出)                                  │
│                                                         │
│  graph.html  — vis.js 交互式可视化                       │
│  obsidian/   — Obsidian Vault (Markdown + links)        │
│  wiki/       — Agent 可爬取的知识库                      │
│  GRAPH_REPORT.md — 分析报告                              │
│  graph.json  — 持久化数据                                │
│  (可选) .svg / .graphml / neo4j / MCP                   │
└─────────────────────────────────────────────────────────┘
```

## 二、多模态提取详解

### 代码文件 (tree-sitter)

| 语言 | 扩展名 | 提取内容 |
|------|--------|---------|
| Python | .py | 函数/类/导入/调用关系 |
| TypeScript | .ts | 接口/类型/函数/导出 |
| JavaScript | .js | 函数/模块/依赖 |
| Go | .go | 结构体/方法/包引用 |
| Rust | .rs | trait/impl/mod/use |
| Java | .java | 类/接口/继承/注入 |

### 文档文件 (Claude 语义)

| 格式 | 提取方式 | 提取内容 |
|------|---------|---------|
| .md | Claude 语义分析 | 概念/术语/关系/定义 |
| .txt | Claude 语义分析 | 关键实体/关联 |
| .rst | Claude 语义分析 | 文档结构/引用 |

### PDF 论文 (引用挖掘)

```
PDF 输入 → 文本提取 → Claude 分析
  提取:
    - 论文标题/作者
    - 关键概念
    - 引用关系
    - 方法论实体
```

### 图片 (Claude Vision)

```
图片输入 → Claude Vision API
  识别:
    - 架构图中的组件和连接
    - 白板上的概念和关系
    - UML 图中的类和关系
    - 流程图中的步骤和决策
```

## 三、图谱构建

### NetworkX 图结构

```python
# 概念示例
G = nx.DiGraph()

# 节点 (实体)
G.add_node("UserService", type="class", file="user.ts", line=15)
G.add_node("createUser", type="function", file="user.ts", line=20)

# 边 (关系)
G.add_edge("UserService", "createUser", 
           type="EXTRACTED", relation="contains")
```

### 边类型透明度

| 边类型 | 含义 | 置信度 | 来源 |
|--------|------|--------|------|
| EXTRACTED | 直接从代码/文档提取 | 高 | tree-sitter / 明确引用 |
| INFERRED | 通过分析推断出的关系 | 中 | Claude 语义推理 |
| AMBIGUOUS | 不确定的关系 | 低 | 模糊引用 / 图片识别 |

### Leiden 社区检测

```
Leiden 算法 (graspologic 实现):
  输入: 图 G 的边权重
  输出: 节点分组 (社区)
  
  效果: 自动将相关实体分组
    社区 1: [UserService, createUser, UserRepo, User]
    社区 2: [NotificationService, sendEmail, EmailTemplate]
    社区 3: [APIGateway, AuthMiddleware, RateLimit]
```

### God Nodes (上帝节点)

```
God Node = 连接数极多的节点
  = 系统中的核心枢纽
  = 可能是:
    - 基础设施类 (Logger, Config)
    - 核心业务类 (UserService)
    - 共享工具 (utils)
    
GRAPH_REPORT.md 会标记 God Nodes
```

## 四、缓存与增量更新

### SHA256 缓存

```
首次处理:
  文件 A → SHA256(A) → 提取 → 缓存结果
  文件 B → SHA256(B) → 提取 → 缓存结果

再次运行:
  文件 A (未变) → SHA256 匹配 → 跳过 (使用缓存)
  文件 B (已变) → SHA256 不匹配 → 重新提取
  文件 C (新增) → 无缓存 → 新提取

效果: 只处理变化的文件，大幅减少时间和 API 调用
```

## 五、命令系统

| 命令 | 功能 |
|------|------|
| `/graphify` | 构建完整图谱 |
| `/graphify query <问题>` | 查询图谱中的信息 |
| `/graphify path <A> <B>` | 查找两个实体间的路径 |
| `/graphify explain <节点>` | 解释某个实体的角色 |
| `/graphify add <URL>` | 从 URL 添加内容 |
| `--watch` | 文件变化自动同步 |
| `--wiki` | 生成 wiki 格式输出 |
| `--svg` | 导出 SVG 图片 |
| `--graphml` | 导出 GraphML 格式 |
| `--neo4j` | 导出到 Neo4j |
| `--mcp` | 启动 MCP Server |

## 六、71.5x Token 缩减

```
对比实验:
  语料: 52 文件混合语料库 (代码 + 论文 + 图片)
  
  直接给 Agent 所有文件: ~500K tokens
  给 Agent graphify 输出: ~7K tokens
  
  缩减比: 500K / 7K ≈ 71.5x

原理:
  把"全部原始文件"压缩为"结构化知识图谱"
  Agent 通过图谱理解项目，而非阅读每个文件
```

## 七、待深入研究

- [ ] tree-sitter 解析的具体 query 语法
- [ ] Claude Vision 的 prompt 设计
- [ ] Leiden 算法的参数调优
- [ ] --watch 的文件系统监听实现
- [ ] MCP Server 的工具接口设计
- [ ] Obsidian Vault 的链接格式

---

*参考来源见 [references.md](./references.md)*
*最后更新: 2026-07*
