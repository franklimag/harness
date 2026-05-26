# CodeGraph 架构总览

> 基于 GitHub 仓库 (colbymchenry/codegraph) 和官方文档整理。最后更新: 2026-05-24 (v0.9.4)

## 零、项目基本信息

| 属性 | 值 |
|------|-----|
| 仓库 | https://github.com/colbymchenry/codegraph |
| 作者 | Colby McHenry |
| 语言 | TypeScript (自带 Node.js 运行时) |
| 许可 | MIT |
| npm 包 | @colbymchenry/codegraph |
| 当前版本 | **v0.9.4** (2026-05-24) |
| 安装方式 | `npx @colbymchenry/codegraph` |
| 存储位置 | `.codegraph/codegraph.db` (SQLite) |
| 设计理念 | 零配置 (Zero-config)，无需配置文件 |
| 支持语言 | 20+ (通过 Tree-sitter) |

## 一、整体架构

CodeGraph 是一个 **预索引代码知识图谱** 工具。它通过 Tree-sitter 解析源代码为 AST，提取节点和边，存储到本地 SQLite 数据库，再通过 MCP Server 暴露给 AI Agent 使用。

### 四阶段流水线

```
┌──────────────────────────────────────────────────────────────────────────┐
│                     CodeGraph 四阶段架构                                  │
│                                                                          │
│  ┌─────────────┐    ┌─────────────┐    ┌──────────────┐    ┌─────────┐ │
│  │  Extraction │───▶│   Storage   │───▶│  Resolution  │───▶│  Sync   │ │
│  │  (提取阶段)  │    │  (存储阶段)  │    │  (解析阶段)   │    │ (同步)   │ │
│  └─────────────┘    └─────────────┘    └──────────────┘    └─────────┘ │
│        │                   │                  │                  │       │
│   Tree-sitter         SQLite +          函数调用→定义        FSEvents    │
│   AST 解析            FTS5 索引          导入→源文件         inotify     │
│   语言查询             节点/边存储        类继承关系          ReadDir     │
│                                          框架模式识别                     │
└──────────────────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌──────────────────────────────────────────────────────────────────────────┐
│                    MCP Server (工具暴露层)                                │
│                                                                          │
│  codegraph_search | codegraph_context | codegraph_trace                  │
│  codegraph_callers | codegraph_callees | codegraph_impact                │
│  codegraph_node | codegraph_explore | codegraph_files | codegraph_status │
│                                                                          │
│  → AI Agent (Claude / GPT / Cursor / Windsurf / 其他 MCP 客户端)         │
└──────────────────────────────────────────────────────────────────────────┘
```

### 架构层次图

```
┌─────────────────────────────────────────────────────────────┐
│                Source Code (源代码层)                         │
│  20+ 语言: TypeScript, Python, Go, Rust, Swift, Java...     │
│  14 框架: Django, Express, NestJS, Rails, Spring...          │
└───────────────────────────┬─────────────────────────────────┘
                            │ Tree-sitter 解析
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              Extraction Engine (提取引擎)                     │
│  Tree-sitter Parsers / Language-specific Queries             │
│  节点提取: functions, classes, methods, modules              │
│  边提取: calls, imports, extends, implements                 │
└───────────────────────────┬─────────────────────────────────┘
                            │ 写入
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              Storage Layer (存储层)                           │
│  SQLite (.codegraph/codegraph.db) + WAL 模式                 │
│  FTS5 全文搜索索引                                           │
│  节点表 + 边表 + 文件元数据                                   │
└───────────────────────────┬─────────────────────────────────┘
                            │ 查询/解析
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              Resolution Phase (解析阶段)                      │
│  函数调用 → 定义 (call → definition)                         │
│  导入语句 → 源文件 (import → source file)                    │
│  类继承: extends / implements                                │
│  框架模式: 路由、中间件、控制器                               │
└───────────────────────────┬─────────────────────────────────┘
                            │ 暴露
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              MCP Server (协议层)                              │
│  10 个工具 / Model Context Protocol                          │
│  供 AI Agent 调用: 搜索、追踪、影响分析                       │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              Auto-Sync (自动同步层)                           │
│  OS 原生文件事件监听                                         │
│  FSEvents (macOS) / inotify (Linux) / ReadDirectoryChangesW │
│  2s 防抖 → 增量更新 → 无需手动重建                           │
└─────────────────────────────────────────────────────────────┘
```

