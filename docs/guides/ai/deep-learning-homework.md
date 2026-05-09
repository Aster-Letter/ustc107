# 使用集群提交深度学习作业任务

> 一句话，5090，不来上车吗？

[本科生算力平台](https://107.ustc.edu.cn/dashboard)为我们提供了丰富的 GPU 资源，非常适合《深度学习基础》这种作业对 GPU 算力要求较高的课程。我们可以从以下几个方面来了解集群资源，以及使用集群提交深度学习作业任务。

当前默认一位学生可以使用 1 块 GPU 和 4 核 CPU，内存 4GB，作业最长运行时间为 4 小时，但是这对于深度学习作业来说通常是不够的（CPU 和内存限制较明显），申请更高资源的链接为 [107-算力申请](https://107.ustc.edu.cn/apply)。

## 几个重要概念

- **登录节点**：这就是我们在「登录集群——Shell」中连接到的节点（比如 `tradmin-02`），用于进行文件传输、环境配置和提交作业等操作。登录节点上不进行开销大的计算任务。
- **计算节点**：实际执行我们提交的作业任务的节点（比如 `anode01`、`anode02` 等）。这些节点是实际的 GPU 服务器，负责运行我们的深度学习代码。

在[本科生算力平台](https://107.ustc.edu.cn/dashboard)当前的实现中，登录节点和计算节点是共享 `~` 目录的（实现上是一个高速网络文件系统），这意味着：

- 可以使用登录节点进行文件传输；
- 可以用登录节点创建 [Miniforge](https://mirrors.ustc.edu.cn/help/anaconda.html#miniforge-mamba) 环境，然后在计算节点上使用；

## 基本思想

- 在登录节点上用 Miniforge 配置环境（也可以用 `srun` 进入计算节点配置环境）；
- 试自己运行代码的习惯，可以使用以下方式：
    - 编写作业脚本（`.sbatch` 文件），使用 `sbatch` 提交作业；
    - 使用 `salloc` 申请一个交互式的计算节点，再用 `srun` 进入计算节点，直接在计算节点上运行代码（可以使用 `tmux` 等工具保持会话）；
- 如果深入计算节点监控任务情况（比如 GPU 占用率 `nvitop`），可以：
    - 使用 `squeue -u $USER` 查看基本的作业队列情况；
    - 使用 `srun --jobid=xxx --pty bash` 打开计算节点的 bash；
    - 使用 [Wandb](https://wandb.ai/site)、[Swanlab](https://swanlab.cn/)（国内，可直连，推荐）等网站，监控实时 GPU 占用率，训练曲线等特征。

![在登录节点上打开计算节点的 bash](./assets/cp4u-term.png)

## 分步指引

### 配置环境

默认读者使用过 `conda` 管理过 Python 环境。

Miniforge 是一个[开源](https://github.com/conda-forge/miniforge)的 Python 环境管理工具，是 [Miniconda](https://docs.conda.io/en/latest/miniconda.html) 的开源替代品，非常适合在集群环境中使用。我们可以在登录节点上安装 Miniforge，并创建一个新的环境来安装我们需要的深度学习库（如 PyTorch）。

`mamba`（或 `conda`）可以用来安装：

- `mamba create -n dl-homework python=3.14` 创建一个新的 Python 环境；
- `nvitop`, `git`, `cuda-toolkit` 等实用工具；
- ……

### 编写作业脚本

一个基本的 `.sbatch` 作业脚本示例如下：

```bash
#!/bin/bash
#SBATCH --job-name=<your-job-name>
#SBATCH --output=/home/scc/<your-username>/.../%x_%j.out
#SBATCH --error=/home/scc/<your-username>/.../%x_%j.err
#SBATCH --partition=Students
#SBATCH --qos=qos_stu_medium
#SBATCH --gres=gpu:1
#SBATCH --cpus-per-task=16
#SBATCH --mem=32G
#SBATCH --time=8:00:00

set -euo pipefail

cd ~/repo/ai3003/lab/lab4

# 激活 conda 环境
set +u
source ~/miniforge3/etc/profile.d/conda.sh
conda activate ai25
set -u

# 运行训练脚本
# 待填写
```

可能要更改的部分可能有 `job-name`、`output/error`, `partition`、`qos`、`gres`（GPU 数量和类型）等部分，可以参考图形界面中的配置来修改。

![参考图形界面的 QOS 和 Partition](./assets/cp4u-work.png)

### Slurm 命令

具体的见（待补充），常用的命令有：

- `sbatch <script.sbatch>`：提交作业脚本；
- `squeue -u $USER`：查看当前用户的作业队列；
- `srun --jobid=xxx --pty bash`：进入计算节点的 bash；
- `scancel <jobid>`：取消作业。

### 文件的传输

有的时候，向登录节点传输文件也是一件很麻烦的事情。由于当前登录节点并没有开启 `ssh` 权限，当前只能通过图形界面的「文件管理」功能来上传和下载文件。

建议在上传较大的文件夹的时候，使用「打包-上传-解包」的方式：

```bash
# 在本地（如果是类 Unix 系统的话）/集群终端中压缩文件夹
tar -czvf my_folder.tar.gz my_folder/
# 或只打包不压缩
tar -cvf my_folder.tar my_folder/

# 使用图形界面上传后，在登录节点上解压
tar -xzvf my_folder.tar.gz
# 或解包
tar -xvf my_folder.tar
```