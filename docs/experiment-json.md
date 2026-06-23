# 实验 JSON 配置

实验配置通过一个 JSON 文件定义，包含三个顶层 key：`Training`、`Test` 和 `Exp_Info`。

## 完整示例

以下是一个综合性示例，展示了大部分字段：

```json
{
    "Training": [
        {
            "Name": "Bikes",
            "Width": 625,
            "Height": 434,
            "Angular_Height": 11,
            "Angular_Width": 11,
            "Type": "Dense",
            "Angular_Format": "XY",
            "SRC": "/path/to/Bikes/Ori",
            "HRC": [
                {
                    "Distortion_Type": "HEVC",
                    "Distortion_Level": 1,
                    "Distortion_Path": "/path/to/Bikes/dist/HEVC/1"
                },
                {
                    "Distortion_Type": "JPEG",
                    "Distortion_Level": "2",
                    "Distortion_Path": "/path/to/Bikes/dist/JPEG/2"
                }
            ]
        }
    ],
    "Test": [
        {
            "Name": "Bikes",
            "Width": 625,
            "Height": 434,
            "Angular_Height": 11,
            "Angular_Width": 11,
            "Type": "Dense",
            "Angular_Format": "XY",
            "SRC": "/path/to/Bikes/Ori",
            "HRC": [
                {
                    "Distortion_Type": "HEVC",
                    "Distortion_Level": 1,
                    "Distortion_Path": "/path/to/Bikes/dist/HEVC/1"
                }
            ]
        }
    ],
    "Exp_Info": {
        "Display_Type": "2D",
        "Score_Levels": 5,
        "ThreeD_Type": "LeftRight",
        "View_Changing": "Active",
        "Refocusing": "Active",
        "Comparison": "DoubleStimuli",
        "Save_Format": "CSV",
        "PairWise_Path": "PairWise.json",
        "Skip_Preprocessing": false
    }
}
```

---

## Training / Test

定义训练集和测试集的光场图像。两者结构相同，区别在于：

- **Training** — 被试练习用，**不记录**评分结果
- **Test** — 正式实验，**记录**被试的每次评分

### LFI 条目字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `Name` | string | LFI 名称（用于显示和文件路径） |
| `Width` | int | 单视角图像宽度（像素） |
| `Height` | int | 单视角图像高度（像素） |
| `Angular_Width` | int | 角度维度宽度（水平视角数） |
| `Angular_Height` | int | 角度维度高度（垂直视角数） |
| `Type` | string | 光场类型：`"Dense"` 或 `"Sparse"` |
| `Angular_Format` | string | 视角排列格式：`"XY"` 等 |
| `SRC` | string | 原始图像（无失真）的根目录路径 |
| `HRC` | array | 失真版本列表（Hypothetical Reference Circuit） |

### HRC 条目字段

| 字段 | 类型 | 说明 |
|------|------|------|
| `Distortion_Type` | string | 失真类型标签（如 `"HEVC"`、`"JPEG"`、`"JPEG2000"`） |
| `Distortion_Level` | int 或 string | 失真级别（可以是数字或字符串，如 `1` 或 `"1"`） |
| `Distortion_Path` | string | 失真图像的根目录路径 |

!!! info "关于 Distortion_Level"
    `Distortion_Level` 可以是 `int` 或 `string`。代码内部将其统一转为字符串处理，
    因此 `1` 和 `"1"` 等价。建议保持同一实验中类型一致。

---

## Exp_Info

实验设置的核心部分。

### 显示设置

| 字段 | 可选值 | 说明 |
|------|--------|------|
| `Display_Type` | `"2D"` / `"3D"` | 显示模式。3D 需要支持立体显示的硬件 |
| `ThreeD_Type` | `"LeftRight"` / `"UpDown"` / `"Full"` | 3D 格式。仅 `Display_Type = 3D` 时有效 |

### 评分设置

| 字段 | 类型 | 说明 |
|------|------|------|
| `Score_Levels` | int | 评分等级数（如 5 表示 1-5 分） |
| `Score_Names` | array of string | 每项评分标准的名称，如 `["Please Rate the Quality"]` |
| `Score_Definition` | array of array | 每个等级的文字说明，如 `[["5. Excellent", "4. Good", ...]]` |
| `Score_Values` | array of array | 可选，自定义分值。如 `[[0, -1, -2, -3]]`（DSCS-PC 常用） |
| `Table_Font_Size` | int | 评分表字体大小（默认 20） |
| `Hint_Font_Size` | int | 提示文字字体大小（默认 60） |

### 比较模式

| 字段 | 可选值 | 说明 |
|------|--------|------|
| `Comparison` | 见下表 | 比较模式 |

**可选值：**

