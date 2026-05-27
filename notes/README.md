# 学习笔记索引

> 每个追踪的 repo 拥有独立目录，承载：repo 解读、学习笔记、更新记录。

## 目录结构

```
notes/
├── README.md                          # 本文件
├── openclaw-openclaw/                 # OpenClaw 学习笔记（已有完整内容）
│   ├── README.md
│   ├── architecture.md
│   ├── changelog.md
│   ├── references.md
│   └── notes/
│       ├── 01-gateway.md
│       ├── 02-agent-loop.md
│       ├── ...
├── colbymchenry-codegraph/
│   └── README.md
├── affaan-m-ECC/
│   └── README.md
├── mattpocock-skills/
│   └── README.md
├── obra-superpowers/
│   └── README.md
├── NousResearch-hermes-agent/
│   └── README.md
├── safishamsi-graphify/
│   └── README.md
└── thedotmack-claude-mem/
    └── README.md
```

## 各 Repo 笔记

| Repo | 状态 | 入口 |
|------|------|------|
| [openclaw/openclaw](openclaw-openclaw/) | ✅ 已有完整笔记 | [查看](openclaw-openclaw/README.md) |
| [colbymchenry/codegraph](colbymchenry-codegraph/) | 待开始 | [查看](colbymchenry-codegraph/README.md) |
| [affaan-m/ECC](affaan-m-ECC/) | 待开始 | [查看](affaan-m-ECC/README.md) |
| [mattpocock/skills](mattpocock-skills/) | 待开始 | [查看](mattpocock-skills/README.md) |
| [obra/superpowers](obra-superpowers/) | 待开始 | [查看](obra-superpowers/README.md) |
| [NousResearch/hermes-agent](NousResearch-hermes-agent/) | 待开始 | [查看](NousResearch-hermes-agent/README.md) |
| [safishamsi/graphify](safishamsi-graphify/) | 待开始 | [查看](safishamsi-graphify/README.md) |
| [thedotmack/claude-mem](thedotmack-claude-mem/) | 待开始 | [查看](thedotmack-claude-mem/README.md) |

## 每个目录的约定结构

```
notes/<owner>-<repo>/
├── README.md              # 学习总览 + 进度 + Tracking Snapshot
├── architecture.md        # 架构解读
├── changelog.md           # 版本更新追踪
├── references.md          # 参考资料
└── notes/                 # 模块学习笔记
    ├── 01-xxx.md
    └── ...
```

## Tracking Snapshot 约定

每个 repo 的 `README.md` 头部（项目简介之前）必须包含以下版本锚点表格：

```markdown
## Tracking Snapshot

| 字段 | 值 |
|------|-----|
| 追踪分支 | `main` |
| 最后同步 commit | `abc1234...` (完整 SHA 或短 SHA) |
| 同步日期 | 2026-05-27 |
| 对应版本 | v0.9.4 (如有 tag/release) |
```

### 用途

- **检查更新**: 使用 `git log <最后同步 commit>..HEAD` 精确筛选新增 commit
- **版本对齐**: 确保笔记内容与源码版本一致
- **变更可追溯**: 每次同步笔记时更新此表格

### 维护规则

1. 每次阅读/同步上游 repo 后，**必须**更新 Tracking Snapshot
2. 如果上游 repo 没有明确版本号，`对应版本` 填 `_N/A_`
3. `最后同步 commit` 优先填完整 SHA，至少填 7 位短 SHA

---

*最后更新: 2026-05-26*
