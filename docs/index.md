# subject-LFIQA 用户手册

**版本 3.0** | 主观光场图像质量评估（LFIQA）软件

## 软件简介

本软件用于主观光场图像质量评估实验（Subjective Light Field Image Quality Assessment）。与传统 2D 图像评估不同，光场图像具有多项可交互的特性，评估实验需要决定评估哪些特性以及如何向被试展示。

### 核心能力

| 特性 | 说明 |
|------|------|
| **立体视差** (Stereo parallax) | 通过 2D 或 3D 显示方式呈现双眼视差 |
| **运动视差** (Moving parallax) | 被试可切换视角，支持主动（鼠标）和被动（视频）两种模式 |
| **重对焦** (Refocusing) | 被试可改变对焦深度，支持主动（鼠标点击）和被动（视频）两种模式 |

### 比较模式

| 模式 | 说明 |
|------|------|
| **SingleStimulus** | 单刺激评分 — 每次呈现一张图像，被试独立评分 |
| **DoubleStimuli** | 双刺激评分 — 同时呈现原图与失真图，被试对比评分 |
| **PairComparison** | 配对比较 — 呈现两张失真图，被试选择更优者 |
| **DSCS-PC** | 自适应配对比较 — 先进行 DoubleStimuli 收集分数，据此自动生成配对进行 PairComparison |

### 技术架构

- **GUI 框架**: PySide6 (Qt for Python)
- **被动模式视频后端**: MPV
- **视频合成**: ffmpeg
- **支持平台**: Windows / Linux / macOS

## 用户群体

本软件面向**主观图像质量评估实验的设计者和操作者**。你通常需要：

1. 准备光场图像数据（原始图像和失真图像）
2. 编写实验配置文件（JSON）
3. 运行预处理生成展示素材
4. 组织被试进行评测
5. 运行后处理得到 MOS/PLCC/SROCC 等统计结果

## 文档导航

| 章节 | 适合 |
|------|------|
| [安装](installation.md) | 首次使用，搭建运行环境 |
| [快速上手](quick-start.md) | 最快跑通一个实验 |
| [实验 JSON 配置](experiment-json.md) | 完整配置字段参考 |
| [软件操作说明](software-operation.md) | 预处理 → 实验 → 后处理全流程 |
| [进阶主题](advanced.md) | 双文件夹模式、编译 exe 等 |
| [常见问题](faq.md) | 遇到问题先看这里 |

## 许可证与引用

本项目采用 BSD 许可证。如有问题请联系 zsy7788@mail.ustc.edu.cn，或提交 [GitHub Issue](https://github.com/USTC-IMCL/subject-LFIQA-software/issues)。

> **引用参考**: JPEG PLENO 提案中关于主观 LFIQA 的实验设计方法 [1]
