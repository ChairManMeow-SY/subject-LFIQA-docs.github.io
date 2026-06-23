# Experiment JSON Configuration

An experiment is defined by a single JSON file with three top-level keys: `Training`, `Test`, and `Exp_Info`.

## Complete Example

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

Defines the light field images for training and test phases. Both have the same structure; the difference is:

- **Training** — practice phase; scores are **not recorded**
- **Test** — formal phase; **every rating is recorded**

### LFI Entry Fields

| Field | Type | Description |
|-------|------|-------------|
| `Name` | string | LFI name (used for display labels and file paths) |
| `Width` | int | Single-view image width (pixels) |
| `Height` | int | Single-view image height (pixels) |
| `Angular_Width` | int | Angular dimension width (number of horizontal views) |
| `Angular_Height` | int | Angular dimension height (number of vertical views) |
| `Type` | string | Light field type: `"Dense"` or `"Sparse"` |
| `Angular_Format` | string | View arrangement format, e.g. `"XY"` |
| `SRC` | string | Path to the root directory of the original (undistorted) images |
| `HRC` | array | List of distorted versions (Hypothetical Reference Circuits) |

### HRC Entry Fields

| Field | Type | Description |
|-------|------|-------------|
| `Distortion_Type` | string | Distortion type label (e.g. `"HEVC"`, `"JPEG"`, `"JPEG2000"`) |
| `Distortion_Level` | int or string | Distortion level (can be numeric or string, e.g. `1` or `"1"`) |
| `Distortion_Path` | string | Path to the root directory of distorted images |

!!! info "About Distortion_Level"
    `Distortion_Level` accepts both `int` and `string`. Internally, all values are converted to strings, so `1` and `"1"` are equivalent. Keeping the type consistent within an experiment is good practice.

---

## Exp_Info

Core experiment settings.

### Display Settings

| Field | Values | Description |
|-------|--------|-------------|
| `Display_Type` | `"2D"` / `"3D"` | Display mode. 3D requires stereo-capable hardware |
| `ThreeD_Type` | `"LeftRight"` / `"UpDown"` / `"Full"` | 3D format. Only applies when `Display_Type = 3D` |

### Scoring Settings

| Field | Type | Description |
|-------|------|-------------|
| `Score_Levels` | int | Number of rating levels (e.g. 5 = 1–5 scale) |
| `Score_Names` | array of string | Labels for each scoring criterion, e.g. `["Please Rate the Quality"]` |
| `Score_Definition` | array of array | Text description for each level, e.g. `[["5. Excellent", "4. Good", ...]]` |
| `Score_Values` | array of array | Optional custom numeric values. E.g. `[[0, -1, -2, -3]]` (common for DSCS-PC) |
| `Table_Font_Size` | int | Font size for the scoring table (default 20) |
| `Hint_Font_Size` | int | Font size for hint text (default 60) |

### Comparison Mode

| Field | Values | Description |
|-------|--------|-------------|
| `Comparison` | See table below | Comparison mode |

**Available modes:**

| Value | Mode | Subject Action |
|-------|------|----------------|
| `"SingleStimulus"` | Single stimulus | View one image → rate |
| `"DoubleStimuli"` | Double stimulus | View reference + distorted side by side → compare and rate |
| `"PairComparison"` | Pair comparison | View two distorted images → pick the better one |
| `"DSCS_PC_base"` | DSCS-PC (base) | Two-pass: DoubleStimuli, then auto-generated pair comparison |
| `"DSCS_PC_ccg"` | DSCS-PC (CCG) | Same as above, with CCG variant pairing strategy |

#### Comparison Mode Details

**SingleStimulus**

One image per trial (may be reference or distorted). Subject rates quality independently. Suitable when no reference is available for comparison.

**DoubleStimuli**

Reference (left/top) and distorted (right/bottom) images shown simultaneously. Subject compares and rates. The most common mode due to the availability of a reference.

**PairComparison**

Two distorted images side by side. Subject chooses the better one. Requires a `PairWise_Path` field pointing to a pair list JSON:

```json
{
    "training": {
        "0": {
            "lfi_name": "Bikes",
            "left": "HEVC",
            "left_level": 1,
            "right": "JPEG",
            "right_level": "2"
        }
    },
    "test": {
        "0": { ... }
    }
}
```

**DSCS-PC (Adaptive Pair Comparison)**

A **two-pass mode** presented as a single choice:

1. **Pass 1** — DoubleStimuli collects scores for each HRC
2. **Interlude** — Software auto-generates comparison pairs from first-pass scores
3. **Pass 2** — PairComparison on the generated pairs

DSCS-PC specific fields:

| Field | Type | Description |
|-------|------|-------------|
| `PC_Group_Index` | array of int | Pair-group indices, e.g. `[1, 2]` |
| `Maximum_Pairs` | int | Maximum number of generated pairs |

### View Changing & Refocusing

Control how subjects interact with the light field.

| Field | Values | Description |
|-------|--------|-------------|
| `View_Changing` | `"Active"` / `"Passive"` / `"None"` | Viewpoint switching mode |
| `Refocusing` | `"Active"` / `"Passive"` / `"None"` | Refocusing mode |

#### Active Mode

| Function | How to interact |
|----------|-----------------|
| View changing | **Right-click** to wake → move mouse to change viewpoint |
| Refocusing | **Left-click** on the image at the desired focal point |

!!! info "Why right-click to wake?"
    The right-click "wake-up" mechanism ensures **all subjects start from the same viewpoint** (typically the center view), avoiding first-impression bias from hover-activated switching.

#### Passive Mode

Pre-recorded videos (`view.mp4` / `refocus.mp4`) play automatically. Subjects **left-click** to start playback and cannot interactively control the content.

Passive mode uses MPV for playback and ffmpeg for video composition. Related settings:

| Field | Type | Default | Description |
|-------|------|---------|-------------|
| `Auto_Play` | bool | — | Auto-start playback |
| `Loop_Times` | int | — | Number of video loop cycles |
| `FPS` | int | — | Playback frame rate (`-1` = original) |
| `Auto_Transition` | bool | — | Auto-advance to scoring page after playback ends |
| `Pause_Allowed` | bool | — | Allow pausing |
| `First_Loop_Skip_Allowed` | bool | — | Allow skipping the first loop |
| `Skip_Hint_Text` | string | — | Hint text for skip |

#### None

Disables the feature. For example, set `Refocusing` to `"None"` when only evaluating view changing.

!!! note "Refocusing requires extra files"
    When refocusing is enabled (active or passive), each LFI folder must contain:
    - `lambda.txt` — calibration file for dense light fields
    - `depth.png` — depth map
    Currently only **Lytro-camera dense light fields** are supported. Sparse light field refocusing is not yet implemented.

### Data Paths & Modes

| Field | Type | Description |
|-------|------|-------------|
| `Save_Format` | `"CSV"` / `"Excel"` | Results file format |
| `PairWise_Path` | string | Path to pair list JSON (PairComparison mode only) |
| `Skip_Preprocessing` | bool | Skip automatic preprocessing when data already exists |
| `Two_Folder_Mode` | bool | Use simplified two-folder project initialization |

### Two-Folder Mode

When `Two_Folder_Mode: true`, `Training` and `Test` are direct folder paths instead of LFI arrays:

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

The software scans the folders for LFI subdirectories automatically. See [Advanced](advanced.md#two-folder-mode) for details.
