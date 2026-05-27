# obra/superpowers 学习笔记工程

> 用于系统学习 [superpowers](https://github.com/obra/superpowers) 架构、工作流方法论与 Skills 系统的知识库。

## Tracking Snapshot

| 字段 | 值 |
|------|-----|
| 追踪分支 | `main` |
| 最后同步 commit | `f2cbfbefebbfef77321e4c9abc9e949826bea9d7` |
| 同步日期 | 2026-05-26 |
| 对应版本 | _N/A_ |

## 项目简介

**obra/superpowers** 是由 Jesse Vincent (@obra) 开发的**完整软件开发方法论**。不同于其他 Skills 集合，superpowers 不仅提供技能，更强制执行一套经过验证的开发工作流：从头脑风暴到实现到审查的完整流程。

### 核心数据

| 属性 | 值 |
|------|-----|
| GitHub Stars | 57K+ |
| 作者 | Jesse Vincent (@obra) |
| 文档 | https://obra-superpowers.mintlify.app/ |
| PyPI | agent-superpowers |
| 标准 | agentskills.io |
| 许可证 | MIT |
| 源码 | https://github.com/obra/superpowers |

### 支持的 Harnesses

| Harness | 支持状态 |
|---------|---------|
| Claude Code | ✅ 完整支持 |
| Codex CLI/App | ✅ 完整支持 |
| Gemini CLI | ✅ 完整支持 |
| OpenCode | ✅ 完整支持 |
| Cursor | ✅ 完整支持 |
| GitHub Copilot CLI | ✅ 完整支持 |
| Factory Droid | ✅ 完整支持 |

### 核心概念

> superpowers 是一个**方法论 (Methodology)**，不仅仅是技能集合。

```
其他 Skills 项目: "这是工具，你想怎么用就怎么用"
superpowers: "这是完整方法论，按照这个流程走，保证高质量产出"
```

### 六阶段开发工作流

```
┌──────────────┐    ┌──────────────┐    ┌──────────────┐
│ 1. Brainstorm│───▶│ 2. Specify   │───▶│  3. Plan     │
│   头脑风暴    │    │   规格化      │    │   规划       │
│  不急着写代码  │    │  可消化的块    │    │ Junior 友好  │
└──────────────┘    └──────────────┘    └──────────────┘
                                              │
┌──────────────┐    ┌──────────────┐          │
│  6. Finish   │◀───│  5. Review   │◀─────────┘
│   完成       │    │   审查       │    ┌──────────────┐
│  完成分支     │    │  检查子Agent  │◀───│4. Implement  │
└──────────────┘    └──────────────┘    │   实现       │
                                        │ Subagent 驱动│
                                        └──────────────┘
```

## 学习目录

```
.
├── README.md                  # 本文件
├── architecture.md            # 架构总览 (六阶段工作流 + Skills 系统)
├── changelog.md               # 版本更新追踪
├── references.md              # 参考资料汇总
├── learning-guide.md          # 学习计划指南 (四阶段路线)
└── notes/
    ├── 01-workflow-methodology.md  # 六阶段开发工作流
    ├── 02-subagent-driven-development.md  # Subagent 驱动开发
    ├── 03-skills-system.md    # Skills 自动触发与 SKILL.md 格式
    └── 04-multi-harness-install.md  # 跨 8+ Harness 安装
```

## 学习路线

1. **阶段一** — 理解方法论定位（为什么是"方法论"而非"工具"）
2. **阶段二** — 掌握六阶段工作流的每个阶段
3. **阶段三** — 理解 Subagent-Driven-Development 模式
4. **阶段四** — 学习 Skills 自动触发和多 Harness 安装

详见 [learning-guide.md](./learning-guide.md)

## 学习目标

- [x] 理解 superpowers 的方法论定位
- [ ] 掌握六阶段工作流的完整流程
- [ ] 理解 Brainstorming 的"不急着写代码"哲学
- [ ] 学习 Subagent-Driven-Development 模式
- [ ] 理解 Skills 自动触发机制
- [ ] 掌握跨 8+ Harness 安装方式
- [ ] 对比 superpowers vs ECC vs mattpocock/skills

## 关键差异化

| 差异点 | 说明 |
|--------|------|
| 方法论优先 | 不是工具箱，是完整流程 |
| 强制工作流 | 不跳步骤，按顺序执行 |
| Subagent 驱动 | 子 Agent 自主执行任务 |
| Junior-Friendly Plan | 计划书新手可读 |
| TDD + YAGNI + DRY | 三大原则内置 |
| 多阶段审查 | 实现后强制 Review |

## 相关链接

- 源码仓库: https://github.com/obra/superpowers
- 文档: https://obra-superpowers.mintlify.app/
- PyPI: agent-superpowers
- 标准: agentskills.io
- 安装: `/plugin install superpowers@claude-plugins-official`

---

*最后更新: 2026-07*
*项目信息见: [repos/obra-superpowers.md](../../repos/obra-superpowers.md)*
