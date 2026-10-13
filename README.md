# 从零搭建 PyTorch 学习环境：Conda、Python 与 Jupyter Notebook

适用对象：刚刚高中毕业、第一次接触 Python 环境配置的同学。  
教程整理日期：2026 年 10 月 14 日。

## 1. 我们要搭建什么？

完成后，你能在浏览器中的 Jupyter Notebook 编写 Python，并使用 PyTorch 做计算。

| 名称 | 用来做什么 | 可以怎样理解 |
| --- | --- | --- |
| Python | 执行我们编写的程序 | 能读懂 Python 代码的工具 |
| 软件包（package） | 提供别人已经写好的功能 | 可以安装的工具箱 |
| PyTorch | 做张量计算、构建和训练神经网络 | 人工智能课程的工具箱 |
| Conda | 管理环境、Python 版本和软件包 | 为不同课程准备独立工具柜 |
| 虚拟环境 | 将一组 Python 和软件包隔离起来 | 本课程专用的工具柜 |
| pip | 安装 Python 软件包 | 工具柜里另一种安装工具 |
| Jupyter Notebook | 将代码、结果和说明放在同一份笔记里 | 可运行代码的实验笔记本 |
| 内核（kernel） | 在后台实际执行 Notebook 代码 | 笔记本使用的 Python 引擎 |

**虚拟环境不是虚拟机，也不需要安装另一个操作系统。** 一个环境可以服务一门课，另一个环境可以服务别的项目，它们的软件版本可以不同。

本教程采用：**Conda 管理环境、Python 和 Jupyter；在同一个 Conda 环境中，用 pip 安装官方 PyTorch。** PyTorch 当前官方安装页面提供 pip 安装方式，因此使用 Conda 并不意味着所有软件都必须通过 `conda install` 安装。

## 2. 本课程使用的版本与设备要求

为了统一课堂，我们选用一组固定的教学版本，而不是追求最新版本：

| 软件 | 教学选择 | 原因 |
| --- | --- | --- |
| Python | 3.11 | 支持下文所选 PyTorch 和 Notebook，适合基础教学 |
| PyTorch | 2.7.1 | 固定版本，便于同学之间对照结果 |
| Jupyter Notebook | 7.x | 现代 Notebook 界面 |
| ipykernel | 由 Conda 选择兼容版本 | 让 Jupyter 找到本课程的 Python |

这里固定 Python 的次版本和 PyTorch 的版本；Notebook、ipykernel 及其依赖的具体版本由 Conda 解析。它不是所有依赖都完全锁定的配置，教师可在试装成功后导出环境。

默认先用 **CPU**：学习基本张量、自动求导和小模型不要求独立显卡。CPU 训练大型模型会比较慢，后续再按教师要求配置 GPU。

建议使用 Windows 10/11 64 位、Apple Silicon Mac 或较新的 Linux x86_64 电脑，并准备网络和数 GB 可用磁盘空间。Windows ARM、Intel Mac、旧操作系统或特殊设备请先告诉老师：不能直接假设下文的固定版本都有对应安装包。

## 3. 先学会在哪里输入命令

本教程有两种代码块：

- 标为 `bash` 的命令：输入终端或 Conda 提示符；本教程主流程中的这些命令也适用于 Windows 的 Miniforge Prompt。
- 标为 `python` 的代码：输入 Jupyter Notebook 的代码单元格。

每次复制一行命令，按 Enter（回车），等待完成后再执行下一行。不要复制代码块外的说明，也不要自己加上 `$`、`>>>` 或 `(base)`。这些是提示符，不是命令的一部分。

安装时如果问 `Proceed ([y]/n)?`，输入 `y` 后回车表示继续。命令可能会下载很多文件，暂时没有输出不一定是出错；若出现错误，请保留完整报错。

## 4. 安装 Conda

### 目的

获得 `conda` 命令。推荐安装 **Miniforge**：它包含 Conda，默认使用 conda-forge 软件源，不必一开始下载完整 Anaconda 的大量软件。

如果你已经安装了 Miniconda 或 Anaconda，并且 `conda --version` 正常，直接跳到第 5 步，不必重复安装。

### 操作：Windows

