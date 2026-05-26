# MCP 工具设计

> 10 个 MCP 工具 — CodeGraph 对 AI Agent 暴露的查询接口。

## 一、概述

CodeGraph 通过 Model Context Protocol (MCP) 暴露 10 个工具，供 AI Agent 查询代码知识图谱。这些工具是 AI 与知识图谱交互的唯一接口。

### MCP 协议简介

MCP (Model Context Protocol) 是一个标准化的 AI 工具通信协议：

```
┌──────────────┐     MCP Protocol      ┌──────────────┐
│  MCP Client  │ ◀══════════════════▶  │  MCP Server  │
│  (AI Agent)  │    JSON-RPC / stdio    │  (CodeGraph) │
│              │                        │              │
│  Claude      │    tools/call          │  10 个工具    │
│  Cursor      │    tools/list          │  SQLite 查询  │
│  Windsurf    │                        │  FTS5 搜索   │
└──────────────┘                        └──────────────┘
```

### 工具分类

| 类别 | 工具 | 目的 |
|------|------|------|
| **搜索类** | `codegraph_search` | 按名称/关键词查找节点 |
| **上下文类** | `codegraph_context`, `codegraph_node` | 获取节点详细信息 |
| **图遍历类** | `codegraph_callers`, `codegraph_callees`, `codegraph_trace` | 分析调用关系 |
| **分析类** | `codegraph_impact` | 变更影响评估 |
| **探索类** | `codegraph_explore`, `codegraph_files` | 浏览项目结构 |
| **状态类** | `codegraph_status` | 查看索引状态 |

## 二、工具详解

### 2.1 codegraph_search — 搜索节点

> 入口工具。AI 首先通过搜索找到相关的代码实体。

**功能**: 使用 FTS5 全文搜索查找节点（函数、类、方法等）

**输入参数**:

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| query | string | ✅ | 搜索关键词 |
| kind | string | ❌ | 过滤节点类型: function/class/method |
| language | string | ❌ | 过滤语言 |
| limit | number | ❌ | 返回数量上限 (默认 20) |

**输出**: 匹配的节点列表 (id, name, kind, file, line, signature)

**使用场景**:
```
AI: "我需要找到处理用户认证的函数"
→ codegraph_search(query: "auth", kind: "function")
→ 返回: [authenticateUser, validateToken, checkPermission, ...]
```

### 2.2 codegraph_context — 获取上下文

> 获取节点的完整上下文，包括代码体和相关信息。

**功能**: 返回节点的完整实现代码和周围上下文

**输入参数**:

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| nodeId | string | ✅ | 节点 ID |

**输出**: 节点完整信息 (name, signature, body, file, line, callers/callees 摘要)

**使用场景**:
```
AI: "让我看看 authenticateUser 的完整实现"
→ codegraph_context(nodeId: "src/auth.ts:15:authenticateUser")
→ 返回: 完整函数代码 + 签名 + 调用关系摘要
```

### 2.3 codegraph_trace — 调用链追踪

> 追踪从一个入口点到目标的完整调用链。

**功能**: 在调用图中寻找两个节点之间的路径

**输入参数**:

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| from | string | ✅ | 起始节点 ID 或名称 |
| to | string | ✅ | 目标节点 ID 或名称 |
| maxDepth | number | ❌ | 最大追踪深度 (默认 10) |

**输出**: 调用路径列表 (每条路径: 节点序列)

**使用场景**:
```
AI: "一个 HTTP 请求如何到达数据库查询？"
→ codegraph_trace(from: "handleRequest", to: "executeQuery")
→ 返回: handleRequest → processData → dbClient.query → executeQuery
```

### 2.4 codegraph_callers — 查找调用者

> "谁调用了这个函数？" — 向上追溯。

**功能**: 查找直接调用指定节点的所有节点

**输入参数**:

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| nodeId | string | ✅ | 节点 ID |
| depth | number | ❌ | 递归深度 (默认 1，仅直接调用者) |

**输出**: 调用者列表 (id, name, file, line, call_site)

