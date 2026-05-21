---
page_type: concept
audience: beginner
status: needs_verification
last_verified:
maintainers:
  - name: docs-team
    contact: TODO
graph:
  priority: core
  next:
    - basics/jobs.md
    - basics/slurm.md
  related:
    - basics/environments.md
    - basics/files.md
  advanced: []
---

# 平台资源

本页解释“资源”在平台中的含义，以及你在提交任务时为什么要填写 CPU、GPU、内存和运行时间。

!!! info "默认配置"
    统一身份认证登录的同学通常默认使用 `Students` 分区，QOS 使用 `qos_stu_default`，初始算力配额为 `4CPU/1GPU/4h`。这些属于平台动态配置，提交前仍应以当前平台界面和管理员说明为准。

## 必须知道

- **CPU**负责通用计算、数据预处理、文件读取、模型训练中的数据加载等任务。
- **GPU**适合深度学习训练、部分科学计算和高度并行任务。不是所有程序都会自动使用 GPU。
- **内存**是 CPU 侧程序运行时使用的主内存；深度学习中还会遇到 **显存**，它是 GPU 上的内存。
- **运行时间**是作业允许占用资源的最长时间。估计过短会被系统中止，估计过长可能更难排队。
- **partition / QOS**是调度策略相关配置，用来约束可用节点、资源上限和优先级。创建应用或提交作业时，界面会展示当前可选项；具体名称请以平台当前页面为准。

## 怎么估算资源

第一次运行一个新任务时，建议从小规模测试开始：

1. 先用很小数据或很少迭代步数跑通流程。
2. 查看日志是否正常结束。
3. 观察 CPU、内存、GPU 和显存是否接近上限。
4. 再逐步增加数据量、batch size、worker 数或运行时间。

如果任务失败，不要立刻把所有资源都加大。先判断瓶颈：

- 日志显示 `CUDA out of memory`：通常是显存不足，先减小 batch size 或模型规模。
- 读取数据很慢：可能是数据加载、文件数量或 CPU worker 设置问题。
- 作业长期排队：可能是申请资源过多、队列拥挤或触发配额限制。
- 没有日志输出：可能是脚本没有运行到输出位置、日志路径不存在，或作业尚未开始。

## 当前状态示例

下面是一份 2026-05-21 从登录 Shell 采集到的状态摘要，只用于帮助理解命令输出格式，不代表长期固定配置。

| 项目 | 示例信息 | 阅读方式 |
| --- | --- | --- |
| 登录节点 | `tradmin-02` | 登录 Shell 不等于计算节点，重计算任务应通过 Slurm 申请资源。 |
| 用户目录 | `/home/scc/<账号>` | 文档和截图中应遮挡完整个人账号。 |
| 登录环境 Python | `Python 3.12.3` | 项目环境可能不同，运行项目前仍应检查当前 conda 环境。 |
| `Students` 分区 | `anode[05-17]`，`TRES` 显示 `cpu=1664`、`mem=7500G`、`gres/gpu=104` | 这是分区总量，不等于单个用户可用配额。 |
| `Students` QOS | `qos_stu001`、`qos_stu_default`、`qos_stu_small`、`qos_stu_medium`、`qos_stu_medium_2gpu`、`qos_stu_long`、`qos_stu_cpu_long` | 账号能否使用某个 QOS，以提交结果和平台授权为准。 |
| 常见节点状态 | `idle`、`mix`、`comp`、`down` | `idle` 表示空闲，`mix` 表示部分占用，`comp` 表示资源较满，`down` 表示不可用。 |

维护文档时可运行：

```bash
scontrol show part
sinfo
```

输出中的 `AllowAccounts`、`AllowQos`、`MaxTime`、`State`、`TRES` 都会随平台调整变化。正式提交作业前，应优先看平台页面和当前命令输出。

## 资源伦理

共享平台的目标是让更多同学能完成课程和训练任务。建议：

- 只申请任务当前阶段需要的资源。
- 交互式任务不用时及时退出。
- 长任务优先使用批处理脚本，保留日志，方便复现和排查。
- 不绕过平台限制，不抢占公共资源。

## 动态事实

以下内容会随平台扩容、维护和调度策略变化，建议以平台界面或管理员确认结果为准：

- 可用 GPU 型号、节点名称和节点规格。
- A100、5090 等 GPU 型号的节点分布、驱动版本和 CUDA 兼容范围。
- 文件传输、共享存储容量和清理策略。
