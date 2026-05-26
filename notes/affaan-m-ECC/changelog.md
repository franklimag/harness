# ECC 版本更新追踪

> 跟踪 ECC (Everything Claude Code) 的版本发布、重要更新和里程碑事件。

## 版本命名规则

ECC 使用语义化版本号（如 `v1.2.0`、`v2.0.0-rc.1`）。
发布节奏: 约每月一个次版本，2026 年 2-4 月为高密度迭代期。

## 版本历史

### v2.0.0 系列 (2026-04+)

#### ⭐ v2.0.0-rc.1 (Apr 2026) — 最新版本

重大架构升级，引入 Rust 控制平面和 GUI Dashboard：

| 特性 | 说明 |
|------|------|
| Dashboard GUI | 可视化系统仪表板 |
| Operator Workflows | 操作员工作流编排 |
| Rust Control-Plane (alpha) | Rust 编写的守护进程控制平面 |
| 61 Agents | 从前版本大幅增加到 61 个专业 Agent |
| 246 Skills | 技能数量达到 246 个 |
| Daemon 管理 | start / stop / resume / sessions / status |

#### 核心 CLI 新增

```bash
ecc dashboard    # 启动 GUI 仪表板
ecc start        # 启动会话
ecc sessions     # 列出会话
ecc status       # 系统状态
ecc stop         # 停止会话
ecc resume       # 恢复会话
ecc daemon       # 守护进程管理
```

---

### v1.9.0 (Mar 2026) — Selective Install Architecture

选择性安装架构，大幅扩展语言生态支持：

| 特性 | 说明 |
|------|------|
| Manifest-Driven Install | 清单驱动的选择性安装 (install-plan.js + install-apply.js) |
| 6 New Agents | 新增 6 个专业 Agent |
| 12 Language Ecosystems | 支持 12 个语言生态系统 (TypeScript, Python, Go, Java, Swift, PHP, ArkTS 等) |
| Install Wizard | 交互式安装向导 |

#### 安装管线

```
install-plan.js (分析环境) → 生成安装清单 → install-apply.js (执行安装)
```

---

### v1.8.0 (Mar 2026) — Harness Performance System

性能系统和测试大幅增强：

| 特性 | 说明 |
|------|------|
| Harness Performance System | Agent Harness 性能评测系统 |
| NanoClaw v2 | NanoClaw 第二代 |
| 997 Tests | 测试数量达到 997 个 |
| Performance Benchmarks | 标准化性能基准 |

---

### v1.7.0 (Feb 2026) — Cross-Platform Expansion

跨平台大扩展：

| 特性 | 说明 |
|------|------|
| Cross-Platform | 跨平台扩展支持 |
| Cursor IDE Support | Cursor IDE 完整适配 (DRY Adapter Pattern) |
| Presentations | 演示/报告能力 |
| DRY Adapter | 复用 Claude Code Hook 脚本的适配器模式 |

#### Cursor 适配方式

```
Cursor ──(DRY Adapter)──▶ Claude Code Hook Scripts
         不重写，直接复用
```

---

### v1.6.0 (Feb 2026) — AgentShield & Marketplace

安全系统和发布渠道重大更新：

| 特性 | 说明 |
|------|------|
| Codex CLI Support | 支持 Codex 命令行工具 (macOS + CLI) |
| AgentShield | Red-Team / Blue-Team / Auditor 安全管线 |
| GitHub Marketplace | 上线 GitHub Marketplace (ecc@ecc) |
| npm: ecc-agentshield | AgentShield 独立 npm 包 |

#### AgentShield 管线

```
Red Team (攻击模拟) → Blue Team (防御验证) → Auditor (审计报告)
                      全程 Opus 4.6 驱动
                      1282 Tests / 102 Static Rules
```

---

### v1.4.0 (Feb 2026) — Multi-Language Rules

多语言规则和运维增强：

| 特性 | 说明 |
|------|------|
| Multi-Language Rules | 多语言规则体系 (TypeScript, Python, Go, Swift, PHP, ArkTS) |
| Installation Wizard | 安装向导 |
| PM2 Support | PM2 进程管理支持 |
| 34 Rules | 规则数量达到 34 条 |

