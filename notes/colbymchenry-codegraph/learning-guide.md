# CodeGraph 学习计划指南

> 系统化的学习路径，从理解架构到掌握实现细节，按依赖关系编排。

## 使用说明

本指南提供五个学习阶段，循序渐进：
1. 理解整体架构
2. 深入 Tree-sitter 解析流水线
3. MCP 工具设计
4. 框架路由检测
5. 性能基准方法论

建议按顺序完成，每个阶段建立在前一阶段的理解之上。

---

## 笔记依赖关系图

```
architecture.md (总览，入口)
        │
        ├── notes/01-tree-sitter-pipeline.md (解析流水线)
        │       │
        │       └── notes/02-knowledge-graph.md (存储，依赖解析输出)
        │               │
        │               └── notes/03-mcp-tools.md (工具，查询存储)
        │
        ├── notes/04-framework-routes.md (框架检测，独立可读)
        │
        └── notes/05-auto-sync.md (增量同步，依赖整体理解)

changelog.md (随时查阅)
references.md (深入时参考)
```

## 推荐阅读顺序

| 顺序 | 文件 | 原因 |
|------|------|------|
| 1 | `architecture.md` | 全局视角，建立心智模型 |
| 2 | `notes/01-tree-sitter-pipeline.md` | 数据入口，理解代码如何变成图 |
| 3 | `notes/02-knowledge-graph.md` | 存储层，理解图的数据结构 |
| 4 | `notes/03-mcp-tools.md` | 输出层，理解 AI 如何使用图 |
| 5 | `notes/04-framework-routes.md` | 高级特性，理解框架感知 |
| 6 | `notes/05-auto-sync.md` | 运行时特性，理解增量维护 |
| 参考 | `changelog.md` | 随时查阅版本变化 |
| 参考 | `references.md` | 深入某话题时查阅原始资料 |

---

## 阶段 1: 理解整体架构（Day 1）

**目标**: 理解 CodeGraph 是什么、解决什么问题、四阶段流水线如何协作。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| 了解 CodeGraph 定位 | `README.md` | 能用一句话描述 CodeGraph 的价值 |
| 理解四阶段架构 | `architecture.md` 第一~二节 | 能画出 Extraction→Storage→Resolution→Sync 流程图 |
| 理解数据流 | `architecture.md` 第三节 | 能描述一个函数从源码到 MCP 查询的完整路径 |
| 理解设计原则 | `architecture.md` 第四节 | 理解"零配置"和"预索引"的设计动机 |

**验证**: 能向他人解释 "为什么 AI Agent 需要代码知识图谱，而不是直接读文件"。

### 关键概念

| 概念 | 解释 |
|------|------|
| 预索引 | 提前构建知识图谱，AI 查询时无需遍历文件系统 |
| 零配置 | 无需任何配置文件，自动检测语言和框架 |
| 知识图谱 | 以"节点+边"表示代码实体及其关系 |
| MCP | Model Context Protocol，AI 工具通信标准 |

---

## 阶段 2: 深入 Tree-sitter 解析流水线（Day 2）

**目标**: 理解源代码如何被解析为 AST、如何提取节点和边。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| Tree-sitter 基础 | `notes/01-tree-sitter-pipeline.md` + Tree-sitter 官方文档 | 理解 AST 是什么 |
| 语言查询机制 | `notes/01-tree-sitter-pipeline.md` 查询部分 | 理解 S-expression 查询语法 |
| 节点提取 | `notes/01-tree-sitter-pipeline.md` 节点部分 | 知道提取哪些类型的节点 |
| 边提取 | `notes/01-tree-sitter-pipeline.md` 边部分 | 知道提取哪些类型的关系 |

**验证**: 能解释 "Tree-sitter 查询如何从 TypeScript 函数声明中提取函数名、参数和返回类型"。

### 实践建议

1. 在 Tree-sitter Playground 中加载一段代码，观察 AST 结构
2. 尝试编写简单的查询来匹配函数声明
3. 对比不同语言的 AST 结构差异

---

## 阶段 3: MCP 工具设计（Day 3）

**目标**: 理解 10 个 MCP 工具的设计哲学、输入输出和使用场景。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| MCP 协议基础 | MCP 规范文档 | 理解 tool/resource/prompt 三要素 |
| 10 个工具分类 | `notes/03-mcp-tools.md` | 能分类: 搜索类/图遍历类/元信息类 |
| 工具组合模式 | `notes/03-mcp-tools.md` 模式部分 | 理解常见查询组合 |
| Token 效率原理 | `architecture.md` 第五节 | 理解为什么减少了 71% 工具调用 |

**验证**: 能设计一个场景 "AI 要修改一个函数，用哪些工具组合来获取足够上下文"。

### 工具组合示例

```
场景: AI 需要修改 handleRequest() 函数

1. codegraph_search("handleRequest")    → 找到节点
2. codegraph_context(nodeId)            → 获取完整实现
3. codegraph_callers(nodeId)            → 谁调用了它？
4. codegraph_callees(nodeId)            → 它调用了谁？
5. codegraph_impact(nodeId)             → 修改会影响什么？
```

---

## 阶段 4: 框架路由检测（Day 4）

