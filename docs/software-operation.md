# Software Operations

Complete experiment workflow: **Preprocessing → Experiment Execution → Post-Processing**.

---

## Preprocessing

### Run Preprocessing from the GUI

Click **Run → Preprocessing**. The software processes all LFIs in Training and Test:

1. **Extract view images** — each angular view is rendered as a separate image in `show_views/`
2. **Generate refocused images** — depth-dependent refocused images are saved to `show_refocusing/` (only when `Refocusing` is not `None`)
3. **Produce passive videos** — view sequences or refocusing sequences are concatenated into video files (only when a feature is set to `Passive`)

Progress is shown in the log window. Preprocessing can take significant time for large LFIs or many views.

!!! warning "Refocusing requires extra files"
    When refocusing is enabled (`Refocusing: Active` or `Passive`), each LFI folder must contain:
    - `lambda.txt` — calibration file for dense light fields
    - `depth.png` — depth map
    Currently only **Lytro-camera** dense light fields are supported.

### Preprocessing Output Structure

After preprocessing, each LFI's distortion path contains:

```
<SRC or HRC path>/
├── show_views/
│   ├── 00_00.png     # Viewpoint (row=0, col=0)
│   ├── 00_01.png     # Viewpoint (row=0, col=1)
│   ├── ...
│   └── view.mp4      # Passive view-changing video
├── show_refocusing/
│   ├── 0.5.png       # Focal depth 0.5
│   ├── 1.0.png       # Focal depth 1.0
│   ├── ...
│   └── refocus.mp4   # Passive refocusing video
├── training/
│   └── PairComparison_<key>/
│       ├── show_views/...
│       └── show_refocusing/...
└── test/
    └── PairComparison_<key>/
        ├── show_views/...
        └── show_refocusing/...
```

!!! note "View naming convention"
    View files are named `<row>_<col>.png`, where row and col are coordinates in the angular grid.

### Custom Preprocessing

You may prefer to prepare display materials manually (e.g., using custom stitching or video encoding parameters). To do so:

1. Set `"Skip_Preprocessing": true` in the experiment JSON
2. Manually create `show_views/` and `show_refocusing/` folders following the structure above
3. Place your pre-generated materials into the corresponding folders

The software will read these files directly during the experiment, skipping automatic generation.

### SceneResolution.json

If using passive mode, the `SceneResolution.json` file in the repository root **must exist** and contain resolution information for every passive video:

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

Each entry's key is the LFI name (matching `Name` in the JSON config). Values are single-view dimensions (not stitched dimensions). Missing or incorrect resolution data will cause passive video playback errors.

---

## Running the Experiment

### Menu Workflow

```
Project → New              Create project, load experiment JSON
Run → Preprocessing        Generate display materials (unless skipped)
Run → Start Training       Begin training phase
Run → Start Test           Begin formal testing
Run → Post Processing      Compute statistics
```

### Training vs Test

| | Training | Test |
|---|----------|------|
| Purpose | Familiarize subjects with controls and scale | Collect formal rating data |
| Subject info recorded | No | Yes |
| Order | Sequential | Randomized |

### Subject Operations Guide

This section describes what **the subject** does during evaluation.

#### Active Mode

**View Changing (Active)**

1. **Right-click** anywhere on the image to wake up view switching
2. **Move the mouse** to change the viewpoint — mouse position maps to angular coordinates
3. When done exploring, press **Enter** to proceed to the scoring page

**Refocusing (Active)**

1. **Left-click** on the desired focal point in the image
2. The image refocuses to that depth
3. Activating refocusing temporarily disables view switching

#### Passive Mode

1. **Left-click** the image area to start video playback
2. Video loops automatically (number of cycles set by `Loop_Times`)
3. After playback ends, press **Enter** to proceed to the scoring page

#### Scoring

| Action | Key |
|--------|-----|
| Select rating level | :material-arrow-left-bold: :material-arrow-right-bold: **Left / Right arrow keys** |
| Confirm rating | :material-numeric-1: through :material-numeric-9: **Number keys** |
| Next page | :material-keyboard-return: **Enter** |

The scoring table appears below the image. Arrow keys move the cursor; number keys confirm the rating.

#### Pair Comparison

| Action | Key |
|--------|-----|
| Choose left image | :material-arrow-left-bold: **Left arrow** |
| Choose right image | :material-arrow-right-bold: **Right arrow** |

Selection is recorded immediately and advances to the next pair — no Enter confirmation needed.

!!! tip "Briefing subjects"
    Before starting the experiment, inform subjects that:
    - In active mode, right-click first to "wake up," then move the mouse
    - After scoring, press Enter to advance — there is no back button
    - Take breaks if feeling fatigued (if break intervals are configured)

### Subject Info Entry

When starting a test session, a subject information dialog appears. Fill in:

- Subject ID / name
- Any custom fields defined in the configuration

This information is saved alongside the rating data.

### Break Intervals

If `trials_per_session` or `minutes_per_session` is configured, the software displays a break prompt when the threshold is reached. Subjects can rest before continuing.

---

## Post-Processing

### Running Post-Processing

Click **Run → Post Processing**. The software will:

1. Aggregate all subject rating data
2. Compute **MOS** (Mean Opinion Score) — average subjective rating per HRC
3. Compute **PLCC** (Pearson Linear Correlation Coefficient) — per-subject Pearson correlation against the group mean
4. Compute **SROCC** (Spearman Rank Order Correlation Coefficient) — rank-order correlation

### Output Files

Results are saved to `<project>/SubjectResults/`:

| File | Content |
|------|---------|
| `MOS.csv` | Mean opinion score per HRC |
| `PLCC.csv` | Per-subject PLCC values |
| `SROCC.csv` | Per-subject SROCC values |
| `AllResults.xlsx` | All subjects' complete rating records |
| `all_subject_info.csv` | Subject information summary |

### Project Persistence

Project data is stored as a `.lfqoe` file under `Projects/<project_name>/`. This is a Python pickle file containing the full `ProjectInfo` object. Save manually via **Project → Save**, or the software prompts on exit.
