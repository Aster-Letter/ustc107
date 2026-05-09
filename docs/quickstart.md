# 快速开始

这一页面向第一次使用本科生算力平台的同学，目标是在最短路径内完成一次可复现的小任务。

## 你需要先确认

- 已获得平台账号或具备统一身份认证入口权限；
- 知道自己的课程、课题或实验需要使用 CPU、GPU、内存与运行时间的大致规模；
- 本地已有需要运行的代码、数据或最小测试脚本。

## 推荐阅读路径

1. 阅读[平台概览](overview/index.md)，明确平台适用边界。
2. 按[环境配置](basics/environments.md)准备 Python、Conda/Mamba 或课程指定环境。
3. 按[文件与数据](basics/files.md)上传代码与数据。
4. 按[提交任务](basics/jobs.md)提交一个最小作业。
5. 遇到命令问题时查阅[Slurm 速查](basics/slurm.md)和[常见问题](basics/faq.md)。
6. 通过[GUI 使用](basics/gui.md)或[命令行使用](basics/cli.md)了解其他平台交互方法。

## 第一个任务应当很小

建议先提交一个 1 到 3 分钟内结束的任务，例如打印 CUDA 可用性、读取一小段数据、运行课程仓库自带的 smoke test。不要把第一次作业直接设成完整训练或长时间仿真。

```bash
python - <<'PY'
import torch
print("cuda:", torch.cuda.is_available())
print("device:", torch.cuda.get_device_name(0) if torch.cuda.is_available() else "cpu")
PY
```

## 之后再进入学科用例

基础流程跑通后，再进入[分专业与需求指引](guides/index.md)，选择与你的课程或实验最接近的用例页面。
