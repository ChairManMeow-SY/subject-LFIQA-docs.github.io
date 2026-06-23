# 常见问题

## 启动与安装

### 启动时报 `ImportError: No module named 'Widgets'`

**原因**：未从仓库根目录启动。

**解决**：
```bash
cd /path/to/subject-LFIQA-software
python LFIQoE.py
```
不要在子目录中运行，也不要用绝对路径 `python /path/to/LFIQoE.py` 从其他目录启动。

### ffmpeg 找不到 / 被动模式视频无法生成

**原因**：ffmpeg 未安装或不在 PATH 中。

**解决**：
1. 确认 ffmpeg 已安装：终端执行 `ffmpeg -version`
2. Windows 用户需将 ffmpeg 的 `bin/` 目录添加到系统 PATH
3. 或者将 `ffmpeg.exe` 直接复制到软件根目录

### 被动模式报 `SceneResolution.json not found`

**原因**：仓库根目录缺少 `SceneResolution.json` 文件。

**解决**：在根目录创建该文件，内容格式见[软件操作说明](software-operation.md#sceneresolutionjson)。文件中需包含每个被动模式 LFI 的分辨率信息。

---

## 预处理

### 重对焦预处理报错

**原因**：缺少 `lambda.txt` 或 `depth.png`。

**解决**：
- 确认每个启用了重对焦的 LFI 文件夹中包含这两个文件
- 如果不需要重对焦，在 `Exp_Info` 中设置 `"Refocusing": "None"`
- 目前仅支持 Lytro 相机格式的密集光场，其他设备需要自定义预处理

### 预处理生成的文件打不开 / 显示异常

**原因**：可能是视角图像尺寸不一致，或 ffmpeg 编码参数不兼容。

**解决**：
1. 确认所有视角图像的宽高一致（与 JSON 配置中的 `Width` / `Height` 匹配）
2. 确认 angular 维度数与 `Angular_Width` / `Angular_Height` 一致
3. 查看 `Logs/` 目录下的日志文件获取具体错误

---

## 实验运行

### 评分界面显示不全 / 错位

**原因**：屏幕分辨率不匹配。

**解决**：
1. 调整 `Table_Font_Size` 和 `Hint_Font_Size` 适应屏幕
2. 在 `SoftwareConfig.json` 中检查屏幕设置
3. 多屏环境下确认 `Screen_Index` 指向正确的显示器

### 被动视频播放卡顿

**原因**：MPV 解码性能不足或视频编码参数过高。

**解决**：
1. 降低视频分辨率
2. 减少 `Loop_Times`
3. 在性能更好的电脑上运行

### 主动模式鼠标无法切换视角

**原因**：需要先右键唤醒。

**解决**：在图像上**右键点击**（任意位置），然后移动鼠标切换视角。这是设计特性，确保所有被试从同一视角开始。

### 被试不小心打错分怎么办

**原因**：评分后无法返回修改。

**解决**：这是设计特性（防止被试反复修改）。建议：
- 在 Training 阶段让被试充分练习
- 如果错误严重，可以在后处理时删除该条记录，重新运行 Post Processing

---

## 后处理

### 后处理结果显示空白或全为 0

**原因**：没有正式测试数据（Test），或数据格式异常。

**解决**：
1. 确认至少运行过 Run → Start Test
2. 确认 `SubjectResults/` 目录下有被试数据文件
3. 检查 `Save_Format` 与实际保存格式是否一致

### PLCC 值异常低

**原因**：被试评分一致性差。

**解决**：
- 检查 Training 阶段是否充分
- 确认被试理解了评分标准
- PLCC 反映单个被试与群体的相关性，低 PLCC 可能表示该被试的评分需要排除

---

## 其他

### 软件窗口闪退

**解决**：从终端运行 `python LFIQoE.py`，观察错误输出。常见原因：
- 缺少 Python 依赖包
- Python 版本过低（需要 3.9+）
- 显卡驱动不兼容（MPV 相关）

### 如何查看日志

日志保存在 `Logs/` 目录，文件名格式为 `YYYY-MM-DD.log`。关键调试信息会记录在此。

### 我可以修改源码吗

本项目采用 BSD 许可证，欢迎修改和分发。如需重新编译，参考[编译 exe](advanced.md#compile-exe)。

### 支持的 LFI 输入格式

- 视角图像：任意常见格式（PNG、JPG、BMP 等）
- 命名约定：视角文件按 XY 坐标命名
- 光场类型：密集光场（Dense）完整支持，稀疏光场（Sparse）部分支持

### 问题反馈

- 提交 [GitHub Issue](https://github.com/USTC-IMCL/subject-LFIQA-software/issues)
- 邮件联系：zsy7788@mail.ustc.edu.cn
- 附上日志文件可加速问题定位