## 二、核心组件详解

### 2.1 Extraction Engine（提取引擎）

提取引擎是 CodeGraph 的入口 — 使用 Tree-sitter 将源代码解析为 AST，再通过语言特定查询提取知识图谱节点和边。

#### 工作流程

```
源代码文件 (.ts, .py, .go, ...)
    │
    ▼
Tree-sitter Parser (语言对应的 parser)
    │ 生成 AST (抽象语法树)
    ▼
Language-specific Queries (语言查询)
    │
    ├── 节点提取 (Nodes)
    │   ├── functions (函数)
    │   ├── classes (类)
    │   ├── methods (方法)
    │   ├── modules (模块)
    │   └── interfaces/types (接口/类型)
    │
    └── 边提取 (Edges)
        ├── calls (函数调用)
        ├── imports (导入关系)
        ├── extends (继承)
        └── implements (实现)
```

#### 支持的 20+ 语言

| 类别 | 语言 |
|------|------|
| 前端 | TypeScript, JavaScript, JSX/TSX, CSS |
| 后端 | Python, Go, Rust, Java, C#, Ruby, PHP |
| 移动端 | Swift, Kotlin, Objective-C, Dart |
| 系统 | C, C++ |
| 其他 | Elixir, Scala, Lua, Zig |

### 2.2 Storage Layer（存储层）

#### SQLite + WAL 模式

CodeGraph 使用 SQLite 作为本地存储引擎，配合 WAL (Write-Ahead Logging) 模式确保并发安全：

| 特性 | 说明 |
|------|------|
| 数据库路径 | `.codegraph/codegraph.db` |
| 写入模式 | WAL (v0.9.0 引入) |
| 全文搜索 | FTS5 索引 |
| 并发控制 | 无 "database is locked" 问题 |
| 自带运行时 | 捆绑 Node.js，无需本地编译 native 模块 |

#### 数据模型

```
┌─────────────────────────────────┐
│           Nodes (节点)           │
│─────────────────────────────────│
│ id: 唯一标识                     │
│ name: 名称                       │
│ kind: function/class/method/...  │
│ file: 所在文件                   │
│ line: 行号                       │
│ language: 语言                   │
│ signature: 签名                  │
│ body: 代码体 (可选)              │
└─────────────────────────────────┘

┌─────────────────────────────────┐
│           Edges (边)             │
│─────────────────────────────────│
│ source: 源节点 ID                │
│ target: 目标节点 ID              │
│ kind: calls/imports/extends/...  │
│ file: 发生位置                   │
│ line: 行号                       │
└─────────────────────────────────┘
```

#### FTS5 全文搜索

- 对节点名称、签名、文件路径建立 FTS5 索引
- 支持模糊搜索和前缀匹配
- 查询响应极快（毫秒级）

### 2.3 Resolution Phase（解析阶段）

解析阶段将"松散"的边（如：文件 A 中调用了 `foo()`）解析为"精确"的边（指向 `foo` 的确切定义位置）。

#### 解析策略

| 解析类型 | 输入 | 输出 |
|----------|------|------|
| 函数调用解析 | `foo()` 调用 | → `foo` 函数定义所在文件:行号 |
| 导入解析 | `import { X } from './module'` | → 源文件中 `X` 的导出位置 |
| 类继承解析 | `class B extends A` | → `A` 类定义 |
| 接口实现 | `class C implements I` | → `I` 接口定义 |
| 框架模式 | `@Get('/users')` | → 路由处理函数 |

#### 框架模式识别

Resolution 阶段还能识别 **14 个框架** 的特定模式：

