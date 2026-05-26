# Agent Harness 学习指引

> 从入门到深入的系统化学习路线，基于 8 个收录项目的依赖关系和难度编排。

## 学习哲学

```
先学"怎么写好指令给 Agent"（Skills）
  → 再学"怎么用指令编排完整流程"（方法论/配置系统）
    → 然后学"怎么优化 Agent 的效率"（上下文工程/记忆）
      → 最后学"怎么从零构建完整 Agent"（完整框架）
```

## 推荐学习顺序

### 第一阶段：理解 Skill 的本质

| 顺序 | 项目 | 预计时间 | 核心收获 |
|------|------|---------|---------|
| ① | **mattpocock/skills** | 2-3 小时 | 什么是"好的 Skill"、SKILL.md 格式、工程哲学 |

**为什么从这里开始：**
- 最轻量 — 纯 Markdown Skills，没有复杂架构
- 立即可用 — 装完就能在 Claude Code 里体验
- 解决真实痛点 — 对齐、冗余、质量、架构腐化
- 是后续所有项目的基础概念

**学完能回答：** "一个好的 Skill 应该长什么样？解决什么问题？"

---

### 第二阶段：从 Skill 到方法论

| 顺序 | 项目 | 预计时间 | 核心收获 |
|------|------|---------|---------|
| ② | **obra/superpowers** | 4-5 小时 | Skill 如何组成完整的开发方法论、子 Agent 驱动开发 |

**为什么第二个学：**
- 从"单个 Skill"升级到"完整工作流"
- 理解 brainstorm → plan → TDD → review → finish 的强制流程
- 学习 subagent-driven-development（Agent 自主工作数小时的秘密）
- 多 Harness 支持的第一次接触

**学完能回答：** "如何让 Agent 按照工程最佳实践自主完成复杂任务？"

---

### 第三阶段：Harness 配置系统全貌

| 顺序 | 项目 | 预计时间 | 核心收获 |
|------|------|---------|---------|
| ③ | **affaan-m/ECC** | 8-10 小时 | 完整 Harness 配置体系、自动学习(Instincts)、跨工具设计、安全 |

**为什么第三个学：**
- 它是 Skills + Hooks + Rules + Agents + Instincts 的集大成者
- 理解"配置即基础设施"的哲学
- Instincts（自动学习）是最独创的设计
- AgentShield 安全管线是生产级必备
- Token 优化策略直接省钱

**学完能回答：** "如何构建一个跨 10+ 工具的、自动学习的 Agent 配置系统？"

---

### 第四阶段：上下文工程（三个项目可并行）

| 顺序 | 项目 | 预计时间 | 核心收获 |
|------|------|---------|---------|
| ④ | **colbymchenry/codegraph** | 4-5 小时 | Tree-sitter + MCP、代码知识图谱、92% 工具调用减少 |
| ⑤ | **safishamsi/graphify** | 3-4 小时 | 多模态知识图谱、Claude Vision 应用、71x token 减少 |
| ⑥ | **thedotmack/claude-mem** | 4-5 小时 | 跨 session 记忆、Hook 生命周期、AI 压缩 |

**为什么放在一起学：**
- 三个项目解决同一个核心问题："如何让 Agent 更高效地理解上下文"
- 但策略完全不同，对比学习价值极高：
  - CodeGraph = **代码结构图谱**（AST 级别，精确但仅限代码）
  - Graphify = **语义知识图谱**（多模态，广但依赖 LLM）
  - Claude-Mem = **记忆压缩注入**（时间维度，跨 session 连续性）
- 三种方案可以组合使用，互不冲突

**学完能回答：** "如何从空间(图谱)和时间(记忆)两个维度优化 Agent 的上下文？"

---

### 第五阶段：完整 Agent 系统

| 顺序 | 项目 | 预计时间 | 核心收获 |
|------|------|---------|---------|
| ⑦ | **NousResearch/hermes-agent** | 6-8 小时 | 完整独立 Agent、学习循环、多平台 Gateway、Serverless 部署 |
| ⑧ | **openclaw/openclaw** | 8-10 小时 | 最大开源 Agent 框架、Gateway 架构、Memory 系统、安全事件 |

**为什么最后学：**
- 前面学的所有概念在这里完整呈现
- 这两个是"从零构建完整 Agent"的参考实现
- 可以用前面积累的知识审视它们的设计取舍：
  - Hermes Agent = 自进化 + 轻量 + 研究导向
  - OpenClaw = 大而全 + 生产级 + 社区驱动

**学完能回答：** "如果要从零构建一个 Agent 系统，该如何设计架构？"

---

## 依赖关系图

```
mattpocock/skills (Skill 基础)
    │
    ├──▶ obra/superpowers (方法论 = Skills 的编排)
    │        │
    │        └──▶ affaan-m/ECC (全配置系统 = 方法论 + 学习 + 安全)
    │
    ├──▶ colbymchenry/codegraph (上下文工程 - 代码图谱)
    │
    ├──▶ safishamsi/graphify (上下文工程 - 多模态图谱)
    │
    ├──▶ thedotmack/claude-mem (上下文工程 - 记忆持久化)
    │
    └──▶ NousResearch/hermes-agent (完整 Agent - 自进化)
              │
              └──▶ openclaw/openclaw (完整 Agent - 最大规模)
```

## 每个阶段的学习方式

### 对于每个项目：

1. **先读 `notes/<repo>/README.md`** — 建立全局印象
2. **读 `architecture.md`** — 理解核心架构
3. **按 `learning-guide.md`** — 跟着学习路线走
4. **读 `notes/` 子目录** — 深入各模块
5. **回到 `changelog.md`** — 了解演进方向
6. **查 `references.md`** — 需要时查阅原始资料

### 学习产出建议：

- 在各 `notes/` 文件的"问题与思考"部分记录自己的答案
- 将"学习记录"表格保持更新
- 有新发现时更新 `docs/comparison.md`（跨项目对比）

---

## 时间预估总览

| 阶段 | 项目数 | 预计时间 | 累计 |
|------|--------|---------|------|
| 第一阶段 | 1 | 2-3 小时 | 2-3h |
| 第二阶段 | 1 | 4-5 小时 | 6-8h |
| 第三阶段 | 1 | 8-10 小时 | 14-18h |
| 第四阶段 | 3 (可并行) | 11-14 小时 | 25-32h |
| 第五阶段 | 2 | 14-18 小时 | 39-50h |

**总计约 40-50 小时**，按每天 2 小时算约 3-4 周完成。

---

## 进度追踪

```markdown
### 我的学习进度

- [ ] ① mattpocock/skills (开始: ___ / 完成: ___)
- [ ] ② obra/superpowers (开始: ___ / 完成: ___)
- [ ] ③ affaan-m/ECC (开始: ___ / 完成: ___)
- [ ] ④ colbymchenry/codegraph (开始: ___ / 完成: ___)
- [ ] ⑤ safishamsi/graphify (开始: ___ / 完成: ___)
- [ ] ⑥ thedotmack/claude-mem (开始: ___ / 完成: ___)
- [ ] ⑦ NousResearch/hermes-agent (开始: ___ / 完成: ___)
- [ ] ⑧ openclaw/openclaw (开始: ___ / 完成: ___)
```

---

## 学完之后

完成全部学习后，建议：

1. **输出对比总结** → 更新 `docs/comparison.md`
2. **实践项目** → 基于所学构建自己的 Agent Harness 组件
3. **持续追踪** → 定期更新各 repo 的 `changelog.md`
4. **社区参与** → 向感兴趣的项目贡献 PR

---

*最后更新: 2026-05-26*
