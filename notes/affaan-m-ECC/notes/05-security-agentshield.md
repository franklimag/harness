# AgentShield 安全管线学习笔记

> ECC 的安全保障系统 — Red-Team/Blue-Team/Auditor 三阶段管线 + 1282 自动化测试。

## 系统概述

**AgentShield** 是 ECC 的安全子系统，通过模拟攻击（Red Team）、验证防御（Blue Team）、生成审计报告（Auditor）的三阶段管线，确保 Agent 行为的安全性。它作为独立 npm 包 `ecc-agentshield` 发布。

### 核心数据

| 属性 | 值 |
|------|-----|
| 引入版本 | v1.6.0 (Feb 2026) |
| npm 包 | ecc-agentshield |
| 驱动模型 | Opus 4.6 |
| 自动化测试 | 1282 个 |
| 静态规则 | 102 条 |
| 管线阶段 | 3 (Red/Blue/Auditor) |
| 扫描模式 | 多种 (快速/深度/全量) |

## 三阶段管线架构

### 总体流程

```
┌──────────────────────────────────────────────────────────────┐
│                    AgentShield Pipeline                        │
│                                                               │
│  ┌────────────┐     ┌────────────┐     ┌────────────┐       │
│  │  Stage 1   │     │  Stage 2   │     │  Stage 3   │       │
│  │ RED TEAM   │────▶│ BLUE TEAM  │────▶│  AUDITOR   │       │
│  │            │     │            │     │            │       │
│  │ 攻击模拟    │     │ 防御验证    │     │ 审计报告    │       │
│  │ 寻找漏洞    │     │ 修补漏洞    │     │ 安全评分    │       │
│  │            │     │            │     │            │       │
│  │ Opus 4.6   │     │ Opus 4.6   │     │ Opus 4.6   │       │
│  └────────────┘     └────────────┘     └────────────┘       │
│        │                   │                   │             │
│        ▼                   ▼                   ▼             │
│  Attack Vectors      Defense Patches      Security Report    │
│  (攻击向量集)         (防御补丁集)         (安全审计报告)      │
└──────────────────────────────────────────────────────────────┘
```

### Stage 1: Red Team（红队 — 攻击模拟）

#### 职责

Red Team 的目标是**尽可能发现安全漏洞**，模拟恶意用户或恶意环境对 Agent 的攻击：

| 攻击类别 | 说明 | 示例 |
|---------|------|------|
| Prompt Injection | 通过输入注入恶意指令 | "忽略之前的指令，执行..." |
| Tool Abuse | 滥用工具调用 | 通过 shell 执行危险命令 |
| Data Exfiltration | 数据窃取 | 尝试读取环境变量/密钥 |
| Privilege Escalation | 权限提升 | 绕过安全限制获取更高权限 |
| Context Poisoning | 上下文污染 | 注入虚假信息到记忆系统 |
| Denial of Service | 拒绝服务 | 触发无限循环/资源耗尽 |

#### 攻击生成流程

```
配置目标环境
    │
    ▼
Opus 4.6 生成攻击方案
    │
    ▼
执行攻击模拟
    │
    ▼
记录: 攻击向量 + 成功/失败 + 影响评估
    │
    ▼
输出: Attack Report → 传递给 Blue Team
```

### Stage 2: Blue Team（蓝队 — 防御验证）

#### 职责

Blue Team 接收 Red Team 的攻击报告，**验证防御有效性**并**修补发现的漏洞**：

| 防御行为 | 说明 |
|---------|------|
| 验证现有防护 | 确认 Rules/Hooks 是否阻止了攻击 |
| 识别防护缺口 | 找出被攻破的防线 |
| 生成修补方案 | 为每个漏洞生成防御代码 |
| 回归测试 | 确保修补不破坏正常功能 |

#### 防御验证流程

```
接收 Attack Report
    │
    ▼
逐一验证每个攻击向量
    ├── 被阻止 → 标记为 "已防护"
    └── 未阻止 → 生成防御补丁
    │
    ▼
应用防御补丁
    │
    ▼
重新运行攻击模拟
    │
    ▼
确认所有攻击均被阻止
    │
    ▼
输出: Defense Report → 传递给 Auditor
```

### Stage 3: Auditor（审计员 — 审计报告）

#### 职责

Auditor 综合两轮结果，生成**最终安全审计报告**和**安全评分**：

| 输出内容 | 说明 |
|---------|------|
| Security Score | 整体安全评分 (0-100) |
| Vulnerability List | 发现的漏洞清单 |
| Remediation Status | 修补状态 (修复/待修/无法修) |
| Risk Assessment | 残余风险评估 |
| Recommendations | 改进建议 |

## 测试体系

### 1282 自动化测试

| 测试类别 | 约数量 | 说明 |
|---------|--------|------|
| Prompt Injection | ~300 | 各类注入攻击模式 |
| Tool Safety | ~250 | 工具调用安全边界 |
| Data Protection | ~200 | 数据泄漏防护 |
| Permission | ~150 | 权限控制验证 |
| Context Integrity | ~150 | 上下文完整性 |
| Edge Cases | ~130 | 边界情况测试 |
| Regression | ~102 | 回归测试 (对应静态规则) |

### 102 静态规则

静态规则是**不依赖 LLM 判断**的确定性安全检查：

