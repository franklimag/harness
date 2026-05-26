# graphify 学习计划指南

> 系统化学习路径，从理解多模态提取到掌握图谱查询，按依赖关系编排。

## 笔记依赖关系图

```
architecture.md (总览，入口)
        │
        ├── notes/01-multimodal-extraction.md (提取层)
        │       │
        │       └── notes/02-graph-construction.md (构建层)
        │               │
        │               └── notes/03-output-formats.md (输出层)
        │
        └── notes/04-incremental-updates.md (优化，独立)
```

## 推荐阅读顺序

| 顺序 | 文件 | 原因 |
|------|------|------|
| 1 | `architecture.md` | 三阶段流水线全景 |
| 2 | `notes/01-multimodal-extraction.md` | 输入处理 |
| 3 | `notes/02-graph-construction.md` | 核心算法 |
| 4 | `notes/03-output-formats.md` | 输出利用 |
| 5 | `notes/04-incremental-updates.md` | 性能优化 |

---

## 阶段一: 理解多模态提取（Day 1）

| 任务 | 完成标准 |
|------|----------|
| tree-sitter | 理解代码 AST 解析 |
| Claude 语义 | 理解文档实体提取 |
| Claude Vision | 理解图片识别 |
| 输入类型 | 能列出支持的文件格式 |

---

## 阶段二: 图谱构建（Day 2）

| 任务 | 完成标准 |
|------|----------|
| NetworkX | 理解图数据结构 |
| Leiden | 理解社区检测 |
| 边类型 | 区分 EXTRACTED/INFERRED/AMBIGUOUS |
| God Nodes | 理解核心枢纽识别 |

---

## 阶段三: 输出与查询（Day 3）

| 任务 | 完成标准 |
|------|----------|
| graph.html | 能使用交互式可视化 |
| Obsidian | 理解 Vault 导出 |
| 查询命令 | 掌握 query/path/explain |
| 71.5x 缩减 | 理解 Token 压缩原理 |

---

## 阶段四: 增量与自动化（Day 4）

| 任务 | 完成标准 |
|------|----------|
| SHA256 | 理解缓存机制 |
| --watch | 理解文件监听 |
| git hook | 理解提交触发 |

---

## 难度与时间预估

| 阶段 | 难度 | 时间 |
|------|------|------|
| 1 多模态提取 | ⭐⭐ | 3h |
| 2 图谱构建 | ⭐⭐⭐ | 3h |
| 3 输出查询 | ⭐⭐ | 2h |
| 4 增量更新 | ⭐ | 1h |

**总计**: 约 9 小时

---

*最后更新: 2026-07*
