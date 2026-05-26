# ECC (Everything Claude Code) 学习笔记工程

> 用于系统学习 [ECC](https://github.com/affaan-m/ECC) 架构、功能与更新的知识库。

## 项目简介

**ECC** (Everything Claude Code，原名 everything-claude-code) 是一个开源的、plugin-first 的 Agent Harness 配置框架。它不是独立的 AI Agent，而是增强和优化现有 AI 编码工具（Claude Code、Cursor、Codex 等）行为的配置系统，通过 Skills、Agents、Hooks、Rules、Instincts 六大构建块让 AI 编码工具按照最佳实践自动工作。

### 核心数据

| 属性 | 值 |
|------|-----|
| 当前版本 | **v2.0.0-rc.1** |
| GitHub Stars | 182K+ |
| Forks | 28K+ |
| Contributors | 170+ |
| 作者 | Affaan Mustafa (@affaan-m)，Anthropic Hackathon 获奖者 |
| 语言 | Markdown/YAML + Node.js/TypeScript/Python/Go/Java/Perl |
| npm 包 | ecc-universal, ecc-agentshield |
| 源码 | https://github.com/affaan-m/ECC |
| 许可证 | MIT |

### 核心特点

- **Plugin-First** — Claude Code Marketplace 一键安装 (ecc@ecc)
- **六大构建块** — Agents (61) + Skills (246) + Commands (76) + Hooks + Rules (34) + Instincts
- **跨工具支持** — 10+ Harness (Claude Code, Cursor, Codex, OpenCode, Gemini CLI 等)
- **自动学习** — Continuous Learning v2 (Instincts): 观察→评分→进化
- **安全管线** — AgentShield: Red-Team/Blue-Team/Auditor (Opus 4.6, 1282 tests)
- **Token 优化** — 模型降级 + 思考控制 + 早压缩 + 工具管理 (总计 ~75% 降本)
- **DRY Adapter** — 一套配置多工具复用，不重复实现
- **ECC 2.0** — Rust 控制平面 (Dashboard GUI, Daemon, Sessions)

### 架构模式

```
Harness Layer (10+ 工具)
    │ AGENTS.md / 配置注入
    ▼
Control Plane (ECC 2.0 Rust Daemon)
    │ 调度 / 触发
    ▼
Component Layer (61 Agents + 246 Skills + 76 Commands + Hooks + 34 Rules + Instincts)
    │ 执行 / 反馈
    ▼
Security Layer (AgentShield: Red/Blue/Auditor)
    │
    ▼
Learning Layer (Continuous Learning v2: 观察 → 评分 → 进化)
```

## 学习目录

```
.
├── README.md                  # 本文件
├── architecture.md            # 架构总览 (六层模型 + 组件详解 + ECC 2.0)
├── changelog.md               # 版本更新追踪 (v1.2.0 ~ v2.0.0-rc.1)
├── references.md              # 参考资料汇总 (官方/npm/指南/社区)
├── learning-guide.md          # 学习计划指南 (六阶段路线)
└── notes/
    ├── 01-skills-system.md    # Skills 系统 (246 skills, SKILL.md 格式, 分类)
    ├── 02-hooks-architecture.md # Hook 架构 (事件类型, 运行时控制, DRY Adapter)
    ├── 03-instincts-learning.md # Instincts 学习 (CL v2, 置信度, 项目隔离, 进化)
    ├── 04-cross-harness.md    # 跨 Harness 设计 (10+ 工具, AGENTS.md, 功能对等)
    ├── 05-security-agentshield.md # AgentShield 安全 (Red/Blue/Auditor, 1282 tests)
    └── 06-token-optimization.md  # Token 优化 (模型/思考/压缩/工具, 75% 降本)
```

## 学习路线

1. **阶段一** — 理解核心概念（六大构建块 + 安装方式）
2. **阶段二** — 深入组件系统（Skills 工作流 + Hook 事件驱动）
3. **阶段三** — 学习 Instincts 系统（自动学习 + 置信度 + 进化）
4. **阶段四** — 跨 Harness 设计（DRY Adapter + AGENTS.md + 功能对等）
5. **阶段五** — Token 优化策略（模型选择 + 思考控制 + 压缩 + 工具管理）
6. **阶段六** — ECC 2.0 与 Rust 控制平面（Dashboard + Daemon + 安全管线）

详见 [learning-guide.md](./learning-guide.md)

## 学习目标

- [x] 理解 ECC 定位（Harness 配置框架 vs 独立 Agent）
- [x] 理解六大构建块的关系和职责
- [ ] 深入 Instincts 自动学习系统的机制
- [ ] 学习 Hook 机制扩展 Agent 行为
- [ ] 理解跨 Harness 兼容性设计 (DRY Adapter)
- [ ] 掌握 Token 优化实践
- [ ] 了解 ECC 2.0 Rust 控制平面架构

## 关键差异化

与其他 Harness 项目相比，ECC 的独特之处：

| 差异点 | 说明 |
|--------|------|
| Instincts (CL v2) | 唯一具有自动学习→进化能力的系统 |
| 跨 10+ 工具 | 最广泛的 Harness 支持 |
| AgentShield | 唯一的 AI 攻防安全管线 |
| 182K+ Stars | 社区规模最大 |
| Plugin-First | Marketplace 一键安装 |
| Rust 控制平面 | 进化为"配置 + 运行时"一体化 |

## 相关链接

- 源码仓库: https://github.com/affaan-m/ECC
- npm (universal): https://www.npmjs.com/package/ecc-universal
- npm (agentshield): https://www.npmjs.com/package/ecc-agentshield
- Claude Code Marketplace: `ecc@ecc`
- Releases: https://github.com/affaan-m/ECC/releases

---

*最后更新: 2026-07 | 基于 v2.0.0-rc.1*
*项目信息见: [repos/affaan-m-ECC.md](../../repos/affaan-m-ECC.md)*