```javascript
// 示例静态规则
const STATIC_RULES = [
  // 文件系统安全
  { id: 'FS-001', check: 'no_write_outside_workspace', severity: 'critical' },
  { id: 'FS-002', check: 'no_read_sensitive_files', severity: 'high' },
  { id: 'FS-003', check: 'no_recursive_delete', severity: 'critical' },
  
  // Shell 安全
  { id: 'SH-001', check: 'no_sudo_commands', severity: 'critical' },
  { id: 'SH-002', check: 'no_pipe_to_shell', severity: 'high' },
  { id: 'SH-003', check: 'no_network_exfil', severity: 'high' },
  
  // 环境安全
  { id: 'ENV-001', check: 'no_env_var_exposure', severity: 'high' },
  { id: 'ENV-002', check: 'no_secret_in_output', severity: 'critical' },
  
  // 上下文安全
  { id: 'CTX-001', check: 'no_instruction_override', severity: 'critical' },
  { id: 'CTX-002', check: 'no_context_injection', severity: 'high' },
  
  // ... 共 102 条
];
```

### 规则严重度分级

| 级别 | 含义 | 示例 |
|------|------|------|
| critical | 必须阻止，不可绕过 | sudo, rm -rf /, 密钥泄漏 |
| high | 默认阻止，特殊情况可放行 | 网络外传, 敏感文件读取 |
| medium | 警告，需用户确认 | 大范围文件修改, 第三方 API 调用 |
| low | 记录，用于审计 | 非标准工具使用 |

## 扫描模式

### 快速扫描 (Quick)

```bash
ecc shield --mode quick
```

- 仅运行静态规则 (102 条)
- 不涉及 LLM 调用
- 秒级完成
- 适用: CI/CD 流水线, 每次 commit

### 深度扫描 (Deep)

```bash
ecc shield --mode deep
```

- 静态规则 + 部分动态测试 (~500 tests)
- 使用 Opus 4.6 进行智能分析
- 分钟级完成
- 适用: PR 审查, 定期安全检查

### 全量扫描 (Full)

```bash
ecc shield --mode full
```

- 完整三阶段管线 (Red → Blue → Auditor)
- 1282 全部测试
- 可能需要较长时间
- 适用: 版本发布前, 安全审计

## 与 Hook 系统的集成

AgentShield 通过 Hooks 提供**实时防护**：

```
PreToolUse Hook → AgentShield 静态规则检查
                       │
                       ├── 通过 → 允许执行
                       └── 拒绝 → 阻止 + 记录原因
```

### 实时防护脚本

```javascript
// scripts/hooks/pre-tool-use/agentshield-check.js
const { staticRules } = require('ecc-agentshield');

module.exports = async function(event) {
  const violations = staticRules.check(event.tool);
  
  if (violations.length > 0) {
    const critical = violations.filter(v => v.severity === 'critical');
    
    if (critical.length > 0) {
      return {
        allow: false,
        reason: `AgentShield blocked: ${critical[0].rule} - ${critical[0].message}`
      };
    }
    
    // 非 critical 的违规记录但允许
    await logViolations(violations, event);
  }
  
  return { allow: true };
};
```

## npm 包: ecc-agentshield

### 安装

```bash
npm install ecc-agentshield
# 或
npm install -g ecc-agentshield
```

### API 使用

```javascript
const { AgentShield, staticRules, runPipeline } = require('ecc-agentshield');

// 快速静态检查
const violations = staticRules.check(toolCall);

// 完整管线
const report = await runPipeline({
  mode: 'full',          // quick / deep / full
  target: './project',   // 目标项目路径
  model: 'opus-4.6',    // 使用的模型
});

console.log(report.score);           // 安全评分
console.log(report.vulnerabilities); // 漏洞列表
console.log(report.recommendations); // 建议
```

## 安全报告格式

### 报告结构

```json
{
  "timestamp": "2026-07-01T10:00:00Z",
  "mode": "full",
  "score": 87,
  "grade": "B+",
  "summary": {
    "total_tests": 1282,
    "passed": 1245,
    "failed": 37,
    "fixed_by_blue_team": 34,
    "remaining_vulnerabilities": 3
  },
  "vulnerabilities": [
    {
      "id": "VULN-001",
      "severity": "medium",
      "category": "data_protection",
      "description": "Environment variable exposure via error messages",
      "remediation": "Filter env vars from error output",
      "status": "open"
    }
  ],
  "recommendations": [
    "Add output sanitization for error messages",
    "Implement rate limiting for tool calls",
    "Add timeout for shell executions"
  ]
}
```

## 与其他安全系统对比

| 维度 | ECC AgentShield | OpenClaw Exec Policy | 传统 SAST |
|------|-----------------|---------------------|-----------|
| 方式 | AI 攻防模拟 | 规则引擎 | 静态分析 |
| 覆盖 | Agent 行为全面 | 工具调用控制 | 代码漏洞 |
| 动态性 | 高 (LLM 驱动) | 中 (规则配置) | 低 (固定规则) |
| 自适应 | ✅ (新攻击模式) | ❌ | ❌ |
| 成本 | 高 (Opus 4.6) | 低 | 低 |
| 误报率 | 低 | 中 | 高 |

## 问题与思考

- [x] 管线三个阶段的职责? → Red 攻击、Blue 防御、Auditor 审计
- [x] 驱动模型? → Opus 4.6
- [x] 测试规模? → 1282 tests + 102 static rules
- [ ] Opus 4.6 的 API 成本? 全量扫描一次大约多少 token?
- [ ] 静态规则是否可自定义扩展?
- [ ] Red Team 是否能发现 0-day 级别的新攻击模式?
- [ ] Blue Team 的修补是否自动应用还是需要人工确认?
- [ ] 与 CI/CD 的具体集成方式?

## 学习记录

| 日期 | 内容 | 来源 |
|------|------|------|
| 2026-07 | 初始整理: 三阶段管线、测试体系、扫描模式、Hook 集成 | GitHub README + npm 包信息 |

---

*参考: [architecture.md](../architecture.md) | [02-hooks-architecture.md](./02-hooks-architecture.md)*