```
Django       → urls.py path() / include() → view 函数
Flask        → @app.route() → handler
FastAPI      → @router.get() → endpoint 函数
Express      → router.get() → middleware chain
NestJS       → @Controller() + @Get() → method
Laravel      → Route::get() → controller@method
Drupal       → *.routing.yml → controller
Rails        → routes.rb → controller#action
Spring       → @RequestMapping → handler method
Gin/chi/gorilla → r.GET() → handler func
Axum/actix/Rocket → .route() / #[get()] → handler
ASP.NET      → [HttpGet] → action method
Vapor        → app.get() → handler
React Router/SvelteKit → 文件系统路由
```

### 2.4 Auto-Sync（自动同步层）

#### 原生 OS 文件事件

| 平台 | 事件 API | 说明 |
|------|----------|------|
| macOS | FSEvents | Apple 原生文件系统事件 |
| Linux | inotify | 内核级文件监听 |
| Windows | ReadDirectoryChangesW | Win32 API |

#### 同步机制

```
文件变更事件 (创建/修改/删除)
    │
    ▼
事件收集 (缓冲区)
    │ 2 秒防抖 (debounce)
    ▼
增量更新处理
    │
    ├── 新文件 → 提取节点/边 → 写入 DB
    ├── 修改文件 → 删除旧记录 → 重新提取 → 写入
    └── 删除文件 → 删除相关节点/边
    │
    ▼
Resolution 重新运行 (受影响的边)
    │
    ▼
数据库更新完成 (无需手动重建)
```

#### 设计要点

- **防抖 2s**: 避免频繁文件保存触发大量重建
- **增量更新**: 只处理变更文件，不重建整个图
- **无需重启**: MCP Server 持续运行，数据库实时可查
- **WAL 模式**: 读写不互斥，查询不阻塞同步

### 2.5 MCP Server（协议层）

MCP Server 是 CodeGraph 对外暴露知识图谱的接口层，遵循 Model Context Protocol 标准。

#### 10 个 MCP 工具

| 工具 | 功能 | 典型场景 |
|------|------|----------|
| `codegraph_search` | 搜索节点（函数/类/方法） | AI 查找相关代码 |
| `codegraph_context` | 获取节点完整上下文 | 理解函数完整签名和实现 |
| `codegraph_trace` | 调用链追踪 | 分析请求从入口到底层的完整路径 |
| `codegraph_callers` | 查找调用者 | "谁调用了这个函数？" |
| `codegraph_callees` | 查找被调用者 | "这个函数调用了谁？" |
| `codegraph_impact` | 影响分析 | "修改这个函数会影响哪些地方？" |
| `codegraph_node` | 获取单个节点详情 | 按 ID 精确查询 |
| `codegraph_explore` | 探索图结构 | 浏览代码结构和关系 |
| `codegraph_files` | 列出文件 | 查看项目文件列表 |
| `codegraph_status` | 索引状态 | 查看索引是否完成、节点/边数量 |

#### MCP 集成方式

CodeGraph MCP Server 兼容所有 MCP 客户端：

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

### 2.6 Mixed iOS/RN/Expo Bridging（跨平台桥接）

CodeGraph 特别支持混合移动应用的代码图谱构建：

| 桥接类型 | 说明 |
|----------|------|
| Swift ↔ ObjC | Swift 和 Objective-C 互调 |
| RN Legacy Bridge | React Native 旧桥接 (NativeModules) |
| TurboModules | React Native 新架构 (JSI) |
| Fabric | React Native 新渲染系统 |
| Expo Modules | Expo 模块系统 |

这意味着 CodeGraph 可以正确解析像这样的调用链：

```
React Native JS → TurboModule → Swift/ObjC → iOS Framework
```

## 三、数据流（完整索引生命周期）

```
项目根目录下执行 npx @colbymchenry/codegraph
    │
    ▼
扫描项目文件 (过滤 .gitignore 等)
    │ 识别语言、框架
    ▼
阶段一: Extraction (提取)
    │ Tree-sitter 逐文件解析
    │ 语言查询提取 nodes + edges
    ▼
阶段二: Storage (存储)
    │ 创建 .codegraph/codegraph.db
    │ 写入节点表、边表
    │ 建立 FTS5 索引
    ▼
阶段三: Resolution (解析)
    │ 遍历所有边
    │ 函数调用 → 定义位置
    │ 导入 → 源文件
    │ 继承/实现 → 类/接口定义
    │ 框架路由 → 处理函数
    ▼
阶段四: Auto-Sync (启动文件监听)
    │ OS 原生 file watcher
    │ 2s 防抖 → 增量更新
    ▼
MCP Server 就绪
    │ AI Agent 可通过 10 个工具查询知识图谱
    ▼
持续运行
    ├── 文件变更 → 增量同步
    ├── AI 查询 → 即时响应
    └── 无需手动重建
```

