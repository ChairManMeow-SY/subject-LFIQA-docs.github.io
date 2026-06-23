# Installation

## Requirements

| Dependency | Version | Required |
|------------|---------|----------|
| Python | 3.9+ | :material-check: |
| ffmpeg | Any recent version | :material-check: (passive mode) |
| MPV | Bundled with python-mpv | :material-check: (passive mode) |
| OS | Windows / Linux / macOS | :material-check: |

## Setup Steps

### 1. Install Python Dependencies

```bash
pip install PySide6 xlsxwriter openpyxl numpy opencv-python python-mpv
```

### 2. Install ffmpeg

=== "Windows"
    - Download from [gyan.dev](https://www.gyan.dev/ffmpeg/builds/) (recommended: **release essentials** build)
    - Extract and add the `bin/` folder to your system `PATH`
    - Or copy `ffmpeg.exe` directly into the software root directory
    - Verify: run `ffmpeg -version` in a terminal

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

!!! warning "ffmpeg is essential for passive mode"
    If you plan to use **passive mode** (passive view changing or passive refocusing), ffmpeg is required. Active-only experiments can run without it, but installing it is recommended anyway.

### 3. Get the Software

=== "Run from source"
    ```bash
    git clone https://github.com/USTC-IMCL/subject-LFIQA-software.git
    cd subject-LFIQA-software
    python LFIQoE.py
    ```

=== "Use pre-built binary"
    Download the binary for your platform from [Releases](https://github.com/USTC-IMCL/subject-LFIQA-software/releases).
    Double-click to run. The binary bundles ffmpeg internally.

!!! important "Must run from repository root"
    When running from source, always execute `python LFIQoE.py` from the repository root directory. The software uses relative paths to locate `Widgets/`, `UI/`, and `Utils/` modules. Launching from a different working directory will cause `ImportError`.

## Verify Installation

Upon launching, you should see:

1. A main window with menu bar (Project, Run, Settings, About)
2. An empty central area (for project display)

If the window appears normally, installation is successful. If it crashes immediately, run from a terminal and check the error output.
