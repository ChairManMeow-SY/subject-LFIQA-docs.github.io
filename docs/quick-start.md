# Quick Start

The fastest path to a working experiment using the **two-folder mode**.

## Prerequisites

- [Installation](installation.md) completed, software launches successfully
- Light field image data available (or sample data)

## Two-Step Setup (Two-Folder Mode)

Two-folder mode is the quickest way to try the software: provide a training folder and a test folder, and the software auto-configures the rest.

### 1. Prepare Folders

Suppose you have two sets of light field images:

```
my_experiment/
├── training_lfi/       # Training set (subject practice)
│   ├── scene_1/
│   │   ├── view_00_00.png
│   │   ├── view_00_01.png
│   │   └── ...（multi-view images）
│   └── scene_2/
│       └── ...
└── test_lfi/           # Test set (formal scoring)
    ├── scene_A/
    │   └── ...
    └── scene_B/
        └── ...
```

### 2. Create a New Project

1. Launch the software, click **Project → New**
2. In the dialog:
   - **Training Path**: select `my_experiment/training_lfi/`
   - **Test Path**: select `my_experiment/test_lfi/`
   - Other settings can stay at defaults for now
3. Click **OK**

The software creates the project and loads the data automatically.

## Three Steps to Complete an Experiment

### Step 1: Configure

If using the full JSON configuration mode (instead of two-folder), write an experiment JSON. The core structure:

```json
{
    "Training": [...],      // Training LFI list
    "Test": [...],          // Test LFI list
    "Exp_Info": {           // Experiment settings
        "Display_Type": "2D",
        "View_Changing": "Active",
        "Refocusing": "None",
        "Comparison": "DoubleStimuli",
        "Save_Format": "Excel",
        ...
    }
}
```

Full field reference: [Experiment JSON](experiment-json.md).

### Step 2: Preprocess

Click **Run → Preprocessing**. The software automatically:

1. Extracts view images for each LFI (`show_views/`)
2. Generates refocused images (`show_refocusing/`, if configured)
3. Produces video files for passive mode (`view.mp4` / `refocus.mp4`)

!!! tip "Skipping preprocessing"
    If you have pre-generated `show_views/` and `show_refocusing/` folders, set `Skip_Preprocessing: true` in the JSON. See [Operations](software-operation.md#custom-preprocessing) for the required directory structure.

### Step 3: Run Experiment & Post-Process

1. **Click Run → Start Training** to begin the training phase (subject practice)
2. **Click Run → Start Test** to begin formal testing (scores are recorded)
3. After all subjects finish, **click Run → Post Processing** to compute statistics

Post-processing outputs:
- `MOS.csv` — Mean Opinion Score
- `PLCC.csv` — Pearson Linear Correlation Coefficient
- `SROCC.csv` — Spearman Rank Order Correlation Coefficient
- `AllResults.xlsx` — Complete results for all subjects

## Next Steps

- [Experiment JSON](experiment-json.md) — detailed field reference
- [Operations](software-operation.md) — complete operational walkthrough
- [FAQ](faq.md) — common issues and solutions
