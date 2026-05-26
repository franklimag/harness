# CodeGraph 版本更新追踪

> 跟踪 CodeGraph 的版本发布、重要更新和技术演进。

## 版本命名规则

CodeGraph 使用标准 SemVer 格式 (`vX.Y.Z`)。当前仍处于 0.x 阶段，接口可能有 breaking changes。

## 版本历史

### v0.9.x 系列（当前）

#### ⭐ v0.9.4 (2026-05-24) — 最新版本 🆕

- **Benchmark 重新验证**: 在 7 个代码库上重新跑通完整 benchmark
- **性能数据更新**:
  - 平均成本降低 35%
  - Token 消耗减少 57%
  - 响应速度提升 46%
  - 工具调用减少 71%
- **测试覆盖**: VS Code, Excalidraw, Django, Tokio, OkHttp, Gin, Alamofire
- **关键结论**: 收益随代码库规模增长而扩大

#### v0.9.2 (2026-05 中旬)

- **Hermes Agent 支持**: 集成 NousResearch Hermes Agent 作为可选推理后端
- **Drupal 8-11 框架检测**: 新增 Drupal 8/9/10/11 的路由和控制器检测
  - 支持 `*.routing.yml` 解析
  - 支持 Controller 注解识别
- **框架检测扩展**: 框架总数增至 14 个

#### ⭐ v0.9.0 (2026-05 初) — 里程碑版本

> "No more database is locked, no more native build failures, no more WASM fallback active"

这是 CodeGraph 的一个重大稳定性版本，解决了长期困扰用户的三大问题：

| 问题 | 解决方案 |
|------|----------|
| "database is locked" 错误 | SQLite WAL 模式 |
| Native build 失败 | 捆绑 Node.js 运行时 |
| WASM fallback 性能差 | 无需 WASM，直接用原生 Node.js |

**主要变更:**

1. **捆绑 Node.js 运行时 (Bundled Runtime)**
   - CodeGraph 现在自带 Node.js，不再依赖用户系统的 Node 版本
   - 避免了 native SQLite 模块编译失败的问题
   - 不再需要回退到性能较差的 WASM 实现

2. **WAL 模式 (Write-Ahead Logging)**
   - SQLite 切换到 WAL 模式
   - 读操作不再被写操作阻塞
   - 彻底消除 "database is locked" 错误
   - Auto-Sync 写入不影响 MCP 查询

3. **稳定性全面提升**
   - 文件监听更可靠
   - 错误恢复更完善
   - 首次索引体验更流畅

### v0.8.x 系列（推测）

> 注: v0.8.x 的具体版本记录暂未公开获取，以下基于已知信息推测。

- 初始 MCP Server 实现
- Tree-sitter 多语言支持扩展
- 基础框架路由检测
- SQLite 存储引擎（非 WAL 模式）
- 可能存在 native build 和 WASM fallback 问题

## 里程碑事件

| 时间 | 事件 |
|------|------|
| 2026-05 初 | v0.9.0 发布 — 捆绑运行时 + WAL 模式，稳定性飞跃 |
| 2026-05 中旬 | v0.9.2 发布 — Hermes Agent + Drupal 支持 |
| 2026-05-24 | v0.9.4 发布 — Benchmark 验证，7 大代码库测试 |
| — | 学术论文 arxiv:2603.27277 引用 CodeGraph 概念 |
| — | tokensave (Rust 移植版) 开始开发 |

## 关键技术演进

```
早期阶段
├── 基础 Tree-sitter 解析
├── SQLite 存储 (标准模式)
├── 依赖用户 Node.js 环境
└── 可能回退到 WASM

v0.9.0 (稳定性版本)
├── 捆绑 Node.js 运行时 (自包含)
├── SQLite WAL 模式
├── 消除 "database is locked"
└── 消除 native build 失败

v0.9.2 (生态扩展)
├── Hermes Agent 集成
├── Drupal 8-11 支持
└── 14 框架全覆盖

v0.9.4 (验证版本)
├── 7 代码库 Benchmark
├── 量化性能收益
└── 规模效应验证
```

## 版本对比

| 版本 | 核心变化 | 稳定性 | 框架数 | 备注 |
|------|----------|--------|--------|------|
| < 0.9.0 | 基础功能 | ⚠️ 有已知问题 | < 14 | WASM fallback |
| v0.9.0 | 捆绑运行时 + WAL | ✅ 稳定 | ~12 | 里程碑 |
| v0.9.2 | Hermes + Drupal | ✅ 稳定 | 14 | 生态扩展 |
| v0.9.4 | Benchmark 验证 | ✅ 稳定 | 14 | 当前推荐 |

## 关注的功能方向

- [ ] v1.0 正式发布（何时 API 稳定？）
- [ ] 更多语言支持（Zig? Gleam? Haskell?）
- [ ] 更多框架检测（Next.js App Router? Remix?）
- [ ] 分布式/团队共享 codegraph
- [ ] VS Code 插件深度集成
- [ ] tokensave (Rust 版) 的功能对齐

## 如何追踪更新

1. **GitHub Releases**: https://github.com/colbymchenry/codegraph/releases
2. **npm**: https://www.npmjs.com/package/@colbymchenry/codegraph
3. **GitHub Issues/Discussions**: https://github.com/colbymchenry/codegraph/issues

---

*最后更新: 2026-05-24*
