# OpenSportsLib

<img src="docs/assets/osl.jpg" height="400">

OpenSportsLib 是一个面向体育视频理解的模块化 Python 库。

它提供统一框架，用于对体育视频中的关键时序理解任务进行**训练、评估和推理**，目前包括：

- **动作分类**
- **动作定位 / 动作检测（spotting）**
- **视觉问答（VQA）**

检索以及动作描述 / 字幕生成仍在路线图中。本包尚未提供对应的一等任务封装或训练流程。

OpenSportsLib 面向希望为体育视频 AI 建立可复现、可扩展工作流的**研究人员、机器学习工程师和体育分析团队**。

## 为什么选择 OpenSportsLib？

- 训练与推理共用一套工作流
- 模块化设计，便于新增任务、数据集和模型
- 配置驱动的实验，便于复现
- 可选的 SpoTTA 测试时自适应，用于 E2ESpot 推理
- 支持多种模态和体育场景工作流
- 兼顾研究友好与实际落地

## 快速链接

- **文档：** https://opensportslab.github.io/opensportslib/
- **OSL JSON 格式：** https://opensportslab.github.io/opensportslib/data/osl-json-format/
- **推理服务：** [server/README.md](server/README.md)，包含 checkpoint 与仅配置的规则模型注册
- **PyPI：** https://pypi.org/project/opensportslib/
- **Issues：** https://github.com/OpenSportsLab/opensportslib/issues

---

## 安装

> 需要 **Python 3.12+**。  
> 支持 CUDA 12.6 / 12.8 / 13.0（没有 NVIDIA 驱动时回退到 CPU）。  
> 驱动只到 CUDA 12.1–12.5 时，安装仍支持 Pascal 的 PyTorch 2.5.1（`cu121`）。  
> PyTorch Geometric 在 CUDA 12.6+ 上使用 PyTorch 2.12.1 兼容配置；旧驱动则保持 2.5.1。

### 创建 conda 环境

```bash
conda create -n osl python=3.12 pip -y
conda activate osl
```

### 稳定版

```bash
pip install opensportslib
```

### 预发布版

```bash
pip install --pre opensportslib
```

### 源码开发版

```bash
pip install -e .
```

### 配置环境（PyTorch、CUDA 感知与可选依赖）
```bash
# 安装 PyTorch（自动检测 CPU/GPU）
opensportslib setup

# 可选：安装 PyTorch Geometric 支持。这会把已安装的
# Torch 栈替换为与 PyG 兼容的 PyTorch 2.12.1 配置。
opensportslib setup --pyg

# 可选：安装 DALI 支持
opensportslib setup --dali

# 可选：安装与 X-VARS 兼容的 VQA 依赖配置
opensportslib setup --vqa_xvars

# 可选：安装与 Qwen 兼容的 VQA 依赖配置
opensportslib setup --vqa_qwen
```
---

**说明：**  
运行 `opensportslib setup` 会自动配置依赖。  
如果出现问题，请根据你的 CUDA 版本或系统兼容性，手动安装匹配的 `torch`、`torchvision` 及相关库。

VQA 请只选用一套后端专用依赖配置：

- `--vqa_xvars` 会按 `XVARS_DEPENDENCY_PINS` 安装与 X-VARS 兼容的 Hugging Face 依赖栈
- `--vqa_qwen` 会按 `QWEN_DEPENDENCY_PINS` 安装与 Qwen 兼容的 Hugging Face 依赖栈

`vqa_qwen` 配置支持 `Qwen/Qwen2.5-7B-Instruct` 和 `Qwen/Qwen3.5-9B-Base`。

---

## 数据与预训练模型

OpenSportsLib 使用外部标注文件、数据集和预训练 checkpoint。

公开资源托管在 **OpenSportsLab Hugging Face 组织**：

**https://huggingface.co/OpenSportsLab**

可从这里查找：
- 数据集
- 标注文件
- 提取后的特征
- 预训练模型与 checkpoint

可用预训练模型、报告分数、数据集和加载示例见 [模型库](docs/model-zoo.md)。

---

## 数据集格式

OpenSportsLib 使用 **OSL JSON v2.0** 标注格式来描述多模态数据集和预测结果。格式说明、示例和转换说明见 [OSL JSON 格式指南](docs/data/osl-json-format.md)。

---

## 快速开始

### 分类

```python
from opensportslib.apis import ClassificationModel

my_model = ClassificationModel(
    config="/path/to/classification.yaml",
    weights=None,  # 可选：本地路径或 Hugging Face 模型 ID
)

my_model.train(
    train_set="/path/to/train_annotations.json",
    valid_set="/path/to/valid_annotations.json",
)

predictions = my_model.infer(test_set="/path/to/test_annotations.json")
my_model.save_predictions(
    output_path="/path/to/predictions.json",
    predictions=predictions,
)
```