1. 打开官方页面：<https://github.com/conda-forge/miniforge>。
2. 在下载表格中选择 Windows x86_64 安装程序，通常叫 `Miniforge3-Windows-x86_64.exe`。
3. 运行安装程序，建议选择只为当前用户安装（Just Me）。
4. 使用默认安装位置或简单的英文路径。不需要勾选“Add to PATH”来修改全局路径。
5. 安装完成后，在开始菜单搜索并打开 **Miniforge Prompt**。

之后本教程的终端命令都在这个窗口输入。

### 操作：macOS

1. 打开“苹果菜单 → 关于本机”，确认芯片类型。
2. Apple M 系列芯片选择 macOS arm64 安装程序；Intel Mac 需要先让老师确认安装器及 PyTorch 版本的支持情况。
3. 从上述官方页面下载适合你的 `.sh` 安装程序。
4. 打开“终端”。先输入 `bash` 加一个空格，再把下载的 `.sh` 文件拖入终端，最后回车。这会得到类似下面的命令；文件名以实际下载的为准：

```bash
bash ~/Downloads/Miniforge3-MacOSX-arm64.sh
```

5. 按提示查看并接受许可，安装位置通常直接回车使用默认值。
6. 当安装程序询问是否初始化 Conda 时，选择 `yes`。
7. 关闭终端，再打开一个新的终端窗口。

### 操作：Linux

从上述页面下载与机器架构对应的 Linux `.sh` 安装程序。在终端输入 `bash` 加空格，拖入安装程序的完整路径后回车；按提示接受许可、使用默认位置、同意初始化 Conda。完成后重新打开终端。

### 检查

```bash
conda --version
```

如果看到类似 `conda 26.x.x`，说明 Conda 可以使用。数字不必和示例完全一致。

## 5. 创建本课程的独立环境

### 目的

创建名为 `torch-class` 的环境，并一次安装 Python、pip、Notebook 和内核支持。不要把课程的软件都装到默认的 `base` 环境。

### 操作：在终端输入

```bash
conda create -n torch-class --override-channels -c conda-forge python=3.11 pip "notebook>=7,<8" ipykernel
```

| 命令部分 | 意思 |
| --- | --- |
| `conda create` | 新建环境 |
| `-n torch-class` | 环境名为 torch-class |
| `--override-channels -c conda-forge` | 这次只从 conda-forge 获取 Conda 软件包 |
| `python=3.11` | 安装 Python 3.11 系列 |
| `pip` | 安装当前环境的 pip |
| `"notebook>=7,<8"` | 安装 Notebook 7 系列；引号保护版本条件中的符号 |
| `ipykernel` | 安装 Notebook 执行 Python 代码所需的组件 |

等待 Conda 列出安装计划，输入 `y` 后回车。

### 检查

```bash
conda env list
```

列表里应出现 `torch-class`。创建只需要做一次；以后使用时只需要激活。

## 6. 激活环境，并确认 Python 用对了

### 目的

告诉当前终端：“接下来的 Python 和安装操作都使用本课程环境。”

### 操作

```bash
conda activate torch-class
python --version
python -c "import sys; print(sys.executable)"
python -m pip --version
```

### 检查

- 提示符通常会出现 `(torch-class)`。
- Python 版本应为 `Python 3.11.x`。
- Python 路径应指向 `torch-class` 环境，通常包含 `envs/torch-class` 或 Windows 的 `envs\torch-class`。
- pip 输出的安装路径也应属于这个环境。

`python -c` 表示直接执行引号中的一小段 Python。`sys.executable` 显示当前实际运行的 Python 在哪里。

**每打开一个新终端，都需要重新执行 `conda activate torch-class`。** 在一个窗口中激活，不会自动激活其他窗口。

## 7. 安装 PyTorch：按操作系统二选一

### 目的

把 PyTorch 安装进已经激活的 `torch-class` 环境。包名是 `torch`，代码中也写 `import torch`。

`python -m pip` 的意思是“使用当前 Python 的 pip”，可以减少把软件装进另一个 Python 的错误。

### Windows / Linux：CPU 版本

```bash
python -m pip install torch==2.7.1 --index-url https://download.pytorch.org/whl/cpu
```

