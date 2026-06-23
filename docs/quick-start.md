# 快速上手

本指南用**最简单的路径**帮你跑通第一个实验。

## 前提

- 已完成[安装](installation.md)，软件可以正常启动
- 手头有光场图像数据（或使用示例数据）

## 两步启动（双文件夹模式）

如果你只是想快速试用，使用**双文件夹模式**最快：只需准备两个文件夹，一个放训练集、一个放测试集。

### 1. 准备文件夹

假设你有两组光场图像，每组一个文件夹：

```
my_experiment/
├── training_lfi/       # 训练集（用于被试练习）
│   ├── scene_1/
│   │   ├── view_00_00.png
│   │   ├── view_00_01.png
│   │   └── ...（多视角图像）
│   └── scene_2/
│       └── ...
└── test_lfi/           # 测试集（正式评分）
    ├── scene_A/
    │   └── ...
    └── scene_B/
        └── ...
```

### 2. 新建项目

1. 打开软件，点击 **Project → New**
2. 在弹出的对话框中：
   - **Training Path**: 选择 `my_experiment/training_lfi/`
   - **Test Path**: 选择 `my_experiment/test_lfi/`
   - 其他设置按需求选择（可以先保持默认）
3. 点击 **OK**

软件会自动创建项目并加载数据。

## 三步跑完一个实验

### 第 1 步：配置

如果使用完整的 JSON 配置模式（而非双文件夹），需要编写实验 JSON。以下是配置的核心结构：

```json
{
    "Training": [...],      // 训练集 LFI 列表
    "Test": [...],          // 测试集 LFI 列表
    "Exp_Info": {           // 实验设置
        "Display_Type": "2D",
        "View_Changing": "Active",
        "Refocusing": "None",
        "Comparison": "DoubleStimuli",
        "Save_Format": "Excel",
        ...
    }
}
```

详细字段说明见 [实验 JSON 配置](experiment-json.md)。

### 第 2 步：预处理

点击菜单 **Run → Preprocessing**，软件会自动：

1. 为每个 LFI 生成各视角图像（`show_views/`）
2. 生成重对焦图像（`show_refocusing/`，如有配置）
3. 为被动模式生成视频文件（`view.mp4` / `refocus.mp4`）

!!! tip "跳过预处理"
    如果你已经提前准备好了 `show_views/` 和 `show_refocusing/` 文件夹，可以设置 `Skip_Preprocessing: true` 跳过此步骤。目录结构要求见[软件操作说明](software-operation.md#custom-preprocessing)。

### 第 3 步：开始实验 & 后处理

1. **点击 Run → Start Training** 开始训练阶段（被试练习）
2. **点击 Run → Start Test** 开始正式测试（记录评分）
3. 被试完成所有评分后，**点击 Run → Post Processing** 生成统计结果

Post Processing 会输出：
- `MOS.csv` — Mean Opinion Score
- `PLCC.csv` — Pearson Linear Correlation Coefficient
- `SROCC.csv` — Spearman Rank Order Correlation Coefficient
- `AllResults.xlsx` — 所有被试的汇总结果

## 下一步

- [实验 JSON 配置](experiment-json.md) — 了解每个配置字段的含义
- [软件操作说明](software-operation.md) — 完整的操作流程细节
- [常见问题](faq.md) — 遇到报错来这里看看