#### 规则组织

```
rules/
├── common/         # 通用 (代码质量/文档/Git)
├── typescript/     # TypeScript 专用
├── python/         # Python 专用
├── golang/         # Go 专用
├── swift/          # Swift 专用
├── php/            # PHP 专用
└── arkts/          # ArkTS 专用
```

---

### v1.3.0 (Feb 2026) — OpenCode Plugin

| 特性 | 说明 |
|------|------|
| OpenCode Plugin Support | OpenCode 插件支持 (11 事件类型) |
| Extended Hooks | Hook 事件扩展 |

---

### v1.2.0 (Feb 2026) — Unified Commands & CL v2

统一命令和持续学习系统重大升级：

| 特性 | 说明 |
|------|------|
| Unified Commands | 统一命令体系 (76 个斜杠命令) |
| Continuous Learning v2 | 持续学习第二代 (Instincts 系统) |
| Django Support | Django 框架支持 |
| Spring Boot Support | Spring Boot 框架支持 |

#### Continuous Learning v2 核心

```
Session 观察 → 原子直觉创建 → 置信度评分 → 进化为 Skill/Command/Agent
                              项目级隔离，防止跨项目污染
```

---

## 里程碑事件

| 时间 | 事件 |
|------|------|
| — | Affaan Mustafa 获得 Anthropic Hackathon 奖 |
| — | 项目从 everything-claude-code 更名为 ECC |
| Feb 2026 | v1.2.0 — 持续学习 v2 发布 |
| Feb 2026 | v1.3.0 — OpenCode 生态扩展 |
| Feb 2026 | v1.4.0 — 多语言规则体系建立 |
| Feb 2026 | v1.6.0 — AgentShield 安全管线 + GitHub Marketplace |
| Feb 2026 | v1.7.0 — 跨平台扩展 (Cursor 适配) |
| Mar 2026 | v1.8.0 — Harness Performance System + 997 Tests |
| Mar 2026 | v1.9.0 — Selective Install + 12 语言生态 |
| Apr 2026 | v2.0.0-rc.1 — Rust Control-Plane + Dashboard GUI |
| — | GitHub Stars 突破 182K |
| — | Contributors 超过 170 人 |
| — | Forks 超过 28K |

## 版本演进趋势

```
v1.2 ─── Commands/CL    ──┐
v1.3 ─── OpenCode        ──┤
v1.4 ─── Multi-Lang      ──┤   功能积累期
v1.6 ─── Security/Market ──┤   (Feb 2026)
v1.7 ─── Cross-Platform  ──┘
                              ← 分水岭
v1.8 ─── Performance     ──┐
v1.9 ─── Selective Install──┤   架构升级期
v2.0 ─── Rust/Dashboard  ──┘   (Mar-Apr 2026)
```

## 关注的功能方向

- [x] 持续学习系统 (v1.2.0 CL v2)
- [x] 跨平台支持 (v1.7.0 Cursor/Codex/OpenCode)
- [x] 安全管线 (v1.6.0 AgentShield)
- [x] 性能评测 (v1.8.0 Harness Performance System)
- [x] 选择性安装 (v1.9.0 Manifest-Driven)
- [x] GUI Dashboard (v2.0.0-rc.1)
- [x] Rust 控制平面 (v2.0.0-rc.1 alpha)
- [ ] v2.0.0 正式版发布
- [ ] 更多 Harness 适配
- [ ] Instincts → Agent 自动进化成熟
- [ ] ECC 2.0 控制平面稳定化

## 如何追踪更新

1. **GitHub Releases**: https://github.com/affaan-m/ECC/releases
2. **CHANGELOG.md**: https://github.com/affaan-m/ECC/blob/main/CHANGELOG.md
3. **npm**: `npm info ecc-universal` / `npm info ecc-agentshield`
4. **社区**: GitHub Issues / Discussions

---

*最后更新: 2026-07*
