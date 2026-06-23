# 软件操作说明

完整的实验操作流程：**预处理 → 实验执行 → 后处理**。

---

## 预处理

### 软件内运行

点击菜单 **Run → Preprocessing**，软件将依次处理所有 Training 和 Test 中的 LFI：

1. **生成各视角图像** — 为每个视角提取图像，保存到 `show_views/` 文件夹
2. **生成重对焦图像** — 根据深度图计算不同对焦深度的图像，保存到 `show_refocusing/` 文件夹（仅当 `Refocusing` 不为 `None` 时）
3. **生成被动视频** — 将视角序列或重对焦序列拼接为视频文件（仅当某功能设置为 `Passive` 时）

预处理进度在日志窗口显示。如果 LFI 数量多或图像分辨率大，预处理可能需要较长时间。

!!! warning "重对焦需要额外文件"
    如果启用了重对焦功能（`Refocusing: Active` 或 `Passive`），每个 LFI 文件夹中必须有：
    - `lambda.txt` — 密集光场校准文件
    - `depth.png` — 深度图
    目前仅支持 **Lytro 相机**格式的密集光场。

### 预处理生成的文件结构

预处理完成后，每个 LFI 的失真路径下会生成：

```
<SRC 或 HRC 路径>/
├── show_views/
│   ├── 00_00.png     # 视角 (row=0, col=0)
│   ├── 00_01.png     # 视角 (row=0, col=1)
│   ├── ...
│   └── view.mp4      # 被动模式视角切换视频
├── show_refocusing/
│   ├── 0.5.png       # 对焦深度 0.5
│   ├── 1.0.png       # 对焦深度 1.0
│   ├── ...
│   └── refocus.mp4   # 被动模式重对焦视频
├── training/
│   └── PairComparison_<key>/
│       ├── show_views/...
│       └── show_refocusing/...
└── test/
    └── PairComparison_<key>/
        ├── show_views/...
        └── show_refocusing/...
```

!!! note "文件名编码"
    视角文件命名格式为 `<row>_<col>.png`，其中 row 和 col 是视角在角度网格中的位置。

### 自定义预处理 {#custom-preprocessing}

有时你可能希望手动准备展示素材（例如使用自定义的图像拼接方案或视频编码参数）。此时：

1. 在实验 JSON 中设置 `"Skip_Preprocessing": true`
2. 按照上述目录结构手动创建 `show_views/` 和 `show_refocusing/` 文件夹
3. 将准备好的素材放入对应文件夹

软件在运行实验时会直接读取这些文件，跳过自动生成步骤。

### SceneResolution.json

如果使用被动模式，仓库根目录的 `SceneResolution.json` 文件**必须存在**且包含所有被动视频的场景分辨率信息：

```json
{
    "Bikes": {
        "height": 434,
        "width": 625
    },
    "Bookshelf": {
        "height": 1200,
        "width": 1600
    }
}
```

每个条目的 key 是 LFI 名称（与 JSON 配置中的 `Name` 一致），value 是单视角图像的宽高（不是拼接后的尺寸）。缺失或错误的分辨率信息会导致被动视频播放异常。

---

## 实验执行

### 菜单操作流程

```
Project → New      新建项目，加载实验 JSON
Run → Preprocessing  生成展示素材（如未跳过）
Run → Start Training 开始训练阶段
Run → Start Test     开始正式测试
Run → Post Processing 生成统计结果
```

### Training vs Test

| | Training | Test |
|------|----------|------|
| 目的 | 让被试熟悉操作和评分标准 | 收集正式评分数据 |
| 记录被试 | 不记录 | 记录 |
| 随机化 | 按顺序 | 随机打乱 |

### 被试操作指南

以下是**被试**在实际评分过程中的操作方法。

#### 主动模式操作

**视角切换（Active View Changing）**

1. 在图像上**右键点击**任意位置唤醒视角切换
2. **移动鼠标**切换视角 — 鼠标位置对应视角坐标
3. 浏览完毕后按 **Enter** 进入评分页面

**重对焦（Active Refocusing）**

1. 在图像上**左键点击**想要对焦的位置
2. 图像会重新对焦到该位置
3. 激活重对焦后，视角切换会暂时关闭

#### 被动模式操作

1. **左键点击**图像区域开始播放视频
2. 视频自动循环播放（次数由 `Loop_Times` 决定）
3. 播放结束后按 **Enter** 进入评分页面

#### 评分操作

| 操作 | 按键 |
|------|------|
| 选择评分等级 | :material-arrow-left-bold: :material-arrow-right-bold: **左右方向键** |
| 确认评分 | :material-numeric-1: ~ :material-numeric-9: **数字键** |
| 翻到下一页 | :material-keyboard-return: **Enter** |

评分表显示在图像下方，方向键移动光标，数字键确认打分。

#### 配对比较操作

| 操作 | 按键 |
|------|------|
| 选择左侧图像 | :material-arrow-left-bold: **左方向键** |
| 选择右侧图像 | :material-arrow-right-bold: **右方向键** |

选择后立即记录并跳转到下一对，不需要按 Enter 确认。

!!! tip "被试提示"
    建议在实验开始前向被试说明：
    - 主动模式下需要先右键"唤醒"，再移动鼠标
    - 评分后按 Enter 进入下一张
    - 无法返回上一张，请确认后再打分
    - 如感到疲劳可以随时暂停（如有配置休息间隔）

### 被试信息录入

开始测试时，软件会弹出被试信息对话框，填写：

- 被试编号/姓名
- 其他自定义字段

这些信息会随评分数据一起保存。

### 休息间隔

如果配置了 `trials_per_session` 或 `minutes_per_session`，软件会在达到阈值时显示休息提示，被试可以休息后再继续。

---

## 后处理

### 运行后处理

点击菜单 **Run → Post Processing**，软件将：

1. 汇总所有被试的评分数据
2. 计算 MOS（Mean Opinion Score）— 每个 HRC 的平均主观评分
3. 计算 PLCC（Pearson Linear Correlation Coefficient）— 单个被试与其他人平均分的 Pearson 相关性
4. 计算 SROCC（Spearman Rank Order Correlation Coefficient）— 排序相关性

### 输出文件

结果保存在 `<project>/SubjectResults/` 目录下：

| 文件 | 内容 |
|------|------|
| `MOS.csv` | 每个 HRC 的平均主观评分 |
| `PLCC.csv` | 每个被试的 PLCC 值 |
| `SROCC.csv` | 每个被试的 SROCC 值 |
| `AllResults.xlsx` | 所有被试的全部评分明细 |
| `all_subject_info.csv` | 被试信息汇总 |

### 项目保存

项目数据以 `.lfqoe` 文件保存在 `Projects/<项目名>/` 目录下。这是一个 Python pickle 文件，包含 `ProjectInfo` 对象的完整状态。可以通过 **Project → Save** 手动保存，或在退出时自动提示保存。
