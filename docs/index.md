# subject-LFIQA User Manual

**Version 3.0** | Subjective Light Field Image Quality Assessment Software

## Overview

This software is designed for conducting **subjective light field image quality assessment (LFIQA)** experiments. Unlike traditional 2D image evaluation, light field images have several interactive features — you must decide which features to evaluate and how to present them to subjects before running an experiment.

### Core Capabilities

| Feature | Description |
|---------|-------------|
| **Stereo parallax** | Binocular disparity via 2D or 3D display |
| **Moving parallax** | Viewpoint switching — active (mouse) or passive (video) mode |
| **Refocusing** | Focal depth change — active (mouse click) or passive (video) mode |

### Comparison Modes

| Mode | Description |
|------|-------------|
| **SingleStimulus** | One image at a time — subject rates independently |
| **DoubleStimuli** | Reference and distorted image shown side-by-side for comparison |
| **PairComparison** | Two distorted images shown — subject picks the better one |
| **DSCS-PC** | Adaptive two-pass: DoubleStimuli scores feed into auto-generated pairwise comparisons |

### Tech Stack

- **GUI**: PySide6 (Qt for Python)
- **Passive video backend**: MPV
- **Video composition**: ffmpeg
- **Platforms**: Windows / Linux / macOS

## Who This Manual Is For

You are designing or running subjective image quality assessment experiments. You will typically:

1. Prepare light field image data (reference and distorted images)
2. Write an experiment configuration file (JSON)
3. Run preprocessing to generate display materials
4. Conduct evaluation sessions with human subjects
5. Run post-processing to obtain MOS / PLCC / SROCC results

## Document Navigation

| Section | For |
|---------|-----|
| [Installation](installation.md) | First-time setup |
| [Quick Start](quick-start.md) | Fastest path to a working experiment |
| [Experiment JSON](experiment-json.md) | Complete configuration field reference |
| [Operations](software-operation.md) | Preprocessing → experiment → post-processing full walkthrough |
| [Advanced](advanced.md) | Two-folder mode, compile to exe, multi-screen, etc. |
| [FAQ](faq.md) | Troubleshooting common issues |

## License & Citation

BSD License. For questions, contact zsy7788@mail.ustc.edu.cn or open a [GitHub Issue](https://github.com/USTC-IMCL/subject-LFIQA-software/issues).

> **Reference**: JPEG PLENO proposal on subjective LFIQA experimental design [1]