`==2.7.1` 固定版本；`--index-url` 指定本次安装使用官方 CPU 软件包仓库。它不会永久更改 Conda 的软件源。

### Apple Silicon macOS

```bash
python -m pip install torch==2.7.1
```

macOS 使用这一条，不要照抄 Windows/Linux 的 CPU 仓库命令。第一节课仍可使用 CPU；Apple GPU 加速在后文介绍。

### 检查：所有平台都执行

```bash
python -m pip check
python -c "import torch; print(torch.__version__); print(torch.tensor([1, 2, 3]) + 10)"
```

应看到：

```text
No broken requirements found.
2.7.1（可能带有 +cpu 等后缀）
tensor([11, 12, 13])
```

这里的版本说明是示意，不会原样输出括号内文字。出现张量计算结果说明 PyTorch 已能在终端运行。`pip check` 检查声明的依赖关系；运行计算才是功能检查。

> 软件混用原则：先装 Conda 软件包，再用 pip 安装 PyTorch。不要同时用 Conda 和 pip 反复覆盖同一个包。今后若大量修改 Conda 依赖导致环境冲突，优先创建新环境。

## 8. 给 Jupyter 注册本课程内核

### 目的

让 Notebook 的内核列表明确显示本课程环境。浏览器打开了 Notebook，不代表它一定在使用正确的 Python。

### 操作：仍在已激活的终端中

```bash
python -m ipykernel install --user --name torch-class --display-name "Python (torch-class)"
jupyter kernelspec list
```

- `--user`：给当前电脑用户注册内核。
- `--name`：内部名称。
- `--display-name`：Notebook 界面里的名称。

列表中应有 `torch-class`。这一步正常情况下也只需要做一次。

## 9. 创建课程文件夹，启动 Notebook

### 目的

将作业集中放在一个文件夹，并从这里启动 Notebook，方便找到和保存文件。文件夹存代码和作业；Conda 环境存 Python 和软件包，两者不是一回事。

### 第一次操作

下面的命令会在当前终端所在位置创建课程文件夹；也可以先用文件管理器建好文件夹，再在终端输入 `cd` 加空格并拖入该文件夹路径。

```bash
mkdir pytorch-course
cd pytorch-course
python -m notebook
```

如果文件夹已经存在，不必再次运行 `mkdir`，直接进入已有文件夹。

浏览器通常自动打开 Notebook。如果没有打开，复制终端里以 `http://localhost:` 或 `http://127.0.0.1:` 开头、带有 `token` 的完整地址到浏览器。

**保持这个终端窗口开启。** 它正在运行 Notebook 服务。浏览器是操作界面，代码在你自己的电脑上执行；地址中的 token 不要发给别人。

## 10. 新建笔记本并完成第一次实验

### 操作

1. 在 Notebook 文件列表中，点击 **New / 新建**。
2. 选择 **Python (torch-class)**。不同版本界面略有差异，也可能在创建后弹出内核选择窗口。
3. 如果已有笔记本，使用 **Kernel / 内核 → Change Kernel / 切换内核**，选择 `Python (torch-class)`。
4. 点击笔记本名称，将它改成 `01_environment_check.ipynb`。
5. 在第一个代码单元格输入下文代码，按 **Shift + Enter** 运行。

### 实验一：检查环境

```python
import sys
import torch

print("Python 版本：", sys.version)
print("Python 路径：", sys.executable)
print("PyTorch 版本：", torch.__version__)
print("NVIDIA CUDA 可用：", torch.cuda.is_available())
```

Python 应为 3.11，路径应属于 `torch-class`，PyTorch 应为 2.7.1（可带后缀）。默认 CPU 安装时 CUDA 为 `False` 是正常的，Mac 也不能用这个值判断 Apple GPU 是否可用。

### 实验二：用张量计算

张量（tensor）可以先理解为装数字的容器：可以是一组数字，也可以是表格或更高维数据。

```python
x = torch.tensor([1.0, 2.0, 3.0])
y = x * 2 + 1
print(y)
```

预期输出：

```text
tensor([3., 5., 7.])
```

### 实验三：体验自动求导