**使用场景**:
```
AI: "谁调用了 validateToken？"
→ codegraph_callers(nodeId: "src/auth.ts:30:validateToken")
→ 返回: [authMiddleware (line 12), loginHandler (line 45), ...]
```

### 2.5 codegraph_callees — 查找被调用者

> "这个函数调用了谁？" — 向下展开。

**功能**: 查找指定节点直接调用的所有节点

**输入参数**:

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| nodeId | string | ✅ | 节点 ID |
| depth | number | ❌ | 递归深度 (默认 1) |

**输出**: 被调用者列表 (id, name, file, line)

**使用场景**:
```
AI: "handleRequest 内部调用了哪些函数？"
→ codegraph_callees(nodeId: "src/server.ts:10:handleRequest")
→ 返回: [parseBody, validateInput, processRequest, sendResponse]
```

### 2.6 codegraph_impact — 影响分析

> "修改这个函数，会影响哪些代码？" — 变更影响评估。

**功能**: 递归查找所有直接或间接依赖指定节点的代码

**输入参数**:

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| nodeId | string | ✅ | 节点 ID |
| maxDepth | number | ❌ | 最大影响链深度 (默认 5) |

**输出**: 受影响的节点列表 (按深度排列，包含影响路径)

**使用场景**:
```
AI: "如果我修改 formatDate，哪些地方会受影响？"
→ codegraph_impact(nodeId: "src/utils.ts:5:formatDate")
→ 返回: 
  深度1: [renderTimestamp, displayCreatedAt]
  深度2: [UserProfile, OrderHistory]
  深度3: [DashboardPage, OrderPage]
```

### 2.7 codegraph_node — 获取节点详情

> 按 ID 精确获取单个节点的元数据。

**功能**: 返回节点的所有字段

**输入参数**:

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| nodeId | string | ✅ | 节点 ID |

**输出**: 节点完整数据 (id, name, kind, file, line, language, signature, exported)

### 2.8 codegraph_explore — 探索图结构

> 浏览代码结构，不带预设目标的开放式探索。

**功能**: 返回某个节点或文件的"邻居" — 相关的节点和边

**输入参数**:

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| nodeId | string | ❌ | 从某节点开始探索 |
| file | string | ❌ | 从某文件开始探索 |
| kind | string | ❌ | 过滤边类型 |

**输出**: 相关节点和边的子图

### 2.9 codegraph_files — 列出文件

> 查看项目文件列表和基本统计。

**功能**: 列出项目中已索引的文件

**输入参数**:

| 参数 | 类型 | 必需 | 说明 |
|------|------|------|------|
| pattern | string | ❌ | 文件路径匹配模式 (glob) |
| language | string | ❌ | 过滤语言 |

**输出**: 文件列表 (path, language, node_count, edge_count)

### 2.10 codegraph_status — 索引状态

> 查看 CodeGraph 索引的健康状态。

**功能**: 返回索引统计信息和同步状态

**输入参数**: 无

**输出**:

| 字段 | 说明 |
|------|------|
| indexed_files | 已索引文件数 |
| total_nodes | 总节点数 |
| total_edges | 总边数 |
| resolved_edges | 已解析的边数 |
| unresolved_edges | 未解析的边数 |
| last_sync | 最后同步时间 |
| watching | 是否在监听文件变更 |
| languages | 检测到的语言列表 |
| frameworks | 检测到的框架列表 |

## 三、工具组合模式

### 3.1 "理解一个函数" 模式

```
codegraph_search("functionName")     # 1. 找到目标
    → nodeId
codegraph_context(nodeId)            # 2. 获取完整实现
codegraph_callers(nodeId)            # 3. 理解使用场景
codegraph_callees(nodeId)            # 4. 理解依赖
```

### 3.2 "修改前评估" 模式

```
codegraph_search("functionToModify") # 1. 定位
    → nodeId
codegraph_impact(nodeId)             # 2. 评估影响范围
codegraph_callers(nodeId)            # 3. 确认所有使用点
codegraph_context(callerIds[])       # 4. 检查调用者的期望
```

