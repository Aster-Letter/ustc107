# 环境配置

本页整理不同任务都会用到的环境准备方式。具体课程或专业用例只应写差异化步骤，通用安装、激活、复用与排障集中放在这里。

## 推荐原则

- 优先使用课程或平台已经验证过的环境说明；
- Python 项目优先使用 Miniforge/Mamba 或课程指定的 Conda 环境；
- 不要在登录节点运行重计算、长时间训练或大规模编译；
- 每个课程或项目使用独立环境，避免依赖互相污染。

## Miniforge/Mamba

适合需要安装 Python、PyTorch、NumPy、SciPy、Jupyter 相关依赖的任务。

```bash
mamba create -n course-name python=3.12
conda activate course-name
```

如果还没有安装 Miniforge，可参考中科大镜像站的 Miniforge 说明，并结合平台网络情况选择镜像源。

## 课程环境记录

建议每个用例页记录以下信息：

- 环境名称；
- Python 或主要软件版本；
- 关键依赖安装命令；
- 验证环境是否可用的最小命令；
- 已知不兼容版本或常见失败原因。

## 验证 GPU 环境

```bash
python - <<'PY'
import torch
print(torch.__version__)
print(torch.cuda.is_available())
PY
```

如果验证失败，优先确认当前命令是否运行在计算节点，而不是登录节点。
