# Advanced Topics

## Two-Folder Mode

Two-folder mode is the fastest way to initialize an experiment — ideal when you already have preprocessed data.

### Enabling

Set in `Exp_Info`:

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

### Required Folder Structure

Each root folder should contain LFI subdirectories structured as follows:

```
training_root/
├── Scene_A/                   # Folder name → LFI Name
│   ├── 00_00.png              # SRC (reference) view (0,0)
│   ├── 00_01.png
│   ├── ...
│   ├── dist/
│   │   ├── HEVC/
│   │   │   ├── 1/             # Distortion level 1
│   │   │   │   ├── 00_00.png
│   │   │   │   └── ...
│   │   │   └── 2/             # Distortion level 2
│   │   └── JPEG/
│   │       └── 1/
│   │           └── ...
│   ├── lambda.txt             # (optional) Calibration file
│   └── depth.png              # (optional) Depth map
├── Scene_B/
│   └── ...
```

!!! note "Naming conventions in two-folder mode"
    - Top-level folder name = LFI `Name`
    - `dist/` subfolder name = `Distortion_Type`
    - Distortion-type subfolder name = `Distortion_Level`
    - Root-level image files = SRC (undistorted reference)

### Comparison with Full JSON Mode

| | Two-Folder Mode | Full JSON Mode |
|---|-----------------|----------------|
| Configuration complexity | Low | High |
| Flexibility | Fixed folder structure | Fully customizable |
| Metadata | Inferred from folder names | Manually specified |
| Best for | Existing data, quick pilots | Precise control, formal experiments |

Recommendation: use two-folder mode for quick validation; use full JSON mode for formal experiments to ensure precise configuration.

---

## Compiling to a Standalone Executable

Use PyInstaller to package the software into a single exe (Windows) for distribution to subjects.

```bash
pyinstaller --onefile -w \
    -p ./UI \
    -p ./Widgets \
    --add-data "<path/to/ffmpeg>:./" \
    --icon=icon.ico \
    LFIQoE.py
```

Options explained:
- `--onefile` — bundle into a single file
- `-w` — suppress console window
- `-p` — add Python module search paths
- `--add-data` — bundle ffmpeg executable into the exe
- `--icon` — set the exe icon

The compiled exe is placed in `dist/`.

---

## Software Settings

### SoftwareConfig.json

The root-level `SoftwareConfig.json` stores global settings:

```json
{
    "Logs_Path": "./Logs",
    "Log_Level": "DEBUG",
    "Thread_Num": 4
}
```

| Field | Description |
|-------|-------------|
| `Logs_Path` | Log file directory |
| `Log_Level` | Logging level (`DEBUG` / `INFO` / `WARNING` / `ERROR`) |
| `Thread_Num` | Number of parallel ffmpeg worker threads |

Modify these via **Settings → Software Setting** in the menu.

### Font Settings

Click **Settings → Font Setting** to adjust font sizes in the scoring interface:

- **Table Font Size** — scoring table text
- **Hint Font Size** — hint/instruction text

---

## Multi-Screen Setup

If your system has multiple displays, the software can run the experiment fullscreen on a specific screen while keeping the control window on another.

Set `Screen_Index` in the project settings (0 = primary, 1 = secondary). The scoring interface will go fullscreen on the designated display, leaving the main window on the other screen for the experimenter to monitor.

---

## Logging

During operation, all `print()` output and log messages are redirected to daily log files in the `Logs/` directory (e.g., `Logs/2026-06-23.log`).

When troubleshooting, check these log files for detailed error information.