```python
x = torch.tensor(3.0, requires_grad=True)
y = x ** 2
y.backward()
print(x.grad)
```

预期输出：`tensor(6.)`。

如果还没有学过导数，只需知道：PyTorch 能自动计算“输入稍微变化时，结果变化有多快”，这项能力以后会用于训练模型。每次重新运行这个完整单元格都会新建 `x`，避免旧梯度累加。

### 保存

点击工具栏保存按钮或 **File / 文件 → Save / 保存**。保存后的 `.ipynb` 文件包含代码、说明和保存时的输出。

Notebook 的单元格可以乱序运行，但变量依赖实际运行顺序。提交作业前可使用 **Restart Kernel and Run All / 重启内核并运行全部**，确认从上到下也能执行。

## 11. 下次上课怎么打开？怎样关闭？

### 每次上课

1. Windows 打开 Miniforge Prompt；macOS/Linux 打开终端。
2. 激活环境。
3. 进入上次的课程文件夹；下文路径仅为示例，请替换成你自己的真实路径。
4. 启动 Notebook，打开已有 `.ipynb` 文件。

```bash
conda activate torch-class
cd "你的课程文件夹完整路径"
python -m notebook
```

不需要重新创建环境、重新安装软件或重新注册内核。

### 下课关闭

1. 保存笔记本。
2. 回到运行 Notebook 的终端，按 **Ctrl + C**（Mac 也是 Control，不是 Command）。
3. 如果询问是否关闭服务，按提示输入 `y` 后回车。
4. 如需退出课程环境，执行：

```bash
conda deactivate
```

只关闭浏览器标签页，通常不会停止后台 Notebook 服务。

## 12. 常见问题

| 现象 | 原因与处理 |
| --- | --- |
| `conda` 不是命令 / command not found | Windows 先确认打开的是 Miniforge Prompt；Mac/Linux 重新打开终端，确认安装时完成初始化。仍失败时将安装位置和报错交给老师，不要随机改 PATH。 |
| `EnvironmentNameNotFound` | 执行 `conda env list`，核对是否创建了 `torch-class`、名称是否拼错。 |
| 环境已存在 | 不必再次创建，直接 `conda activate torch-class`。 |
| `ModuleNotFoundError: No module named 'torch'` | 检查终端是否激活了环境，Notebook 是否选中正确内核；在 Notebook 打印 `sys.executable` 与终端结果对照。 |
| 终端能 import torch，Notebook 不能 | 多半是内核选错；切换为 `Python (torch-class)`，重启内核后重跑。 |
| 内核列表没有本课程环境 | 激活环境，再执行第 8 步注册命令；刷新 Notebook 页面。 |
| `No matching distribution found` | 核对 Python 版本、系统架构和命令。旧系统、Intel Mac、ARM Windows 等可能没有对应 wheel，请老师按官方支持情况调整版本。 |
| 下载超时、SSL 报错、连接失败 | 检查网络、系统时间与校园网络限制，将完整报错给老师。中国网络环境下官方仓库可能访问较慢，软件源应由教师统一安排；不要关闭 SSL 验证。 |
| CUDA 显示 `False` | CPU 安装是正常的；只有使用 NVIDIA GPU 版本并具备兼容驱动时，才需要进一步检查。 |
| Notebook 没自动打开 | 使用终端给出的完整本地地址；端口不一定是 8888。 |
| 代码左侧一直是 `[*]` | 内核仍在工作；可使用“中断内核”。重启内核会清空内存中的变量，之后需要重跑前面的单元格。 |
| 文件找不到 | 检查启动 Notebook 前进入了哪个文件夹，以及笔记本是否已经保存。 |

遇到问题时，请提供：操作系统、芯片类型、执行的命令、完整报错和 Python 路径。不要只发“装不了”。

## 13. 可选：GPU 与图像工具（按老师要求操作）

### NVIDIA GPU：Windows / Linux

初学阶段可先完成 CPU 主流程。需要加速时，让老师确认显卡和驱动，再按官方版本页面选择与课程版本匹配的 CUDA 安装命令：<https://pytorch.org/get-started/previous-versions/>。