**目标**: 理解 CodeGraph 如何检测 14 个框架的路由模式。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| 框架路由概念 | `notes/04-framework-routes.md` | 理解"框架感知"的含义 |
| Python 框架 | `notes/04-framework-routes.md` Python 部分 | 理解 Django/Flask/FastAPI 的路由检测 |
| Node.js 框架 | `notes/04-framework-routes.md` Node 部分 | 理解 Express/NestJS 的路由检测 |
| 文件路由 | `notes/04-framework-routes.md` 文件路由部分 | 理解 React Router/SvelteKit 的文件路由 |
| 跨平台桥接 | `architecture.md` 2.6 节 | 理解 iOS/RN/Expo 的桥接解析 |

**验证**: 能解释 "CodeGraph 如何知道 Django 的 `path('users/', views.user_list)` 对应哪个视图函数"。

---

## 阶段 5: 性能基准方法论（Day 5）

**目标**: 理解 benchmark 的设计、如何量化"代码知识图谱"的价值。

| 任务 | 阅读材料 | 完成标准 |
|------|----------|----------|
| Benchmark 指标 | `architecture.md` 第五节 | 理解四个核心指标 (cost/tokens/speed/calls) |
| 测试代码库选择 | `changelog.md` v0.9.4 部分 | 理解为什么选择这 7 个项目 |
| 规模效应 | Benchmark 数据分析 | 理解为什么大代码库收益更大 |
| 对比方法 | 与同类工具对比 | 理解 A/B 测试的基线是什么 |

**验证**: 能设计一个简单的 benchmark 来验证 "使用 CodeGraph vs 不使用" 的效率差异。

### 思考题

1. 为什么 "工具调用减少 71%" 是最有意义的指标？
2. 代码库越大收益越大的原因是什么？
3. 什么类型的任务从 CodeGraph 获益最大？什么类型获益最小？

---

## 难度与时间预估

| 阶段 | 难度 | 预计时间 | 前置要求 |
|------|------|----------|----------|
| 1 整体架构 | ⭐ | 2-3 小时 | 无 |
| 2 Tree-sitter 流水线 | ⭐⭐⭐ | 4-6 小时 | 阶段 1 + 编译器基础知识 |
| 3 MCP 工具设计 | ⭐⭐ | 3-4 小时 | 阶段 1-2 |
| 4 框架路由 | ⭐⭐ | 3-4 小时 | 阶段 1 + 框架使用经验 |
| 5 Benchmark 方法论 | ⭐⭐ | 2-3 小时 | 阶段 1-3 |

**总计**: 约 14-20 小时

---

## 学习过程中的关键决策点

| 决策点 | 时机 | 选项 | 建议 |
|--------|------|------|------|
| 是否搭建本地环境 | 阶段 1 后 | 纯文档学习 / 安装体验 | 建议安装体验 (`npx @colbymchenry/codegraph`) |
| Tree-sitter 深入程度 | 阶段 2 | 仅理解概念 / 学写查询 | 看学习目标，开发者建议学写查询 |
| 对比学习 | 阶段 3 后 | 只学 CodeGraph / 对比 Graphify | 对比有助于理解设计取舍 |
| 贡献代码 | 阶段 4 后 | 被动学习 / 贡献新框架支持 | 添加新框架是很好的贡献入口 |

---

## 进度追踪模板

复制以下模板到你的笔记中追踪学习进度：

```markdown
## 我的学习进度

### 阶段 1: 整体架构
- [ ] 读完 README.md，理解 CodeGraph 定位
- [ ] 读完 architecture.md，理解四阶段流水线
- [ ] 能画出完整架构图
- [ ] 能解释为什么需要代码知识图谱

### 阶段 2: Tree-sitter 流水线
- [ ] 理解 Tree-sitter AST 概念
- [ ] 在 Playground 中实验 AST 解析
- [ ] 理解语言查询 (S-expression) 语法
- [ ] 知道节点和边的提取逻辑

### 阶段 3: MCP 工具设计
- [ ] 理解 MCP 协议三要素
- [ ] 掌握 10 个工具的分类和用途
- [ ] 理解工具组合模式
- [ ] 理解 Token 效率原理

### 阶段 4: 框架路由检测
- [ ] 理解 14 个框架的路由检测
- [ ] 知道文件路由 vs 代码路由的区别
- [ ] 理解 iOS/RN 跨平台桥接

### 阶段 5: Benchmark 方法论
- [ ] 理解四个核心指标
- [ ] 理解规模效应
- [ ] 能设计简单的对比实验
```

---

## 补充资源

### 每个阶段的推荐外部阅读

| 阶段 | 推荐外部资源 |
|------|-------------|
| 1 | CodeGraph GitHub README |
| 2 | [Tree-sitter 官方文档](https://tree-sitter.github.io/tree-sitter/) |
| 2 | [Tree-sitter Playground](https://tree-sitter.github.io/tree-sitter/playground) |
| 3 | [MCP 协议规范](https://spec.modelcontextprotocol.io/) |
| 4 | 各框架路由文档 (见 references.md) |
| 5 | [arxiv:2603.27277 — Codebase-Memory](https://arxiv.org/abs/2603.27277) |

### 对比学习资源

| 项目 | 对比维度 | 学习价值 |
|------|----------|----------|
| Graphify | 内存 vs 磁盘存储 | 理解持久化 vs 性能的取舍 |
| CartographAI | 类似概念不同实现 | 理解设计空间 |
| Aider | 如何使用 tree-sitter | 理解另一种代码分析方式 |
| tokensave | TypeScript vs Rust | 理解跨语言移植的考量 |

---

*最后更新: 2026-05-24*