## 四、关键设计原则

1. **零配置 (Zero-config)**: 无需任何配置文件，开箱即用
2. **预索引**: 提前构建知识图谱，查询时零延迟
3. **增量同步**: 文件变更自动检测，只更新变更部分
4. **本地存储**: 所有数据在本地 SQLite，无需外部服务
5. **自带运行时**: 捆绑 Node.js，避免 native build 失败和 WASM fallback
6. **WAL 模式**: 读写分离，查询不阻塞写入
7. **框架感知**: 不仅理解语言语法，还理解框架约定
8. **MCP 标准**: 遵循 Model Context Protocol，兼容所有 MCP 客户端
9. **Token 高效**: 减少 AI 需要的 token 消耗（平均 57% 更少）

## 五、性能基准 (v0.9.4 Benchmark)

| 指标 | 改善幅度 | 说明 |
|------|----------|------|
| 成本 | **-35%** | API 调用费用降低 |
| Token 消耗 | **-57%** | 更少的上下文传递 |
| 响应速度 | **+46%** | 更快完成任务 |
| 工具调用次数 | **-71%** | 更少的探索步骤 |

#### 测试代码库

| 代码库 | 语言 | 领域 |
|--------|------|------|
| VS Code | TypeScript | 编辑器 |
| Excalidraw | TypeScript | 绘图工具 |
| Django | Python | Web 框架 |
| Tokio | Rust | 异步运行时 |
| OkHttp | Java/Kotlin | HTTP 客户端 |
| Gin | Go | Web 框架 |
| Alamofire | Swift | 网络库 |

> 关键发现: **代码库越大，收益越显著** — 大型项目中 AI 不再需要盲目探索文件结构。

## 六、与同类工具对比

| 维度 | CodeGraph | Graphify | CartographAI | 传统 LSP |
|------|-----------|----------|--------------|----------|
| 解析器 | Tree-sitter | Tree-sitter | Tree-sitter | 各语言独立 |
| 存储 | SQLite + FTS5 | 内存 | SQLite | 无持久化 |
| 同步 | 原生 OS 事件 | 手动 | 手动 | 实时 |
| MCP 工具 | 10 个 | N/A | 类似 | 无 |
| 框架感知 | 14 框架 | 无 | 部分 | 有限 |
| 配置 | 零配置 | 需配置 | 需配置 | IDE 集成 |
| 运行时依赖 | 自带 Node.js | 需要 Node.js | 需要 Python | 各语言 |
| 跨平台桥接 | Swift/ObjC/RN/Expo | 无 | 无 | 部分 |

## 七、Library API（程序化使用）

除了 MCP Server，CodeGraph 还提供 TypeScript 库 API：

```typescript
import { CodeGraph } from '@colbymchenry/codegraph';

// 初始化
const graph = new CodeGraph('/path/to/project');

// 搜索节点
const results = await graph.search('handleRequest');

// 获取调用者
const callers = await graph.callers(nodeId);

// 影响分析
const impact = await graph.impact(nodeId);
```

## 八、待深入研究

- [ ] Tree-sitter 查询语法 (S-expressions) 的完整规范
- [ ] SQLite WAL 模式下的并发限制
- [ ] 框架路由解析的具体 query 实现
- [ ] Resolution 阶段的精确匹配 vs 启发式匹配
- [ ] Auto-Sync 在超大型 monorepo 中的性能表现
- [ ] Library API 的完整类型定义
- [ ] iOS 桥接 (TurboModules/Fabric) 的具体解析逻辑

---

*参考来源见 [references.md](./references.md)*
*最后更新: 2026-05-24*
