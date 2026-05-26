# 图谱构建学习笔记

> graphify 的核心层 — NetworkX 图结构、Leiden 社区检测、边类型透明度与 God Nodes。

## 概述

图谱构建是 graphify 的核心算法层，将提取的实体和关系组装为结构化知识图谱，并通过社区检测和分析提供洞察。

## NetworkX 图结构

### 数据模型

```python
import networkx as nx

G = nx.DiGraph()  # 有向图

# 节点 = 实体
G.add_node("UserService", 
    type="class",
    file="src/services/user.ts",
    line=15,
    community=0,
    source="tree-sitter"
)

# 边 = 关系  
G.add_edge("UserService", "createUser",
    relation="contains",
    edge_type="EXTRACTED",
    weight=1.0,
    file="src/services/user.ts"
)
```

### 为什么选 NetworkX (非 Neo4j)

| 维度 | NetworkX | Neo4j |
|------|---------|-------|
| 依赖 | pip install | 需要数据库服务器 |
| 部署 | 零配置 | Docker/安装 |
| 速度 | 内存中，极快 | 网络 I/O |
| 适用规模 | ~100K 节点 | ~1B 节点 |
| graphify 定位 | ✅ 本地工具 | ❌ 太重 |

## 边类型透明度系统

### 三种边类型

| 类型 | 含义 | 来源 | 置信度 |
|------|------|------|--------|
| EXTRACTED | 确定的关系 | tree-sitter / 明确引用 | 高 (>0.9) |
| INFERRED | 推断的关系 | Claude 语义推理 | 中 (0.5-0.9) |
| AMBIGUOUS | 模糊的关系 | 不确定来源 | 低 (<0.5) |

### 透明度的价值

```
问题: 图谱中哪些关系可靠? 哪些只是猜测?

传统图谱: 所有边看起来一样 (不透明)
  A ──── B (这是确定的吗? 还是推测的?)

graphify: 边类型标记 (透明)
  A ═══ B (EXTRACTED: 确定的，代码中明确写了)
  A ─── B (INFERRED: 推断的，Claude 分析得出)
  A ╌╌╌ B (AMBIGUOUS: 不确定，需要人工验证)
```

## Leiden 社区检测

### 算法原理

```
Leiden 算法 (改进的 Louvain):
  1. 初始: 每个节点一个社区
  2. 局部移动: 节点移到增益最大的社区
  3. 细化: 保证社区连通性
  4. 聚合: 创建更高层级图
  5. 重复: 直到收敛

优于 Louvain:
  - 保证社区连通
  - 质量更高
  - 速度更快
```

### 社区的实际含义

```
自动检测出的社区可能对应:
  社区 1: 用户管理模块 [UserService, UserRepo, Auth, Login]
  社区 2: 通知系统 [NotificationService, Email, Push, Template]
  社区 3: 支付系统 [PaymentService, Stripe, Invoice, Refund]

= 自动发现项目的模块边界
= 无需人工标注
```

## God Nodes 分析

### 定义

```
God Node = 图中连接数（度）异常高的节点

识别方法:
  度数分布中的异常值
  连接数 > 平均度数 + 2×标准差

含义:
  - 可能是系统核心 (正常)
  - 可能是违反单一职责 (问题)
  - 可能是基础设施 (Logger/Config)
```

### GRAPH_REPORT.md 中的 God Nodes 报告

```markdown
## God Nodes (高连接度节点)

| 节点 | 类型 | 连接数 | 社区 | 分析 |
|------|------|--------|------|------|
| Logger | utility | 45 | 基础设施 | 正常: 日志工具被广泛使用 |
| UserService | class | 32 | 用户模块 | 警告: 可能职责过多 |
| Config | singleton | 28 | 基础设施 | 正常: 配置被广泛引用 |

## 意外连接 (Surprising Connections)

| 连接 | 边类型 | 分析 |
|------|--------|------|
| PaymentService ─── EmailTemplate | INFERRED | 发现: 支付触发邮件模板 |
```

## 问题与思考

- [x] 为什么 NetworkX? → 轻量本地，无需服务器
- [x] 边类型的价值? → 透明度，区分确定与不确定
- [x] Leiden 的作用? → 自动发现模块边界
- [ ] 社区检测的参数如何调优?
- [ ] God Node 的阈值如何确定?
- [ ] 图谱规模达到什么程度性能会下降?

---

*参考: [architecture.md](../architecture.md) | [01-multimodal-extraction.md](./01-multimodal-extraction.md)*
