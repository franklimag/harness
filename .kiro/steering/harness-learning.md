# Agent Harness 学习工程 Steering

## 项目目标

本仓库用于收集热门 Agent Harness 相关仓库的信息，并记录学习计划和学习笔记。
**不 clone 目标仓库的源码**，只通过公开资料记录信息和学习。

## 命名约定

- **repos/ 文件命名**: `<owner>-<repo_name>.md`（如 `openclaw-openclaw.md`、`affaan-m-ECC.md`）
- **notes/ 目录命名**: `<owner>-<repo_name>/`（如 `notes/openclaw-openclaw/`）
- 目的：避免不同 owner 下同名仓库冲突

## 目录结构

```
repos/<owner>-<repo_name>.md       # 项目信息卡片（基本信息、特点、架构、学习价值）
notes/<owner>-<repo_name>/         # 学习笔记目录
├── README.md                      # 学习总览 + 进度
├── architecture.md                # 架构解读
├── changelog.md                   # 版本更新追踪
├── references.md                  # 参考资料
└── notes/                         # 模块学习笔记
    ├── 01-xxx.md
    └── ...
docs/                              # 通用文档（跨项目对比、全局参考）
plans/                             # 学习计划
```

## 工作约定

### 项目收录规则
- **项目由维护者直接指定**，不自行搜索添加
- 每个项目基于 `repos/_template.md` 模板创建信息卡片
- 信息通过搜索公开资料获取，不 clone 仓库

### 笔记规范
- 每个追踪的 repo 在 `notes/` 下有独立目录
- 笔记目录承载：repo 解读、学习笔记、版本更新记录
- 模块笔记放在子目录 `notes/` 中，按编号排序
- 使用表格追踪学习进度

### 更新流程
- 新增项目时：创建 `repos/<owner>-<repo>.md` + `notes/<owner>-<repo>/README.md`
- 学习新知识时：在对应的 `notes/<owner>-<repo>/notes/` 下添加笔记
- 新的参考资料：添加到对应目录的 `references.md`
- 跨项目对比：更新 `docs/comparison.md`

## 协助要求

1. 收录新项目时，搜索公开信息填充模板
2. 保持中文撰写
3. 不自行添加项目，由用户指定
4. 专注于 Agent Harness 相关内容
5. 文件/目录命名统一使用 `<owner>-<repo_name>` 格式