| 值 | 模式 | 被试操作 |
|----|------|----------|
| `"SingleStimulus"` | 单刺激 | 看一张图 → 打分 |
| `"DoubleStimuli"` | 双刺激 | 同时看原图和失真图 → 对比打分 |
| `"PairComparison"` | 配对比较 | 看两张失真图 → 选择更优者 |
| `"DSCS_PC_base"` | DSCS-PC (base) | 两阶段：先 DoubleStimuli，后自动配对比较 |
| `"DSCS_PC_ccg"` | DSCS-PC (CCG) | 同上，使用 CCG 变体配对策略 |

#### 比较模式详解

**SingleStimulus（单刺激）**

每次呈现一张图像（可能是原图或失真图），被试独立给出质量评分。适用于没有参考图像可比较的场景。

**DoubleStimuli（双刺激）**

同时呈现原图（左/上）和失真图（右/下），被试对比后评分。这是最常见的模式，因为有参考。

**PairComparison（配对比较）**

同时呈现两张失真图，被试选择哪个更好。需要使用 `PairWise_Path` 字段指定一个 pair list JSON 文件。
Pair list JSON 格式如下：

```json
{
    "training": {
        "0": {
            "lfi_name": "Bikes",
            "left": "HEVC",
            "left_level": 1,
            "right": "JPEG",
            "right_level": "2"
        },
        "1": { ... }
    },
    "test": {
        "0": { ... }
    }
}
```

**DSCS-PC（自适应配对比较）**

这是一种**两阶段模式**（但对外呈现为一个选择）：

1. **第一阶段** — DoubleStimuli，收集被试对每个 HRC 的评分
2. **中间** — 软件根据第一阶段分数自动生成配对对
3. **第二阶段** — PairComparison，被试比较自动生成的配对

DSCS-PC 专属字段：

| 字段 | 类型 | 说明 |
|------|------|------|
| `PC_Group_Index` | array of int | 配对分组索引，如 `[1, 2]` |
| `Maximum_Pairs` | int | 最大配对数量 |

### 视差与重对焦

这两个设置决定被试如何与光场图像交互。

| 字段 | 可选值 | 说明 |
|------|--------|------|
| `View_Changing` | `"Active"` / `"Passive"` / `"None"` | 视角切换方式 |
| `Refocusing` | `"Active"` / `"Passive"` / `"None"` | 重对焦方式 |

#### 模式说明

**Active（主动模式）**

| 功能 | 操作 |
|------|------|
| 视角切换 | **右键**点击唤醒 → 移动鼠标切换视角 |
| 重对焦 | **左键**点击图像中想对焦的位置 |

!!! info "为什么要右键唤醒"
    使用右键"唤醒"视角切换，而不是鼠标悬停直接切换，是为了**确保所有被试从同一视角开始**（通常是中心视角），避免首印象偏差。

**Passive（被动模式）**

使用预先录制的视频 (`view.mp4` / `refocus.mp4`) 自动播放。被试只需**左键点击**开始播放，不能交互控制。

被动模式下视频通过 MPV 播放，ffmpeg 合成。相关配置：

| 字段 | 类型 | 默认 | 说明 |
|------|------|------|------|
| `Auto_Play` | bool | — | 是否自动开始播放 |
| `Loop_Times` | int | — | 视频循环次数 |
| `FPS` | int | — | 播放帧率（`-1` 表示原始帧率） |
| `Auto_Transition` | bool | — | 播放结束后是否自动进入评分页面 |
| `Pause_Allowed` | bool | — | 是否允许暂停 |
| `First_Loop_Skip_Allowed` | bool | — | 是否允许跳过首次循环 |
| `Skip_Hint_Text` | string | — | 跳过提示文字 |

**None**

不使用该特性。例如只测视角切换不测重对焦时，将 `Refocusing` 设为 `"None"`。

!!! note "重对焦需要额外文件"
    如果使用重对焦功能（主动或被动），每个 LFI 文件夹中需要有：
    - `lambda.txt` — 密集光场的 lambda 校准文件
    - `depth.png` — 深度图
    目前仅支持 **Lytro 相机**拍摄的密集光场。稀疏光场的重对焦模块尚未实现。

### 数据路径

| 字段 | 类型 | 说明 |
|------|------|------|
| `Save_Format` | `"CSV"` / `"Excel"` | 结果保存格式 |
| `PairWise_Path` | string | Pair list JSON 的文件路径（仅 PairComparison 模式需要） |
| `Skip_Preprocessing` | bool | 是否跳过预处理（已有数据时设为 `true`） |
| `Two_Folder_Mode` | bool | 是否使用双文件夹简化模式 |

### 双文件夹模式

`Two_Folder_Mode: true` 时，`Training` 和 `Test` 不再是 LFI 数组，而是直接指向两个文件夹路径：

```json
{
    "Training": "/path/to/training/folder",
    "Test": "/path/to/test/folder",
    "Exp_Info": {
        "Two_Folder_Mode": true,
        ...
    }
}
```

软件会自动扫描文件夹中的 LFI 子目录。详见[进阶主题](advanced.md#two-folder-mode)。
