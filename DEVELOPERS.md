## 📁 目录结构

以下是 `OpenSportsLib` 仓库的详细目录结构，说明核心模块、配置与文档的组织方式。

---

### 根目录

- **`README.md`**  
  项目概览、功能列表、安装说明和快速开始示例。

- **`pyproject.toml`**  
  项目元数据、构建系统要求，以及依赖列表。

- **`MANIFEST.in`**  
  确保非 Python 文件（例如 YAML 配置）被包含进包分发中。

---

## `opensportslib/`（源代码）

包含框架核心逻辑的主包。

---

### `apis/`
训练与推理的高层入口。

- **`classification.py`**  
  基于视频的动作识别与犯规分类任务 API。

- **`localization.py`**  
  时序动作定位（temporal action spotting）任务 API。

---

### `core/`
框架的内部引擎。

- **`trainer/`**  
  各任务训练与推理循环的实现。

- **`loss/`**  
  自定义损失函数（例如交叉熵，以及专门的定位损失）。

- **`optimizer/` 与 `scheduler/`**  
  优化策略与学习率调度的集中构建器。

- **`sampler/`**  
  专用数据采样器，例如用于处理不平衡数据集的加权采样器。

- **`utils/`**  
  核心工具，用于：
  - DDP 设置  
  - 配置解析  
  - 检查点保存  
  - 视频处理  

---

### `models/`
用于灵活搭建模型的模块化架构组件。

- **`backbones/`、`necks/`、`heads/`**  
  用于构建神经网络架构的层与构建器。

- **`base/`**  
  基类，以及按模态划分的实现  
  （基于视频、基于跟踪，或端到端模型）。

- **`utils/`**  
  共享层与专用模块，例如：
  - TSM  
  - GSM  
  - ASFormer  

- **`builder.py`**  
  根据配置文件组装完整模型的集中逻辑。

---

### `datasets/`
各类足球相关任务的数据读取与预处理逻辑。

- **`classification_dataset.py`**  
  动作识别与犯规分类的数据加载器。

- **`localization_dataset.py`**  
  与 NVIDIA DALI 集成的高性能加载器，用于时序动作定位。

- **`builder.py`**  
  根据 YAML 设置编排数据集的实例化。

---

### `config/`
定义任务参数的模板与配置文件。

- **`classification/default.yaml`**  
  犯规分类任务的配置模板。

- **`localization/default.yaml`**  
  动作定位任务的配置模板。

- **`graph_tracking_classification/`**  
  基于图的跟踪模型的专用配置  
  （例如 GraphConv、GIN）。

---

### `metrics/`
评估逻辑与性能度量工具。

- **`classification_metric.py`**  
  计算准确率、精确率、召回率和 F1。

- **`localization_metric.py`**  
  计算时序定位任务的平均精度（mAP）。


### 👨‍💻 新开发者从哪里开始

从公共 API 入手：
```bash
opensportslib/apis/classification.py
opensportslib/apis/localization.py
```
它们负责：加载配置、创建 trainer、执行训练、提供推理入口。  
大多数工作流都从这里开始。

### 📦 添加新数据集

在以下位置工作：
```bash
opensportslib/datasets/
opensportslib/datasets/builder.py
```
步骤：

1. 创建数据集类。

2. 在 `datasets/builder.py` 中注册它。


### 🧠 添加新模型

在以下位置工作：
```bash
opensportslib/models/
opensportslib/models/builder.py
```
步骤：

1. 在以下目录之一中实现模型：
   - `backbones/`
   - `necks/`
   - `heads/`
   - `base/`

2. 在 `models/builder.py` 中注册该模型。


### ⚙️ 修改配置
正式实验配置位于：
```bash
opensportslib/configs/**/*.yaml
```
仅为兼容与迁移覆盖而保留的旧版夹具配置位于：
```bash
opensportslib/legacy_config/*.yaml
```
常见修改项：
- TYPE
- DATA
- MODEL
- TRAIN
- SYSTEM

### 🔁 修改训练循环

要改变训练行为，编辑：
```bash
opensportslib/core/trainer/
```
可以在这里修改：

- 前向传播  
- 损失计算  
- 日志  
- 验证  
- 检查点保存  

### 高层工作流

YAML 配置 -> API（apis/）-> 数据集（datasets/）-> 模型（models/）-> Trainer（core/trainer/）-> 指标（metrics/）
