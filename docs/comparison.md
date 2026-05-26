# 对比分析

> 对收录的 8 个项目进行多维度对比。

## 一、总览对比

| 项目 | 类型 | 语言 | Stars | 核心价值 |
|------|------|------|-------|---------|
| openclaw/openclaw | 完整 Agent 框架 | JS/TS | 350k+ | 自托管 Agent 标杆 |
| NousResearch/hermes-agent | 完整 Agent 框架 | Python/Rust | — | 自进化 + 多平台 |
| affaan-m/ECC | 配置系统 | Markdown/YAML | 182k+ | Harness 性能优化 |
| obra/superpowers | 方法论框架 | Markdown | 57k+ | 结构化开发流程 |
| thedotmack/claude-mem | 记忆系统 | TypeScript | 72k+ | 跨 session 持久化 |
| safishamsi/graphify | 知识图谱 | Python | 30k+ | 多模态理解 |
| mattpocock/skills | Skills 集合 | Markdown | 20k+ | 工程最佳实践 |
| colbymchenry/codegraph | 知识图谱 | TypeScript | — | 代码结构理解 |

## 二、上下文工程对比

三种不同策略解决同一问题："如何让 Agent 更高效地理解项目"。

| 维度 | CodeGraph | Graphify | Claude-Mem |
|------|-----------|----------|-----------|
| **策略** | 代码结构图谱 (空间) | 多模态语义图谱 (空间) | 记忆压缩注入 (时间) |
| **输入** | 纯代码文件 | 代码+文档+图片+视频 | Session 活动记录 |
| **解析方式** | Tree-sitter (AST) | Claude Vision + Tree-sitter | Claude Agent SDK (AI 压缩) |
| **存储** | SQLite + FTS5 | NetworkX + JSON | SQLite + FTS5 + ChromaDB |
| **接口** | MCP Server (10 tools) | Skill (/graphify) | Hook 系统 (5 阶段) |
| **Token 减少** | 57% (平均) | 71.5x | 依 session 长度而定 |
| **实时性** | 文件监听自动同步 | --watch / git hook | Session Hook 实时 |
| **语言支持** | 20+ (Tree-sitter) | 通过 LLM 理解任意语言 | 无关（记录行为非代码） |
| **是否可组合** | ✅ 与其他方案互不冲突 | ✅ 互不冲突 | ✅ 互不冲突 |

## 三、Skills / 方法论框架对比

| 维度 | mattpocock/skills | obra/superpowers | affaan-m/ECC |
|------|-------------------|------------------|--------------|
| **规模** | ~15 skills | ~10 core skills | 246 skills + 61 agents |
| **哲学** | 小而精，解决具体痛点 | 完整方法论，强制流程 | 大而全，覆盖所有场景 |
| **格式** | 纯 Markdown（无 YAML） | SKILL.md (agentskills.io) | SKILL.md (YAML frontmatter) |
| **触发** | 手动调用 (/slash) | 自动触发 + 强制 | 自动触发 + 手动 + Hook |
| **核心能力** | 对齐(grill) + 质量(tdd) | 全流程(brainstorm→finish) | 全系统(skills+hooks+rules+instincts) |
| **学习系统** | ❌ | ❌ | ✅ Instincts (CL v2) |
| **安全** | ❌ | ❌ | ✅ AgentShield |
| **跨工具** | 通过 skills.sh 分发 | 8+ Harness plugin | 10+ Harness (DRY Adapter) |
| **上手难度** | ⭐ 极低 | ⭐⭐ 低 | ⭐⭐⭐ 中等 |

## 四、完整 Agent 系统对比

| 维度 | OpenClaw | Hermes Agent |
|------|----------|--------------|
| **运行模式** | Local daemon (Node.js) | Local / Docker / SSH / Serverless |
| **通信** | 50+ 通道 (WebSocket Gateway) | Telegram/Discord/Slack/WhatsApp/Signal + CLI |
| **编排** | Observe-Plan-Act Loop | 学习循环 + 子 Agent 委派 |
| **记忆** | Markdown-first (SOUL.md) + SQLite + Activation/Decay | Agent-curated + FTS5 + Honcho 用户建模 |
| **Skills** | ClawHub 市场 1200+ (AgentSkills 格式) | agentskills.io 标准，自创建+自改进 |
| **模型** | 多模型 + Fallback | 多模型 + Fallback (200+ via OpenRouter) |
| **学习** | ❌ 无内置 | ✅ 闭环学习 (唯一) |
| **部署** | 本地 daemon | 本地 / $5 VPS / GPU 集群 / Serverless |
| **安全事件** | ClawJacked / Claw Chain 等 | 暂无公开事件 |
| **社区** | 350k+ stars, 3.2M users | Nous Research 生态 |
| **研究价值** | 生产级架构参考 | 自进化 + 训练数据生成 |

## 五、架构模式对比

| 模式 | 采用的项目 | 说明 |
|------|-----------|------|
| Gateway + Agent Runtime | OpenClaw, Hermes Agent | WebSocket 控制平面 → Agent 运行时 |
| Plugin-First Config | ECC | 配置注入到宿主 Harness |
| Skill Auto-Trigger | Superpowers, ECC | 上下文匹配 → 自动激活 |
| MCP Server | CodeGraph | 标准化工具协议暴露能力 |
| Hook Lifecycle | Claude-Mem, ECC | 事件驱动的行为扩展 |
| Pure Markdown Instruction | mattpocock/skills | 零运行时，纯文本指令 |

## 六、学习系统对比

| 维度 | ECC Instincts | Hermes Learning Loop | Claude-Mem | OpenClaw Memory |
|------|--------------|---------------------|-----------|-----------------|
| **方式** | Session 观察 → 原子直觉 | 经验 → Skill 创建/改进 | Hook 捕获 → AI 压缩 | Markdown + Activation/Decay |
| **粒度** | 原子行为模式 | Skill 级别 | Session 观察 | 文档级 |
| **进化** | Instinct → Skill/Agent | Skill 自改进 | 无（只存储） | 无 |
| **隔离** | 项目级 | Agent 级 | 项目级 | Agent 级 |
| **存储** | SQLite | 本地文件 | SQLite + FTS5 | Markdown + SQLite |

---

## 七、关键洞察

1. **上下文工程是最实用的优化** — CodeGraph/Graphify/Claude-Mem 直接省钱省时间，且可与任何 Harness 组合
2. **方法论 > 工具数量** — Superpowers 证明 10 个精心设计的 Skills 比 246 个泛化 Skills 更有指导价值
3. **学习系统是差异化方向** — ECC Instincts 和 Hermes Learning Loop 代表 Agent "自我进化"的前沿
4. **跨 Harness 是大趋势** — ECC (10+)、Superpowers (8+) 都在追求"一次配置，多工具可用"
5. **安全不可忽视** — OpenClaw 的真实安全事件和 ECC AgentShield 表明 Agent 安全是生产级必备

---

*学习过程中持续更新*

*最后更新: 2026-05-26*
