# 简介
---
- **定义**：FFmpeg 是一个跨平台的命令行工具，用于处理音视频文件，支持几乎所有格式（如 MP4、AVI、MTS、TS、MP3、FLAC 等）。
- **核心组件**：
    - ffmpeg：主程序，用于转码、剪辑、合并等。
    - ffprobe：分析音视频文件信息。
    - ffplay：简单播放器，用于预览音视频。
- **开源协议**：LGPL 或 GPL（取决于编译选项），免费使用。
- **支持平台**：Windows、Linux、macOS 等。
- **安装**：
    - 官网下载：[https://ffmpeg.org/download.html](https://ffmpeg.org/download.html)
    - Windows：通过 winget install ffmpeg 或手动下载。
    - Linux：sudo apt install ffmpeg（Ubuntu/Debian）或 sudo dnf install ffmpeg（Fedora）。
    - macOS：brew install ffmpeg。
    - 验证：ffmpeg -version 检查版本和支持的编码器/解码器。
# 核心功能
---
FFmpeg 的功能涵盖音视频处理的方方面面，以下是主要功能：

1. **格式转换**：将一种音视频格式转换为另一种（如 .MTS 转 .mp4）。
2. **编码/解码**：支持 H.264、H.265、VP9、AAC、MP3 等多种编解码器。
3. **剪辑/分割**：裁剪音视频片段。
4. **合并**：拼接多个音视频文件。
5. **提取/替换**：提取音频、视频流或替换音频/字幕。
6. **缩放/裁剪**：调整分辨率、裁剪画面。
7. **滤镜**：应用视频滤镜（如锐化、去噪、旋转）或音频滤镜（如均衡器）。
8. **流媒体**：支持直播推流/拉流（如 RTMP、HLS）。
9. **元数据编辑**：修改音视频文件的元数据（如标题、作者）。
10. **分析文件**：通过 ffprobe 查看文件详细信息。

# 基本命令结构
---
FFmpeg 的命令通常遵循以下结构：

```bash
ffmpeg [全局选项] -i 输入文件 [输入选项] [输出选项] 输出文件
```

- **全局选项**：影响整个命令，如 -y（覆盖输出文件）、-n（不覆盖）。
- **输入选项**：作用于输入文件，如 -ss（起始时间）、-t（持续时间）。
- **输出选项**：作用于输出文件，如 -c:v（视频编码器）、-c:a（音频编码器）。
- **常用选项**：
    - `-i`：指定输入文件。
    - `-c:v`：视频编码器（如 libx264、copy）。
    - `-c:a`：音频编码器（如 aac、mp3）。
    - `-vf`：视频滤镜（如 scale=1280:720）。
    - `-map`：选择输入流的映射（如 -map 0:v 选择第一个输入的视频流）。

# 常见用法和命令示例
---
## 格式转换
---
- TS/MTS/AVI 转 MP4（简单转换）：
	```bash
	ffmpeg -i input.MTS -c:v libx264 -c:a aac output.mp4
	```
	- `-i input.MTS`：指定输入的 .MTS 文件。
	- `-c:v libx264`：使用 H.264 编码器将视频编码为 MP4 兼容的格式（H.264 是 MP4 的标准视频编码）。
	- `-c:a aac`：将音频编码为 AAC（MP4 常用的音频格式）。
	- `output.mp4`：输出的 .mp4 文件名。

- TS/MTS/AVI 转 MP4（重新编码，优化兼容性）：

	```
	ffmpeg -i input.ts -c:v libx264 -preset fast -crf 23 -c:a aac -b:a 192k -movflags faststart output.mp4
	```
	- `-preset fast`：平衡速度和质量。
	- `-crf 23`：控制视频质量（0-51，值越小质量越高）。
	- `-movflags faststart`：将元数据移到文件开头，优化 Windows 预览。
	- 适用：.ts、.MTS（AVCHD）、.avi 等。

- 无损转换（直接复制流，适用于 H.264 视频）：

	```bash
	ffmpeg -i input.MTS -c:v copy -c:a aac -movflags faststart output.mp4
	```
	- 仅当视频编码为 H.264，音频需转为 AAC（MP4 不支持 AC3）。

## 检查文件信息
---
- 查看音视频编码、格式、分辨率等：
	```bash
	ffprobe -i input.MTS
	```
- 或更详细
	```bash
	ffprobe -show_streams -show_format input.MTS
	```
	- `-show_streams`：显示每个流的详细信息。
	- `-show_format`：显示容器格式和元数据。
	- `-print_format json`：以 JSON 格式输出，便于程序化解析。
- 输出
	- ffmpeg -i 输出可能很长，关注 Input #0 和 Stream 部分即可。
	- 某些复杂文件可能不显示所有流，改用 ffprobe -show_streams。
	- 为确保 .mp4 文件预览，输出需为 H.264 + AAC + -movflags faststart。

## 剪辑视频
---
- 裁剪从第 10 秒开始的 30 秒片段：
	```bash
	ffmpeg -i input.mp4 -ss 10 -t 30 -c:v copy -c:a copy output.mp4
	```
	- -ss：起始时间（秒或 hh:mm:ss）。
	- -t：持续时间。

## 调整分辨率
---
- 缩放到 720p：
	```bash
	ffmpeg -i input.MTS -vf scale=1280:720 -c:v libx264 -preset fast -crf 23 -c:a aac output.mp4
	```

## 提取音频
---
- 提取 MP3 音频：
	```bash
	ffmpeg -i input.mp4 -vn -c:a mp3 output.mp3
	```
	- -vn：禁用视频流。

## 合并视频
---
- 合并多个 .mp4 文件（需先准备 files.txt）：
	```bash
	ffmpeg -f concat -i files.txt -c copy output.mp4
	```
	- files.txt 示例：
		```text
		file 'video1.mp4'
		file 'video2.mp4'
		```

## 添加字幕
---
- 嵌入字幕文件
	```bash
	ffmpeg -i input.mp4 -vf subtitles=subs.srt -c:v libx264 -c:a copy output.mp4
	```

## 批量转换
---
- 将所有 .MTS 转为 .mp4（Windows CMD）：
	```bash
	for %f in (*.MTS) do ffmpeg -i "%f" -c:v libx264 -preset fast -crf 23 -c:a aac -b:a 192k -movflags faststart "converted_%f.mp4"
	```
- Linux/macOS：
	```bash
	for f in *.MTS; do ffmpeg -i "$f" -c:v libx264 -preset fast -crf 23 -c:a aac -b:a 192k -movflags faststart "converted_$f.mp4"; done
	```

## 修复元数据
---
- 重新封装可以修复元数据，同时保持音视频质量不变，解决视频不能预览问题
	```bash
	ffmpeg -i input.mp4 -c:v copy -c:a copy -map_metadata 0 -movflags faststart output.mp4
	```
	- `-i input.mp4`：指定输入文件。
	- `-c:v copy -c:a copy`：视频和音频流直接复制，不重新编码。
	- `-map_metadata 0`：保留原始元数据。
	- `-movflags faststart`：将 MOOV atom 移到文件开头，优化预览和流式播放。
	- `output.mp4`：输出修复后的文件。


# 关键参数详解
---
## 视频编码器（-c:v）
---
- `libx264`：H.264 编码器，广泛兼容，适合 MP4、Web 等。
- `libx265`：H.265/HEVC，压缩率更高，但兼容性稍差。
- `copy`：直接复制视频流，无重新编码。
- `libvpx-vp9`：VP9 编码，适合 WebM 或现代浏览器。

## 音频编码器（-c:a）
---
- `aac`：MP4 标准音频格式，兼容性好。
- `mp3`：广泛支持，适合音频提取。
- `copy`：直接复制音频流（如输入已是 AAC）。

## 质量控制
---
- `-crf`（Constant Rate Factor）：控制 H.264/H.265 质量，0（无损）到 51（最差），默认 23。
- `-b:v`：指定视频比特率（如 2M 表示 2Mbps）。
- `-preset`：编码速度/质量平衡，选项包括 ultrafast、fast、medium（默认）、slow。

## 滤镜（-vf）
---
- `scale=w:h`：调整分辨率（如 scale=1920:1080）。
- `crop=w:h:x:y`：裁剪画面。
- `fps=fps`：设置帧率。
- `rotate=angle`：旋转视频（如 rotate=90）。

## 元数据优化
---
- `-movflags faststart`：将 MP4 元数据移到文件开头，优化流式播放和 Windows 预览。
- `-map_metadata 0`：保留输入文件的元数据。