定位、VQA 以及更多端到端示例见
[API 指南](opensportslib/apis/README.md)、[快速开始脚本](examples/quickstart/)
和 [VQA 指南](docs/tools/vqa.md)。


---

## Hugging Face 数据集传输

OpenSportsLib 提供用于下载和上传 OSL 数据集到 Hugging Face 的 API 与脚本。

SN-GAR 分类与动作检测的配置、环境准备、缓存和训练命令见 [SN-GAR 示例](examples/sngar/README.md)。

### Python API

```python
from opensportslib.tools import (
    download_dataset_split_from_hf,
    download_dataset_sample_inputs_from_hf,
    upload_dataset_inputs_from_json_to_hf,
    upload_dataset_as_parquet_to_hf,
)
```

### 脚本

```bash
python tools/download/download_osl_hf.py --repo-id <org/repo> --revision main --split test --format parquet --output-dir downloaded_data --annotations-only
python tools/download/upload_osl_hf.py --repo-id <org/repo> --json-path <local_dataset.json> --split test --revision main
```

---

## 示例与文档

先用本 README 快速上手，再通过以下资料深入：

- 完整文档：https://opensportslab.github.io/opensportslib/
- OSL JSON 格式：[docs/data/osl-json-format.md](docs/data/osl-json-format.md)
- 高层 API 指南：[opensportslib/apis/README.md](opensportslib/apis/README.md)
- 配置指南：https://opensportslab.github.io/opensportslib/config/configuration-guide/
- 示例配置：[examples/configs/](examples/configs/)
- 快速开始脚本：[examples/quickstart/](examples/quickstart/)
- 贡献指南：[CONTRIBUTING.md](CONTRIBUTING.md)
- 开发者指南：[DEVELOPERS.md](DEVELOPERS.md)

---

## 开发环境

从源码参与开发：

```bash
git clone https://github.com/OpenSportsLab/opensportslib.git
cd opensportslib
pip install -e .
```

### Conda 方式

如果更习惯 conda：

```bash
conda create -n osl python=3.12 pip
conda activate osl
pip install -e .
```

### 配置环境（PyTorch、CUDA 感知与可选依赖）
```bash
# 安装 PyTorch（自动检测 CPU/GPU）
opensportslib setup

# 可选：安装 PyTorch Geometric 支持。这会把已安装的
# Torch 栈替换为与 PyG 兼容的 PyTorch 2.12.1 配置。
opensportslib setup --pyg

# 可选：安装 DALI 支持
opensportslib setup --dali

# 可选：安装与 X-VARS 兼容的 VQA 依赖配置
opensportslib setup --vqa_xvars

# 可选：安装与 Qwen 兼容的 VQA 依赖配置
opensportslib setup --vqa_qwen
```

---

## 贡献

欢迎贡献。Pull request 必须指向 `dev`，每位与 GitHub 账号关联的提交作者在 `CLA check` 提示时，须接受[个人贡献者许可协议](.github/CLA.md)。

贡献流程见 [CONTRIBUTING.md](CONTRIBUTING.md)，架构与扩展说明见 [DEVELOPERS.md](DEVELOPERS.md)。

---

## 许可证

OpenSportsLib 采用双重许可。

### 开源许可
研究、学术和社区使用适用 [AGPL 3.0](LICENSE)。

### 商业许可
专有或商业部署请参阅 [LICENSE-COMMERCIAL](LICENSE-COMMERCIAL)。

---

## 引用

如果在研究中使用 OpenSportsLib，请引用本项目。

```bibtex
@misc{opensportslib,
  title={OpenSportsLib},
  author={OpenSportsLab},
  year={2026},
  howpublished={\url{https://github.com/OpenSportsLab/opensportslib}}
}
```

---

## 致谢

OpenSportsLib 是 OpenSportsLab 体育视频理解工作的一部分。来自 KAUST 的核心贡献者包括：

- [Jeet Vora](https://jeetv.github.io/) — 远程研究工程师
- [Dr. Merey Ramazanova](https://meryusha.github.io/) — 博士后
- [Dr. Silvio Giancola](https://www.silviogiancola.com/) — 研究科学家

同步的原生帧率 GAR 动作片段，以及可选的精确帧复现，见
[spotting 转分类转换器](docs/tools/spotting-to-classification.md)。

使用相同分类样本 ID 的全帧率跟踪片段，见
[跟踪转换脚本](docs/tools/spotting-tracking-to-classification.md)。
