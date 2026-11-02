# CONTRIBUTING.md
本指南说明开发者扩展或维护 OpenSportsLib 时的工作流程与规范。

## AI 代理贡献
由 AI 代理驱动的开发，请遵循仓库根目录的 `AGENTS.md`。

## 1. 开发环境搭建
开始贡献前，请以可编辑（editable）模式搭建本地开发环境，这样你的改动会立即反映到包中。

#### 步骤 1：克隆仓库
```bash
git clone https://github.com/OpenSportsLab/opensportslib.git 
cd opensportslib
```
#### 步骤 2：创建虚拟环境
使用 Conda 管理依赖，并确保兼容 Python 3.12。
```bash
conda create -n osl python=3.12 pip
conda activate osl
```
#### 步骤 3：以可编辑模式安装
安装基础包；如需定位等特定任务，可再安装可选依赖：
```bash
# 以可编辑模式安装核心包
pip install -e .
```

#### 步骤 4：配置环境（感知 PyTorch、CUDA，以及可选依赖）
```bash
# 安装 PyTorch（自动检测 CPU/GPU）
opensportslib setup

# 可选：安装 PyTorch Geometric 支持
opensportslib setup --pyg

# 可选：安装 DALI 支持
opensportslib setup --dali
```

## 2. 分支与合并——开发者日常流程

#### 分支
*main* → 稳定、可发布
*dev* → 活跃开发的集成分支
*dev-<name>* → 开发者个人分支
*feature-<name>* → 新功能
*fix-<name>* → 缺陷修复

#### 规则
- ❌ 禁止直接推送到 `main`
- ❌ 禁止直接提交到 `dev`
- ✅ 始终从 `dev` 创建功能分支
- ✅ 始终使用 Pull Request
- ✅ PR 必须指向 `dev`，不要指向 `main`

### 1. 同步仓库
开始工作前，确认当前分支是 `dev`，并拉取最新更改。
```bash
git checkout dev
git pull origin dev
```

### 2. 创建功能分支
从 `dev` 创建新分支，命名应能说明用途。
```bash
git checkout -b feature-<feature_name>
```
命名示例：
- *feature-model*
- *feature-new-dataset*

### 3. 本地开发
经常提交，并遵循以下提交信息风格：

- *feat:* 新功能
- *fix:* 缺陷修复
- *refactor:* 代码清理
- *docs:* 文档更新

提交示例：
```bash
git add . 
or 
git add -u

git commit -m "feat: add model registry"
```

### 4. 推送分支（只需一次）
将功能分支推送到远程仓库。
```bash
git push origin feature/your-feature-name
```

### 5. 打开 Pull Request（PR）→ dev
发起 Pull Request，将你的分支合并回 `dev`。

包版本由 GitHub Actions 管理。指向 `dev` 的 PR 中，不要修改根目录 `pyproject.toml` 的包版本，也不要修改 `server/pyproject.toml` 中对 OpenSportsLib 的依赖锁定；必需的 Version Integrity 检查会拒绝这些改动。维护者准备功能发布线与稳定版本时，应遵循[发布管理指南](docs/developer/release-management.md)。

### 贡献者许可协议（CLA）

指向 `dev` 的 PR 中，每一位与 GitHub 关联的不同提交作者都必须接受 [OpenSportsLab 个人贡献者许可协议](.github/CLA.md)。当 `CLA check` 报告缺少签署时，列出的每位作者都必须在该 PR 中添加下面这句**原文**评论：

> I have read the OpenSportsLab Individual Contributor License Agreement and I hereby sign it.

该检查会在项目签署账本中记录 GitHub 账号、协议版本、文档哈希和接受时间。在所有提交作者都被记录之前，合并会被阻止。CLA 相关问题、通知或撤销请求，请使用 [OpenSportsLab GitHub 组织](https://github.com/OpenSportsLab)。未关联 GitHub 的提交身份必须先改为使用已关联 GitHub 的作者，才能通过该检查。

### 维护者的 CLA 配置

在将 `CLA check` 设为必需状态之前，创建一个名为 **CLA Signature Ledger** 的 issue，将其 issue 编号写入 [`.github/cla.yml`](.github/cla.yml) 的 `ledger_issue`，并把 CLA 工作流部署到仓库默认分支，以便 GitHub 能处理 `issue_comment` 事件。在仓库设置中保护 `dev`：要求 Pull Request、CI，以及来自 GitHub Actions 的 `CLA check`。除指定的仓库管理员，以及现有开发发布版本同步工作流所需的 `github-actions[bot]` 自动化之外，直接推送和绕过保护应保持关闭。配置完成后重新运行一个测试 PR，使 `CLA check` 出现在必需状态检查的选择列表中。

✅ PR 检查清单：
- [ ] 测试通过：现有逻辑仍然可用。
- [ ] 可在 GPU 上运行：代码兼容 CUDA 环境。
- [ ] 配置可用：YAML 配置能正确解析。
- [ ] 文档已更新：相关文档反映了你的改动。
