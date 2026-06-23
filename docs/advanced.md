# 进阶主题

## 双文件夹模式 {#two-folder-mode}

双文件夹模式是最快的实验初始化方式，适合已有预处理数据的场景。

### 启用方式

在 `Exp_Info` 中设置：

```json
{
    "Training": "/path/to/training_root",
    "Test": "/path/to/test_root",
    "Exp_Info": {
        "Two_Folder_Mode": true,
        "Skip_Preprocessing": true,
        ...
    }
}
```

### 文件夹结构要求

每个文件夹应包含多个 LFI 子目录，每个子目录的结构如下：

```
training_root/
├── Scene_A/                   # LFI 名称自动取文件夹名
│   ├── 00_00.png              # 原始图像 (SRC)，视角 (0,0)
│   ├── 00_01.png
│   ├── ...
│   ├── dist/
│   │   ├── HEVC/
│   │   │   ├── 1/             # 失真级别 1
│   │   │   │   ├── 00_00.png
│   │   │   │   └── ...
│   │   │   └── 2/             # 失真级别 2
│   │   └── JPEG/
│   │       └── 1/
│   │           └── ...
│   └── lambda.txt             # (可选) 重对焦校准文件
│   └── depth.png              # (可选) 深度图
├── Scene_B/
│   └── ...
```

!!! note "文件夹名的含义"
    在双文件夹模式下：
    - 顶层文件夹名 = LFI 名称（`Name`）
    - `dist/` 下的子文件夹名 = 失真类型（`Distortion_Type`）
    - 失真类型下的子文件夹名 = 失真级别（`Distortion_Level`）
    - 根目录的图像文件 = SRC（原始无失真图像）

### 与完整 JSON 模式的对比

| | 双文件夹模式 | 完整 JSON 模式 |
|------|-------------|---------------|
| 配置复杂度 | 低 | 高 |
| 灵活性 | 文件夹结构固定 | 完全自定义 |
| 元数据 | 从文件夹名推断 | 手动指定 |
| 适用场景 | 已有数据、快速实验 | 精确控制、复杂实验 |

建议：先用双文件夹模式快速验证，正式实验用完整 JSON 模式确保配置精确。

---

## 编译为独立可执行文件 {#compile-exe}

使用 PyInstaller 将软件打包成单个 exe 文件（Windows），方便分发给被试使用。

```bash
pyinstaller --onefile -w \
    -p ./UI \
    -p ./Widgets \
    --add-data "<ffmpeg路径>:./" \
    --icon=icon.ico \
    LFIQoE.py
```

参数说明：
- `--onefile` — 打包为单个文件
- `-w` — 不显示控制台窗口
- `-p` — 添加 Python 模块搜索路径
- `--add-data` — 将 ffmpeg 可执行文件打包到 exe 中
- `--icon` — 设置 exe 图标

编译后的 exe 在 `dist/` 目录下。

---

## 软件设置

### SoftwareConfig.json

根目录的 `SoftwareConfig.json` 保存软件级设置：

```json
{
    "Logs_Path": "./Logs",
    "Log_Level": "DEBUG",
    "Thread_Num": 4
}
```

| 字段 | 说明 |
|------|------|
| `Logs_Path` | 日志文件保存路径 |
| `Log_Level` | 日志级别（`DEBUG` / `INFO` / `WARNING` / `ERROR`） |
| `Thread_Num` | ffmpeg 并行处理线程数 |

可以通过软件菜单 **Settings → Software Setting** 修改这些值。

### 字体设置

点击 **Settings → Font Setting** 可调整评分界面中的字体大小。有两个参数：

- **Table Font Size** — 评分表字体
- **Hint Font Size** — 提示文字字体

---

## 多屏环境

如果电脑连接了多个显示器，软件支持在指定屏幕上全屏显示实验界面。

在项目设置中指定 `Screen_Index`（0 为主屏幕，1 为副屏）。评分界面将在对应屏幕上全屏显示，主窗口留在另一个屏幕上供实验操作者监控。

---

## 日志系统

软件运行时所有 `print()` 输出和日志信息会被重定向到 `Logs/` 目录下按日期命名的日志文件（如 `Logs/2026-06-23.log`）。

如遇问题，请查看日志文件获取详细错误信息。
