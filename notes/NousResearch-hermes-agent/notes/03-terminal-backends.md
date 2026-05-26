# 7 种 Terminal Backend 学习笔记

> hermes-agent 的执行层 — 从本地到无服务器的 7 种代码执行环境。

## 概述

hermes-agent 支持 7 种 Terminal Backend，覆盖从本地开发到云端无服务器的全场景。每种后端提供相同的执行接口，Agent Core 无需感知底层差异。

### 统一接口

```python
class TerminalBackend:
    def execute(self, command: str) -> ExecutionResult: ...
    def read_file(self, path: str) -> str: ...
    def write_file(self, path: str, content: str) -> None: ...
    def list_dir(self, path: str) -> list: ...
```

## 7 种后端对比

| 后端 | 隔离级别 | 延迟 | 成本 | 持久化 | 适用场景 |
|------|---------|------|------|--------|---------|
| Local | 无隔离 | 0ms | 免费 | 完全 | 日常开发 |
| Docker | 容器隔离 | ~100ms | 低 | 卷挂载 | 测试/CI |
| SSH | 网络隔离 | ~50ms | 服务器 | 完全 | 远程服务器 |
| Singularity | HPC 隔离 | ~200ms | HPC | 绑定挂载 | 科学计算 |
| Modal | 无服务器 | ~2s冷启 | 按秒 | 临时 | 突发任务 |
| Daytona | 云开发环境 | ~1s | 按时 | 环境持久 | 团队协作 |
| Vercel | Web 沙箱 | ~1s | Hobby免费 | 临时 | 前端预览 |

## Local 后端

```
特点:
  - 零延迟，直接在用户机器执行
  - 完全文件系统访问
  - 无隔离 (风险: 可能影响系统)
  
适用:
  - 日常开发
  - 信任的任务
  - 需要完整环境的操作
```

## Docker 后端

```
特点:
  - 容器级隔离
  - 可复现环境 (Dockerfile)
  - 卷挂载保持数据

适用:
  - 不信任的代码执行
  - CI/CD 流水线
  - 环境一致性要求高的场景
```

## SSH 后端

```
特点:
  - 远程服务器执行
  - 网络级隔离
  - 利用远程算力

适用:
  - 需要特定硬件 (GPU)
  - 生产环境操作
  - 远程调试
```

## Singularity 后端

```
特点:
  - HPC (高性能计算) 容器
  - 用户空间执行 (无需 root)
  - SLURM/PBS 集成

适用:
  - 科学计算任务
  - GPU 集群
  - 大规模并行
```

## Modal 后端

```
特点:
  - 真正的无服务器
  - 按秒计费
  - 冷启动 ~2s，热启动 ~100ms
  - 空闲自动休眠

适用:
  - 突发计算需求
  - 不想维护服务器
  - 偶发的重计算任务

无服务器持久化:
  活跃 → 执行任务 (计费)
  空闲 → 休眠 (不计费)
  新请求 → 自动唤醒 (~2s)
```

## Daytona 后端

```
特点:
  - 云开发环境
  - 预配置好的工作区
  - 团队共享
  - Git 集成

适用:
  - 团队协作
  - 标准化开发环境
  - 新成员快速上手
```

## Vercel Sandbox 后端

```
特点:
  - Web 应用沙箱
  - 前端预览
  - 自动部署

适用:
  - 前端开发预览
  - Web 应用测试
  - 部署验证
```

## 后端选择策略

```
决策树:

需要本地文件? → Local
需要隔离? → Docker
需要远程算力? → SSH
需要 HPC? → Singularity
突发任务/不想管服务器? → Modal
团队协作? → Daytona
前端预览? → Vercel
```

## 问题与思考

- [x] 为什么 7 种? → 覆盖全场景
- [x] 统一接口如何实现? → 抽象 Backend 类
- [ ] 后端切换的延迟和用户体验?
- [ ] 多后端同时使用的场景?
- [ ] Modal 冷启动如何优化?

---

*参考: [architecture.md](../architecture.md) | [04-model-providers.md](./04-model-providers.md)*
