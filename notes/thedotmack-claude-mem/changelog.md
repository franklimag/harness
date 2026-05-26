# claude-mem 版本更新追踪

> 跟踪 claude-mem 的版本发布、架构演进和 Agent 支持扩展。

## 项目概况

claude-mem 是 Agent 记忆领域最大的开源项目 (72K+ Stars)，经历了从 PM2 到 Bun 的重大架构迁移。

## 核心里程碑

### 早期 — 基础记忆系统

| 功能 | 说明 |
|------|------|
| 基础 Hook | Session 事件捕获 |
| SQLite 存储 | 记忆持久化 |
| Claude Code 支持 | 首个目标 Agent |
| PM2 Worker | 后台进程管理 |

### 架构升级 — PM2 → Bun

| 维度 | 变化 | 原因 |
|------|------|------|
| 运行时 | Node.js → Bun | 性能和启动速度 |
| 进程管理 | PM2 → Bun 原生 | 简化依赖 |
| TypeScript | 编译 → 原生 | 开发效率 |
| 包管理 | npm → Bun 内置 | 安装速度 |

### 搜索增强 — FTS5 + ChromaDB

| 功能 | 说明 |
|------|------|
| FTS5 | SQLite 全文搜索 |
| ChromaDB | 可选语义向量搜索 |
| 10 API 端点 | 完整搜索接口 |
| 渐进式披露 | mem-search Skill |

### Agent 扩展 — 7+ 支持

```
Phase 1: Claude Code Only
    │
    ▼
Phase 2: + OpenClaw + Codex
    │
    ▼
Phase 3: + Gemini + Hermes
    │
    ▼
Phase 4: + Copilot + OpenCode
    │
    ▼
当前: 7+ Agents
```

### 当前 — 成熟系统

| 特性 | 状态 |
|------|------|
| 5 阶段 Hook | ✅ 稳定 |
| Worker Service | ✅ Bun Runtime |
| SQLite + FTS5 | ✅ 稳定 |
| ChromaDB | ✅ 可选 |
| Claude Agent SDK | ✅ 压缩引擎 |
| 7+ Agent 支持 | ✅ |
| Token 预算管理 | ✅ |
| Subagent 注入控制 | ✅ (Issue #1464) |

## 社区增长

| 指标 | 值 |
|------|-----|
| Stars | 72K+ |
| 支持 Agent | 7+ |

## 关键设计决策时间线

| 决策 | 原因 |
|------|------|
| TypeScript + Bun | 高性能 JS/TS 运行时 |
| SQLite (非 PostgreSQL) | 零配置，可嵌入 |
| FTS5 (非 Elasticsearch) | 轻量，够用 |
| ChromaDB (可选) | 语义搜索增强 |
| Claude Agent SDK | 压缩质量最好 |
| Hook 生命周期 | 完整覆盖 Session |

## 关注的未来方向

- [ ] 更多 Agent 支持
- [ ] 记忆共享（团队/组织级）
- [ ] 更智能的遗忘策略
- [ ] 压缩质量评估框架
- [ ] 记忆版本控制
- [ ] 多设备同步

---

*最后更新: 2026-07*
