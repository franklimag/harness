# 输出格式学习笔记

> graphify 的输出层 — HTML 交互、Obsidian Vault、Wiki、报告和多种导出格式。

## 概述

graphify 支持多种输出格式，覆盖人类浏览、知识管理、Agent 使用和系统集成等场景。

## 输出格式全景

| 输出 | 格式 | 受众 | 用途 |
|------|------|------|------|
| graph.html | vis.js 交互页面 | 人类 | 可视化浏览探索 |
| obsidian/ | Markdown + [[links]] | 人类 | 知识管理 |
| wiki/ | 结构化 Markdown | Agent | Agent 爬取上下文 |
| GRAPH_REPORT.md | 分析报告 | 人类+Agent | 洞察摘要 |
| graph.json | JSON | 程序 | 持久化/API |
| .svg | 矢量图 | 文档 | 嵌入报告 |
| .graphml | GraphML | 工具 | 通用图格式 |
| Neo4j | Cypher | 数据库 | 大规模分析 |
| MCP | Server | Agent | 实时查询 |

## graph.html (交互式可视化)

```
技术: vis.js Network Visualization

功能:
  - 缩放/平移/拖拽
  - 节点点击查看详情
  - 社区颜色分组
  - 边类型可视区分
  - 搜索和过滤
  - 路径高亮

打开方式: 浏览器直接打开 graph.html
```

## Obsidian Vault 导出

```
obsidian/
├── UserService.md
├── NotificationService.md
├── PaymentService.md
└── ...

每个文件:
  # UserService
  
  类型: class
  文件: src/services/user.ts
  社区: 用户管理
  
  ## 关联
  - [[createUser]] (contains, EXTRACTED)
  - [[UserRepo]] (depends_on, EXTRACTED)
  - [[Auth]] (uses, INFERRED)
```

## Wiki 导出 (Agent 可爬取)

```
wiki/
├── index.md         # 入口: 所有实体列表
├── communities.md   # 社区列表和成员
├── entities/
│   ├── UserService.md
│   └── ...
└── relations.md     # 所有关系列表

特点: 为 Agent 优化的结构
  - 平面目录 (减少路径深度)
  - 结构化表格 (易于解析)
  - 交叉引用 (便于跳转)
```

## GRAPH_REPORT.md

```markdown
# Graph Analysis Report

## 概览
- 节点数: 156
- 边数: 423
- 社区数: 8
- 密度: 0.035

## God Nodes (高连接度)
| 节点 | 度数 | 分析 |
|------|------|------|
| ... | ... | ... |

## 社区摘要
| 社区 | 成员数 | 核心节点 |
|------|--------|---------|
| ... | ... | ... |

## 意外连接
| 连接 | 分析 |
|------|------|
| ... | ... |

## Token 统计
- 原始文件总 Token: ~500K
- 图谱表示 Token: ~7K
- 压缩比: 71.5x
```

## 71.5x Token 缩减原理

```
传统方式:
  Agent 需要阅读 52 个文件 = 500K tokens

graphify 方式:
  Agent 读取 wiki/ 或 graph.json = 7K tokens
  需要细节时: 查询特定节点/路径

核心: 用图谱索引替代全文阅读
```

## 问题与思考

- [x] 为什么多种输出? → 不同受众和用途
- [x] 71.5x 如何实现? → 结构化压缩替代全文
- [ ] Obsidian Vault 的自动更新?
- [ ] MCP Server 的查询接口设计?
- [ ] 大图谱 graph.html 的性能?

---

*参考: [architecture.md](../architecture.md) | [04-incremental-updates.md](./04-incremental-updates.md)*
