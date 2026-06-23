# Frequently Asked Questions

## Startup & Installation

### `ImportError: No module named 'Widgets'` on launch

**Cause**: Not launched from the repository root directory.

**Solution**:
```bash
cd /path/to/subject-LFIQA-software
python LFIQoE.py
```
Do not run from a subdirectory, and do not use an absolute path like `python /path/to/LFIQoE.py` from a different working directory.

### ffmpeg not found / passive video generation fails

**Cause**: ffmpeg is not installed or not on PATH.

**Solution**:
1. Verify ffmpeg is installed: run `ffmpeg -version` in a terminal
2. On Windows, add ffmpeg's `bin/` folder to the system PATH
3. Or copy `ffmpeg.exe` directly into the software root directory

### Passive mode error: `SceneResolution.json not found`

**Cause**: The `SceneResolution.json` file is missing from the repository root.

**Solution**: Create it in the root directory. Format details: [Operations](software-operation.md#sceneresolutionjson). It must contain resolution information for every LFI used in passive mode.

---

## Preprocessing

### Refocusing preprocessing fails

**Cause**: Missing `lambda.txt` or `depth.png`.

**Solution**:
- Ensure each LFI folder with refocusing enabled contains both files
- If you don't need refocusing, set `"Refocusing": "None"` in `Exp_Info`
- Currently only Lytro-camera dense light fields are supported; other devices require custom preprocessing

### Preprocessing output images are garbled or display incorrectly

**Cause**: Inconsistent view image dimensions, or ffmpeg encoding parameter mismatch.

**Solution**:
1. Verify all view images have consistent width/height (matching JSON `Width` / `Height`)
2. Verify the angular dimension counts match `Angular_Width` / `Angular_Height`
3. Check log files in `Logs/` for specific error messages

---

## Running Experiments

### Scoring interface is clipped or misaligned

**Cause**: Screen resolution mismatch.

**Solution**:
1. Adjust `Table_Font_Size` and `Hint_Font_Size` for your display
2. Check screen settings in `SoftwareConfig.json`
3. In multi-screen setups, verify `Screen_Index` points to the correct display

### Passive video playback is choppy

**Cause**: MPV decoding performance or high video encoding parameters.

**Solution**:
1. Reduce video resolution
2. Reduce `Loop_Times`
3. Run on a machine with better performance

### Active mode: mouse doesn't change the viewpoint

**Cause**: View switching needs to be "woken up" first.

**Solution**: **Right-click** anywhere on the image, then move the mouse. This is an intentional design — it ensures all subjects start from the same viewpoint.

### Subject entered a wrong score — can we go back?

**Cause**: No undo/back navigation by design.

**Solution**: This is intentional (prevents subjects from second-guessing). Suggestions:
- Let subjects practice sufficiently during Training
- If the error is severe, delete that record and re-run Post Processing

---

## Post-Processing

### Post-processing results are blank or all zeros

**Cause**: No formal test data, or malformed data format.

**Solution**:
1. Confirm at least one **Run → Start Test** session has been completed
2. Verify `SubjectResults/` contains subject data files
3. Check that `Save_Format` matches the actual saved format

### PLCC values are unusually low

**Cause**: Poor inter-subject rating consistency.

**Solution**:
- Check whether the Training phase was sufficient
- Confirm subjects understood the rating scale
- PLCC measures per-subject correlation with the group — low PLCC may indicate that subject's ratings should be excluded

---

## Other

### Software crashes on launch

**Solution**: Run `python LFIQoE.py` from a terminal and observe the error output. Common causes:
- Missing Python dependencies
- Python version too old (3.9+ required)
- GPU driver incompatibility (MPV-related)

### How to view logs

Logs are saved in `Logs/` with filenames formatted as `YYYY-MM-DD.log`. Critical debugging information is recorded there.

### Can I modify the source code?

Yes — this project is under the BSD License. Modifications and redistribution are welcome. To recompile, see [Compiling to exe](advanced.md#compiling-to-a-standalone-executable).

### Supported LFI input formats

- View images: any common format (PNG, JPG, BMP, etc.)
- Naming convention: view files named by XY coordinates
- Light field type: Dense fully supported; Sparse partially supported

### Reporting Issues

- Open a [GitHub Issue](https://github.com/USTC-IMCL/subject-LFIQA-software/issues)
- Email: zsy7788@mail.ustc.edu.cn
- Attach log files to speed up troubleshooting
