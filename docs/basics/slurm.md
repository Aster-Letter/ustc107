# Slurm 速查

本科生算力平台的批处理和交互式计算任务通常围绕 Slurm 命令展开。本页只放跨课程通用命令；具体参数应在课程或用例页中解释。

## 常用命令

| 目标 | 命令 |
| --- | --- |
| 提交批处理作业 | `sbatch script.sbatch` |
| 查看自己的队列 | `squeue -u $USER` |
| 取消作业 | `scancel <jobid>` |
| 查看作业详情 | `scontrol show job <jobid>` |
| 交互式申请资源 | `salloc ...` |
| 在作业内启动命令 | `srun ...` |

## 作业脚本骨架

```bash
#!/bin/bash
#SBATCH --job-name=example
#SBATCH --output=%x_%j.out
#SBATCH --error=%x_%j.err
#SBATCH --partition=Students
#SBATCH --gres=gpu:1
#SBATCH --cpus-per-task=4
#SBATCH --mem=8G
#SBATCH --time=00:30:00

set -euo pipefail

cd ~/courses/course-name/code

source ~/miniforge3/etc/profile.d/conda.sh
conda activate course-name

python train.py
```

## 参数解释

- `--job-name`：作业名称，建议包含课程或实验名；
- `--output` / `--error`：标准输出与错误日志；
- `--partition`：分区名称；
- `--gres`：通用资源，例如 GPU 数量；
- `--cpus-per-task`：CPU 核数；
- `--mem`：内存；
- `--time`：最长运行时间。

## 排查顺序

1. 作业是否进入队列：`squeue -u $USER`。
2. 日志文件是否产生：检查 `--output` 和 `--error` 指向的位置。
3. 环境是否激活：在脚本中打印 `which python` 和关键包版本。
4. 资源是否足够：检查内存、CPU、GPU 和最长运行时间。
