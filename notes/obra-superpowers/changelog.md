# obra/superpowers 版本更新追踪

> 跟踪 superpowers 的版本发布、方法论演进和生态系统扩展。

## 项目概况

superpowers 作为软件开发方法论，更新主要体现在工作流完善、Harness 支持扩展和 Skills 迭代。

## 发展里程碑

### 早期 — 核心方法论确立

| 事件 | 说明 |
|------|------|
| 项目创建 | Jesse Vincent (@obra) 发起 |
| 核心工作流 | 确立六阶段开发流程 |
| Brainstorming | 第一个核心 Skill 发布 |
| TDD 集成 | 测试驱动开发作为内置原则 |

### 增长期 — Harness 扩展

| 事件 | 说明 |
|------|------|
| Claude Code 支持 | Plugin Marketplace 上架 |
| Codex 支持 | CLI 和 App 双端适配 |
| Gemini CLI 支持 | Google AI 工具适配 |
| OpenCode 支持 | 开源 Agent 工具 |
| Cursor 支持 | IDE 环境适配 |
| Copilot CLI | GitHub 工具链 |
| Factory Droid | 企业级支持 |

### 当前 — 社区与标准化

| 事件 | 说明 |
|------|------|
| 57K Stars | 社区规模达到 57K+ |
| agentskills.io | 参与/发起社区标准 |
| PyPI 发布 | agent-superpowers 包 |
| Mintlify 文档 | 完整文档站点上线 |

## 方法论演进

```
V1: 基础方法论
├── Brainstorm → Plan → Implement → Review
└── 四阶段，线性流程

V2: 增强方法论 (当前)
├── Brainstorm → Specify → Plan → Implement → Review → Finish
├── 六阶段，增加了 Specify 和 Finish
├── 引入 Subagent-Driven-Development
└── 强化了 TDD/YAGNI/DRY 内置原则
```

## Skills 演进

| 阶段 | 新增 Skills | 关键变化 |
|------|------------|---------|
| 初始 | brainstorming, TDD | 核心流程 |
| 中期 | subagent-driven-development | 引入子 Agent 模式 |
| 当前 | code-reviewer, systematic-debugging, writing-skills | 完善辅助能力 |

## Harness 支持时间线

```
Phase 1: Claude Code (原生支持)
    │
    ▼
Phase 2: Codex + Gemini (主流扩展)
    │
    ▼
Phase 3: OpenCode + Cursor (IDE 生态)
    │
    ▼
Phase 4: Copilot CLI + Factory Droid (企业级)
    │
    ▼
Phase 5: 8+ Harnesses (当前)
```

## 社区增长

| 指标 | 数值 | 趋势 |
|------|------|------|
| GitHub Stars | 57K+ | 快速增长 |
| PyPI 下载 | — | 稳步增长 |
| Harness 支持 | 7+ | 持续扩展 |
| agentskills.io 参与 | 核心标准 | 引领方向 |

## 关注的未来方向

- [ ] 更多 Harness 适配
- [ ] Subagent 并行执行优化
- [ ] 方法论可定制化（根据项目类型调整）
- [ ] 团队协作工作流
- [ ] 与 CI/CD 系统集成
- [ ] 性能指标和质量度量

## 如何追踪更新

1. **GitHub**: https://github.com/obra/superpowers (Watch)
2. **文档**: https://obra-superpowers.mintlify.app/
3. **PyPI**: `pip show agent-superpowers`
4. **agentskills.io**: 标准规范更新

---

*最后更新: 2026-07*
