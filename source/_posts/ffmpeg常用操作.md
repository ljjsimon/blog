---
title: ffmpeg常用操作
date: 2020-06-16 18:13:28
tags: ffmpeg
id: 1592302487
---
```sh
# 视频转音频
ffmpeg -i video.mp4 -f mp3 sound.mp3

# 转MP3为wav

ffmpeg -i input.mp3 -acodec pcm_s16le -ac 2 -ar 44100 output.wav

# 转m4a为wav

ffmpeg -i input.m4a -acodec pcm_s16le -ac 2 -ar 44100 output.wav

# wav与PCM的相互转换

ffmpeg -i input.wav -f s16le -ar 44100 -acodec pcm_s16le output.raw

# PCM转wav

ffmpeg -f s16le -ar 44100 -ac 2 -acodec pcm_s16le -i input.raw output.wav

# 用ffplay播放PCM

ffplay -f s16le -ar 44100 -ac 2 **.raw
```

s16le表示：s表示有符号，l表示小端。 可以用 s16be代替，表示s有符号b表示大端

44100代表采样率，注意保持一致，可以是16000／8000

# 视频转图片
```sh
ffmpeg -i 1.mp4 -r 5 -f image2 .\output\1_frame_%05d.bmp
```
"-r 5"代表一秒中抽取五帧

“ image2”代表图片的类型，

“%05d”代表五位的数，如“00001”

输出图片的后缀不一定要为bmp，也可以为png，这都是无损提取。而输出的图片为jpg时，输出的图片就是压缩过后的。

```sh
ffmpeg -i GGGabc.mp4 -t 5 -s 1080x1080 -r 15 %3d.jpg
```
其中-i GGGabc.mp4表示调用bin目录下的GGGabc.mp4视频

-t 5表示选取视频中的前五秒进行视频转图像

-s 1080x1080表示视频转图片后图片的像素

-r 15表示每秒截取15张图片，也就是15帧

%3d.jpg表示生成图片后的图片的命名格式会以001.jpg，002.jpg....命名
# 图片转视频
```sh
ffmpeg -f image2 -framerate 25 -i "img%05d.bmp" -b:v 25313k C:\123\222.mp4
```
“ -framerate 25”：代表一秒25帧，

“-b:v 25313k ”：代表视频所需的码率为25313k

```sh
ffmpeg -r 15 -i %3d.jpg video.avi
```
-r 15表示15张图片一秒，就是一秒十五帧

-i %3d.jpg表示要选取图片的命名格式，就是调用001.jpg，002.jpg......等图片组成视频

video.avi就是最后视频的名称

# 下载m3u8
```
ffmpeg -i "xxx.m3u8" xx.mp4
```