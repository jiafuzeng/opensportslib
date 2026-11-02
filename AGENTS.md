# AGENTS.md

本文档是 AI 编程代理为 OpenSportsLib 贡献代码时的操作手册。

## 目的与读者

- 读者：在本仓库中工作的 AI 编程代理（自主或半自主）。
- 目标：提供具体、可执行的贡献规则，保证开发安全且一致。

## 库的使命

OpenSportsLib 用于集中开展体育领域的 AI 开发。

- 库必须支持多种模态（视频、轨迹跟踪、图、语言，以及多模态组合）。
- 库必须通过统一框架支持多种任务。
- 每个任务都必须有清晰定义，并用 YAML 进行配置。

当前任务覆盖重点：

- 分类（classification）
- 定位（localization）

长期任务路线图包括（非穷尽）：

- 检索（retrieval）
- 描述与稠密描述（description and dense description）
- 视觉问答（VQA）
- 表征学习（representation learning）
- 球员检测（player detection）
- 跟踪（tracking）
- 识别与重识别（identification and re-identification）
- 比赛状态重建（game state reconstruction）
- 场地定位（field localization）
- 主动学习工具（active learning tooling）
- 测试时自适应工具（test-time adaptation tooling）

## 任务与模块架构规则

- 每个任务必须作为独立的任务模块/包装器开发，并拥有清晰的接口。
- 任务专属逻辑应留在任务模块内。
- 只有在逻辑确实被多个任务复用时，才应提取为共享逻辑。
- 若某个组件被共享，应实现为跨任务模块，使用中性命名，且不得隐式耦合到某一个任务。
- 任务边界必须明确：任务 API 负责编排，共享模块提供可复用的构建块。

## 事实来源层级

- `AGENTS.md` 定义代理的执行流程与贡献护栏。
- `CONTRIBUTING.md` 与 `DEVELOPERS.md` 仍是更宽泛的政策与架构参考。
- 若内容重叠：
  - 日常代理操作以 `AGENTS.md` 为准，
  - 额外的项目背景与设计理由以 `CONTRIBUTING.md` 和 `DEVELOPERS.md` 为准。

## 环境初始化

使用以下命令搭建工作环境：

```bash
git clone https://github.com/OpenSportsLab/opensportslib.git
cd opensportslib

conda create -n osl python=3.12 pip
conda activate osl

pip install -e .

# 安装 PyTorch（自动检测 CPU/GPU）
opensportslib setup

# 可选依赖
opensportslib setup --pyg
opensportslib setup --dali
```

## 必须遵守的贡献流程

1. 从 `dev` 开始：

```bash
git checkout dev
git pull origin dev
```

2. 从 `dev` 创建功能或修复分支。
3. 打开以 `dev` 为目标的 PR（不要指向 `main`）。
4. PR 中每个与 GitHub 关联的提交作者，在必需的 `CLA check` 提示时必须接受个人 CLA；详见 [CONTRIBUTING.md](CONTRIBUTING.md)。
5. 提交信息使用以下前缀：
   - `feat:` 新功能
   - `fix:` 缺陷修复
   - `refactor:` 内部清理或重构
   - `docs:` 文档更新

## 代码变更规则

- 默认保持公共 API 不变。
- 若破坏性 API 变更不可避免：
  - 在 PR/提交说明中明确注明 `BREAKING CHANGE`，
  - 在同一次变更中更新所有受影响的文档与测试。
- 保持任务包装器的一致性：
  - 共享契约放在 `BaseTaskModel`，
  - 任务专属行为放在任务包装器中（`ClassificationModel`、`LocalizationModel` 等）。
- 预测结果的保存必须通过 `save_predictions(...)` 显式完成；避免在 `infer()` 中产生隐式的磁盘写入副作用。

## 测试约定（强制）

打开 PR 之前，运行唯一的测试入口：

```bash
bash scripts/run_tests.sh
```

当 API、函数签名或行为发生变化时，更新相关测试，包括：

- 冒烟测试（smoke tests）
- API 契约测试（API contract tests）
- 集成子集测试（integration subset tests）

## 文档约定

当 API 行为发生变化时，在同一个 PR 中更新相关文档：

- `README.md`
- `opensportslib/apis/README.md`
- `docs/tni/tni.md` 和/或 `docs/tni/config-guide.md`（适用时）

## PR 检查清单（代理可执行）

- [ ] 分支从 `dev` 创建
- [ ] PR 目标为 `dev`
- [ ] 测试通过（`bash scripts/run_tests.sh`）
- [ ] 已针对行为/API 变更更新文档
- [ ] 已审查向后兼容性
- [ ] 提交/PR 说明包含 API 影响摘要
- [ ] 若为破坏性变更：明确的 `BREAKING CHANGE` 说明，并更新面向迁移的文档与测试

## 相关文档

- `CONTRIBUTING.md`
- `DEVELOPERS.md`
