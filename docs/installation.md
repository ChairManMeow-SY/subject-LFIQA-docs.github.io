# 安装

## 环境要求

| 依赖 | 版本 | 必需 |
|------|------|------|
| Python | 3.9+ | :material-check: |
| ffmpeg | 任意较新版本 | :material-check:（被动模式） |
| MPV | 随 python-mpv 安装 | :material-check:（被动模式） |
| 操作系统 | Windows / Linux / macOS | :material-check: |

## 安装步骤

### 1. 安装 Python 依赖

```bash
pip install PySide6 xlsxwriter openpyxl numpy opencv-python python-mpv
```

### 2. 安装 ffmpeg

=== "Windows"
    - 从 [gyan.dev](https://www.gyan.dev/ffmpeg/builds/) 下载（推荐 **release essentials** 版本）
    - 解压后将 `bin/` 目录添加到系统 `PATH` 环境变量
    - 或者将 `ffmpeg.exe` 直接放到软件根目录
    - 验证：在终端执行 `ffmpeg -version`

=== "Linux (Ubuntu/Debian)"
    ```bash
    sudo apt install ffmpeg
    ffmpeg -version
    ```

=== "macOS"
    ```bash
    brew install ffmpeg
    ffmpeg -version
    ```

!!! warning "ffmpeg 是必需的"
    如果你使用**被动模式**（被动视差切换或被动重对焦），必须安装 ffmpeg。主动模式的实验可以不安装，但建议都装。

### 3. 获取软件

=== "从源码运行"
    ```bash
    git clone https://github.com/USTC-IMCL/subject-LFIQA-software.git
    cd subject-LFIQA-software
    python LFIQoE.py
    ```

=== "使用编译好的二进制文件"
    从 [Releases](https://github.com/USTC-IMCL/subject-LFIQA-software/releases) 下载对应平台的二进制文件，
    双击运行即可。二进制文件已内置 ffmpeg。

!!! important "必须从仓库根目录运行"
    如果从源码运行，请确保在仓库根目录执行 `python LFIQoE.py`。软件内部通过相对路径加载 `Widgets/`、`UI/`、`Utils/` 等模块，从其他目录启动会导致 `ImportError`。

## 验证安装

启动软件后，你应该看到：

1. 主窗口（带有菜单栏：Project、Run、Settings、About）
2. 中央空白区域（用于项目展示）

如果窗口正常显示，说明安装成功。如果闪退，请在终端运行并查看错误信息。