### 3.3 "理解请求流" 模式

```
codegraph_search("routeHandler")     # 1. 找到入口
codegraph_trace(from, to)            # 2. 追踪到目标
codegraph_context(path nodes)        # 3. 逐一检查路径节点
```

### 3.4 "探索新代码库" 模式

```
codegraph_status()                   # 1. 了解项目规模
codegraph_files(pattern: "src/")     # 2. 浏览目录结构
codegraph_explore(file: "src/main")  # 3. 从入口开始探索
codegraph_search("main|entry|app")   # 4. 找关键入口点
```

## 四、Token 效率分析

### 4.1 为什么减少 71% 工具调用？

| 传统方式 (无 CodeGraph) | CodeGraph 方式 |
|------------------------|---------------|
| ① 列出目录 | ① `codegraph_search` |
| ② 读取文件 A | ② `codegraph_context` |
| ③ 看到导入，读取文件 B | (已包含在 context 中) |
| ④ 看到调用，读取文件 C | (已包含在 callers/callees 中) |
| ⑤ grep 搜索引用 | ③ `codegraph_impact` |
| ⑥ 读取搜索结果文件 D | (已包含在 impact 结果中) |
| ⑦ 读取搜索结果文件 E | — |
| **7 次工具调用** | **3 次工具调用** |

### 4.2 为什么减少 57% Token？

| 原因 | 说明 |
|------|------|
| 精准返回 | 只返回相关的节点信息，不是整个文件 |
| 签名优先 | 先给签名，需要时再给 body |
| 关系元数据 | 调用关系直接返回，不需要 AI 自己分析 |
| 无冗余 | 不会读取无关的代码 |

## 五、MCP 配置

### 5.1 Claude Desktop 配置

```json
{
  "mcpServers": {
    "codegraph": {
      "command": "npx",
      "args": ["@colbymchenry/codegraph"],
      "cwd": "/path/to/your/project"
    }
  }
}
```

### 5.2 Cursor 配置

```json
{
  "mcpServers": {
    "codegraph": {
      "command": "npx",
      "args": ["@colbymchenry/codegraph"]
    }
  }
}
```

### 5.3 程序化使用 (Library API)

```typescript
import { CodeGraph } from '@colbymchenry/codegraph';

const graph = new CodeGraph('/path/to/project');
await graph.index();  // 首次索引

// 等同于 MCP 工具调用
const results = await graph.search('handleRequest');
const context = await graph.context(results[0].id);
const impact = await graph.impact(results[0].id);
```

## 六、设计哲学

### 6.1 "少而精" 的工具设计

10 个工具覆盖了代码理解的核心需求，同时保持接口简洁：

```
搜索 (search)
    → 发现 (explore / files)
        → 理解 (context / node)
            → 分析 (callers / callees / trace / impact)
                → 监控 (status)
```

### 6.2 "渐进式披露" 原则

- `search` 返回摘要 (name + signature)
- `context` 返回完整实现 (body)
- `impact` 返回深层依赖 (recursive)

AI 可以逐步深入，不需要一次获取所有信息。

### 6.3 "图思维" vs "文件思维"

| 文件思维 | 图思维 (CodeGraph) |
|----------|-------------------|
| "打开 auth.ts" | "找到 authenticate 函数" |
| "搜索 grep" | "查找调用者" |
| "读取整个文件" | "获取函数上下文" |
| "人工追踪导入链" | "调用链追踪" |

## 七、待深入研究

- [ ] 各工具的 JSON Schema 定义
- [ ] 工具调用的错误处理和降级策略
- [ ] 大型项目中查询结果的分页策略
- [ ] 未解析边 (unresolved edges) 对工具输出的影响
- [ ] MCP Server 的启动/停止生命周期管理

---

*前置阅读: [02-knowledge-graph.md](./02-knowledge-graph.md)*
*后续阅读: [04-framework-routes.md](./04-framework-routes.md)*
*最后更新: 2026-05-24*