`nvidia-smi` 可以查看驱动和 GPU；其中显示的 CUDA 版本不等于你已安装的 CUDA Toolkit 版本。不要看到一个数字就自行照抄 CUDA 安装命令。普通预编译 PyTorch 使用通常不需要另装完整 CUDA Toolkit，但需要兼容的 NVIDIA 驱动。

建议另建 GPU 环境，复用第 5～8 步并更换环境名与 PyTorch 安装命令，避免在已经完成的 CPU 环境里反复覆盖。GPU 安装后用 `torch.cuda.is_available()` 验证；即使为 `True`，代码也需要将模型和数据移到 GPU 才会使用它。

### Apple Silicon GPU：MPS

在 Notebook 中检查：

```python
print("MPS 可用：", torch.backends.mps.is_available())
```

MPS 是 Apple GPU 的计算接口；是否可用还取决于系统与硬件。入门代码默认仍在 CPU 上运行，不需要为了第一节课调整设备。

### 后续图像课程需要 torchvision 时

先关闭 Notebook 服务，在已激活的环境中执行对应平台的一条命令。

Windows / Linux CPU：

```bash
python -m pip install torch==2.7.1 torchvision==0.22.1 --index-url https://download.pytorch.org/whl/cpu
```

Apple Silicon macOS：

```bash
python -m pip install torch==2.7.1 torchvision==0.22.1
```

PyTorch 和 torchvision 要使用官方对应版本，不要独立随意升级。基础张量课程无需安装 torchvision 或 torchaudio。

## 14. 教师附录：留存班级环境配置

建议教师先在实际教学电脑上试装，再让学生操作。本教程的命令和版本对应关系已按官方资料核对，但未在学生的每一种操作系统上实机安装验证。

在验证成功的环境中，进入课程文件夹后执行：

```bash
conda env export > environment.yml
python -m pip freeze > pip-packages.txt
```

`environment.yml` 记录 Conda 环境（通常也包含 pip 安装的软件），`pip-packages.txt` 补充记录 pip 看到的软件版本。导出文件可能包含本机路径及平台特定依赖：它们便于同平台复现和排查，不保证跨 Windows、Mac、Linux 直接通用。

同平台迁移时，教师检查 `environment.yml`，删除末尾本机的 `prefix:` 路径，并确认 pip 部分对 CPU/CUDA 仓库的要求；不能假设导出文件保留了原始 `--index-url`。基础创建命令和平台对应的 PyTorch 安装命令也应一并保留。

本教程主流程在 pip 安装前完成 Conda 软件包安装，减少混用引起的依赖冲突。若课程后续需要 NumPy、Matplotlib 等工具，教师可把它们加入第 5 步的创建命令，在统一试装后再发给学生。

## 15. 完成检查表

- [ ] `conda --version` 有输出。
- [ ] `conda env list` 中有 `torch-class`。
- [ ] 激活后 Python 是 3.11，路径属于课程环境。
- [ ] PyTorch 是 2.7.1，`pip check` 没有报告依赖损坏。
- [ ] Notebook 选中了 `Python (torch-class)`。
- [ ] Notebook 内的 Python 路径与终端一致。
- [ ] 张量计算输出 `[3., 5., 7.]`，自动求导输出 `6.`。
- [ ] 已保存 `01_environment_check.ipynb`，知道下次怎样打开。

## 官方参考资料

1. Conda 安装与发行版说明：<https://docs.conda.io/projects/conda/en/stable/user-guide/install/>
2. Miniforge 官方安装说明与下载：<https://github.com/conda-forge/miniforge>
3. Conda 环境管理（含 pip 使用原则）：<https://docs.conda.io/projects/conda/en/stable/user-guide/tasks/manage-environments.html>
4. PyTorch 当前安装说明：<https://pytorch.org/get-started/locally/>
5. PyTorch 历史版本与配套版本：<https://pytorch.org/get-started/previous-versions/>
6. Jupyter 安装说明：<https://jupyter.org/install>
7. IPython 内核注册说明：<https://ipython.readthedocs.io/en/stable/install/kernel_install.html>

本教程使用固定教学版本。今后若更换版本，应同时检查 Python 支持范围、操作系统/架构支持及 PyTorch 配套包版本，并完成课堂设备试装。
