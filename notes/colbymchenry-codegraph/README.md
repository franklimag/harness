# CodeGraph 学习笔记工程

> 用于系统学习 [CodeGraph](https://github.com/colbymchenry/codegraph) 架构、实现与设计思想的知识库。

## Tracking Snapshot

| 字段 | 值 |
|------|-----|
| 追踪分支 | `main` |
| 最后同步 commit | `02935d777a848e067778ad792c636b4c6e63082d` |
| 同步日期 | 2026-05-26 |
| 对应版本 | v0.9.4 |

## 项目简介

**CodeGraph** 是一个预索引代码知识图谱工具，由 Colby McHenry 开发。它使用 Tree-sitter 将源代码解析为 AST，提取函数/类/方法等节点和调用/导入/继承等边，存储到本地 SQLite 数据库，通过 MCP Server 暴露给 AI Agent 使用。

### 核心数据

| 属性 | 值 |
|------|-----|
| 当前版本 | **v0.9.4** (2026-05-24) |
| 作者 | Colby McHenry |
| 语言 | TypeScript (自带 Node.js 运行时) |
| 许可 | MIT |
| npm | @colbymchenry/codegraph |
| 源码 | https://github.com/colbymchenry/codegraph |
| 支持语言 | 20+ (通过 Tree-sitter) |
| 框架检测 | 14 个 Web 框架 |
| MCP 工具 | 10 个 |

### 核心特点

- **零配置** — 无需配置文件，自动检测语言和框架
- **预索引** — 提前构建知识图谱，AI 查询时零延迟
- **增量同步** — 原生 OS 文件事件监听，2s 防抖自动更新
- **Token 高效** — 平均减少 57% token 消耗，71% 更少工具调用
- **框架感知** — 14 个框架的路由模式识别
- **跨平台桥接** — Swift↔ObjC, React Native, TurboModules, Expo
- **自包含** — 捆绑 Node.js 运行时，无 native build 失败

### 架构模式

```
Source Code (20+ 语言)
    │ Tree-sitter
    ▼
Extraction → Storage (SQLite+FTS5) → Resolution → Auto-Sync
                                                       │
                                                       ▼
                                          MCP Server (10 工具)
                                                       │
                                                       ▼
                                          AI Agent (Claude/Cursor/Windsurf)
```

### Benchmark (v0.9.4)

| 指标 | 改善 |
|------|------|
| 成本 | -35% |
| Token 消耗 | -57% |
| 响应速度 | +46% |
| 工具调用 | -71% |

> 测试于 7 个代码库: VS Code, Excalidraw, Django, Tokio, OkHttp, Gin, Alamofire

## 学习目录

```
.
├── README.md                         # 本文件 — 项目概览
├── architecture.md                   # 架构总览 (四阶段流水线 + 组件详解)
├── changelog.md                      # 版本更新追踪 (v0.9.0 ~ v0.9.4)
├── references.md                     # 参考资料汇总 (官方/生态/学术/框架)
├── learning-guide.md                 # 学习计划指南 (5 个阶段)
└── notes/
    ├── 01-tree-sitter-pipeline.md    # Tree-sitter 解析流水线
    ├── 02-knowledge-graph.md         # SQLite + FTS5 知识图谱存储
    ├── 03-mcp-tools.md              # 10 个 MCP 工具设计
    ├── 04-framework-routes.md        # 14 框架路由检测
    └── 05-auto-sync.md              # 文件监听与增量同步
```

## 学习进度

| 阶段 | 主题 | 状态 | 对应笔记 |
|------|------|------|----------|
| 1 | 整体架构理解 | 🔲 待开始 | `architecture.md` |
| 2 | Tree-sitter 解析流水线 | 🔲 待开始 | `notes/01-tree-sitter-pipeline.md` |
| 3 | MCP 工具设计 | 🔲 待开始 | `notes/03-mcp-tools.md` |
| 4 | 框架路由检测 | 🔲 待开始 | `notes/04-framework-routes.md` |
| 5 | 性能基准方法论 | 🔲 待开始 | `architecture.md` 第五节 |

## 学习路线

1. **阶段一**: 理解整体架构（四阶段流水线: 提取→存储→解析→同步）
2. **阶段二**: 深入 Tree-sitter 解析流水线（AST、语言查询、节点/边提取）
3. **阶段三**: 研究 MCP 工具设计（10 个工具、组合模式、Token 效率）
4. **阶段四**: 学习框架路由检测（14 框架、文件路由、跨平台桥接）
5. **阶段五**: 理解性能基准方法论（benchmark 设计、规模效应）

## 对比学习

| 项目 | 对比维度 | 学习价值 |
|------|----------|----------|
| [Graphify](../safishamsi-graphify/) | 内存图 vs 持久化 SQLite | 存储策略取舍 |
| [CartographAI](https://github.com/CartographAI/mcp-server-codegraph) | 类似概念不同实现 | 设计空间探索 |
| [tokensave (Rust)](https://github.com/aovestdipaperino/tokensave) | TypeScript vs Rust 实现 | 跨语言移植 |
| [Codebase-Memory (论文)](https://arxiv.org/abs/2603.27277) | 学术研究 vs 工程实现 | 理论基础 |

## 关键问题清单

- [ ] Tree-sitter 查询如何处理动态语言中无法静态确定的调用？
- [ ] Resolution 阶段的精确匹配率是多少？
- [ ] 超大型 monorepo 中 Auto-Sync 的性能极限在哪里？
- [ ] 与 LSP 的区别和互补关系是什么？
- [ ] 知识图谱的"过期数据"如何处理？

## 相关链接

- 源码仓库: https://github.com/colbymchenry/codegraph
- npm 包: https://www.npmjs.com/package/@colbymchenry/codegraph
- 学术论文: https://arxiv.org/abs/2603.27277 (Codebase-Memory)
- MCP 协议: https://modelcontextprotocol.io/
- Tree-sitter: https://tree-sitter.github.io/tree-sitter/

---

*项目信息见: [repos/colbymchenry-codegraph.md](../../repos/colbymchenry-codegraph.md)*
*最后更新: 2026-05-24 | 基于 v0.9.4*
