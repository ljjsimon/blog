---
title: MediaSource Extensions (MSE) 踩坑
date: 2025-07-14 10:36:59
tags: javascript
id: 1752479943
---
MediaSource Extensions (MSE) 必须使用 fragmented MP4 (fMP4) 格式。转换命令为：
```
ffmpeg -i input.mp4 -c:v copy -c:a copy -movflags +faststart -f mp4 output.mp4
```
这条命令的作用如下：

-i input.mp4：指定输入文件为input.mp4。
-c:v copy 和 -c:a copy：直接复制视频和音频流而不重新编码，这样可以节省时间和保持原始质量。
-movflags +faststart：将MOOV原子移动到文件开头，使得视频可以在整个文件下载完成之前就开始播放。
-f mp4：指定输出格式为MP4。

如果你需要进一步优化视频以适应不同的网络条件，可以考虑使用H.264编码器（libx264）来调整视频质量和比特率。例如：
```
ffmpeg -i input.mp4 -c:v libx264 -preset fast -b:v 1M -c:a aac -b:a 128k -movflags +faststart -f mp4 output.mp4
```