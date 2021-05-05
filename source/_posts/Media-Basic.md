---
title: Media-Basic
date: 2021-05-05 15:49:50
tags:
- media
- audio
- video
categories:
- media
---

[TOC]

# 音视频基础知识

## 视频基础

### RGB

数字图像使用RGB表示
- R: 红光
- G: 绿光
- B: 蓝光

每个RGB分量用8bit表示；(256个色度)

#FF0000 = 红色

#FFFFFF = 白色

也有使用10bit表示的

- RGBA_8888: RGBA分量各占8bit（A表示alpha不透明度参数）
- RGB_565: R分量5bit，G分量6bit，B分量5bit
- RGB_888：RGB分量各8bit

720的RGB_888图像的大小：
```
1280 * 720 * 3 = 2.637MB
```

如果是90分钟电影，每秒25帧，则大小：
```
2.637MB * 90 * 15 = 347.651MB
```

因此需要压缩；
- 图像压缩：jpeg, png...
- 视频压缩: h264, h265...

### YUV

YUV主要应用于优化彩色视频信号的传输，使其向后兼容老式黑白电视。

“Y”表示明亮度（Luminance或Luma），也称灰阶值；而“U”和“V”表示的则是色度（Chrominance或Chroma），它们的作用是描述影像的色彩及饱和度，用于指定像素的颜色。


YUV格式有两大类: planar和packed

- planar: 先连续存储所有像素点的Y，接着存储所有像素点的U，随后是所有像素点的V；
  ```
  YYYYYYYYYYYY
  YYYYYYYYYYYY
  UUUUUUUUUUUU
  UUUUUUUUUUUU
  VVVVVVVVVVVV
  VVVVVVVVVVVV
  ```
  ```
  YUVYUVYUVYUV
  YUVYUVYUVYUV
  YUVYUVYUVYUV
  YUVYUVYUVYUV
  ```
- packed：每个像素点的Y、U、V是连续交替存储的；
  ```
  YUVYUVYUVYUV
  YUVYUVYUVYUV
  YUVYUVYUVYUV
  YUVYUVYUVYUV
  ```
YUV存在多种格式，不同的YUV格式的数据在存储时的排列顺序不一样。开发时不注意会导致画面不正常，如：花屏、绿屏等
- YUV 4:4:4  每一个Y分量对应一组UV分量
- YUV 4:2:2  每两个Y分量共用一组UV分量
- YUV 4:2:0  每四个Y分量共用一组UV分量

YUV420p
```
YYYYYYYY
YYYYYYYY
YYYYYYYY
YYYYYYYY
UUUUUUUU
VVVVVVVV
```

YUV420sp
```
YYYYYYYY
YYYYYYYY
YYYYYYYY
YYYYYYYY
UVUVUVUV
UVUVUVUV
```

相较于RGB，我们可以计算一帧为1280×720的视频帧，用YUV420P的格式来表示，其数据量的大小如下
```
1280*720*1+1280*720*0.5 = 1.318MB
```

如果fps（1秒的视频帧数目）是25，按照一般电影的长度90分钟来计算，那么这部电影用YUV420P的数据格式来表示的话，其数据量的大小就是：

```
1.318MB*25fps*90min*60s = 173.76GB
``` 

libyuv: google开源的实现各种YUV和RGB之间相互转换、旋转、缩放、裁剪的库。

### 主要概念

- 视频码率：kb/s, 视频文件在单位时间内使用的数据流量，也叫码流率。码率越大，说明单位时间内采样率越大，数据流精度就越高；
- 视频帧率：fps，单位时间内的视频帧数，准率越高，给人的视觉越流畅。


### 帧

#### I帧
Infra coded frames

I帧通常是每个GOP（MPEG所使用的一种视频压缩技术）的第一个帧，经过适度地压缩，作为随机访问的参考点，可以当成静态图像。I帧可以看作一个图像经过压缩后的产物，I帧压缩可以得到6：1的压缩比而不会产生任何可觉察的模糊现象。

I帧自身可以通过视频解压算法解压成一张单独的完整视频画面，所以I帧去掉的是视频帧在空间维度上的冗余信息。

#### P帧
前向预测编码帧（predictiveframe），通过将图像序列中前面已编码帧的时间冗余信息充分去除来压缩传输数据量的编码图像，也称为预测帧。

P帧需要参考其前面的一个I帧或者P帧来解码成一张完整的视频画面。

#### B帧
双向预测内插编码帧（bidirectionalinterpolatedpredictionframe），既考虑源图像序列前面的已编码帧，又顾及源图像序列后面的已编码帧之间的时间冗余信息，来压缩传输数据量的编码图像，也称为双向预测帧。

B帧则需要参考其前一个I帧或者P帧及其后面的一个P帧来生成一张完整的视频画面，所以P帧与B帧去掉的是视频帧在时间维度上的冗余信息。


#### GOP

两个I帧之间形成的一组图片，就是GOP（Group Of Picture）的概念。

```
|I|P|B|B|P|B|B|I|B|B|B|......
|<--  GOP  -->|
```

#### 常用视频压缩算法

- MPEG2 (MPEG)
- H264  (MPEG)
- H265  (MPEG)
- AVS   （中国）
- VP8   (Google)
- VP9   (Google)

#### H264

Elecard streameye tools
TODO

#### HEVC
TODO

## 音频基础

### 物理特性

声音是由物体振动而产生的

- 频率：频率（过零率）越高，波长就越短。低频声响的波长则较长，所以其可以更容易地绕过障碍物，因此能量衰减就小，声音就会传得远，反之则会得到完全相反的结论。

- 响度：响度其实就是能量大小的反映，用不同的力度敲击桌子，声音的大小势必也会不同。在生活中，分贝常用于描述响度的大小。声音超过一定的分贝，人类的耳朵就会受不了。

- 音色：音色其实也不难理解，在同样的音调（频率）和响度（振幅）下，钢琴和小提琴的声音听起来是完全不相同的，因为它们的音色不同。波的形状决定了其所代表声音的音色，钢琴和小提琴的音色不同就是因为它们的介质所产生的波形不同。

### 音频主要概念
- 比特率： 每秒传输的bit数，单位为bps，是简介衡量声音质量的一个标准。没有压缩的音频数据采样率=采样频率*采样精度*通道数
- 码率：压缩后的音频数据比特率。常见的码率：
  - 96kbps：FM质量
  - 28-160kbps：一般质量音频
  - 192kbps： CD质量
  - 256-320kbps：高质量音频
  码率越大，压缩效率越低，音质越好，压缩后数据越大；
  码率=音频文件大小/时长

### 音频帧
音频的帧的概念没有视频帧那么清晰。

音频压缩单位：一帧多少个采样点
- aac: 通常1024，有时2048（16khz）
- mp3: 通常1152

帧长：
- 可以指每帧采样数播放的时间，如：mp3 48k, 1152个采样点，每帧则为24ms；aac则是每帧1024个采样点。攒够一帧的数据才送去编码。
- 也可以指压缩后每帧的数据长度。
所以讲到帧的时候要注意其适用的场合。


每帧的持续时间=每帧的采样点数/采样频率(HZ)


交错模式：数据音频信号的存储方式。数据以连续帧的方式存放，即首先记录帧1的左声道样本和右声道样本，再开始帧2的记录...
```
1024个采样点一帧为例
|0 |1 |2 |3 |...|1023
|LR|LR|LR|LR|...|LR|......
```
非交错模式:首先记录的是一个周期内所有帧的左声道样本，再记录所有右声道样本。
```
1024个采样点一帧为例
0......1023
LLLLLL...L
RRRRR....R
LLLLLL...L
RRRRR....R
```

### PCM
TODO

### AAC
TODO

### OGG
TODO
 
## 封装格式基础
封装格式(也叫容器)就是将已经编码压缩好的视频流、音频流、字幕按照一定方案放到一个文件中，便于播放软件播放。一般来说，视频文件的后缀名，就是它的封装格式。

封装格式不一样，后缀名也就不一样。

### FLV
流式

```
------
Header
------
Audio
------
Video
------
Audio
------
Video
------
......
```
适合直播

TODO

### MP4
索引

```
-----------
Header
-----------
audio index
-----------
video index
-----------
audio index
-----------
video index
-----------
......
-----------
audio
-----------
video
-----------
audio
-----------
video
-----------
```
适合本地播放

## 传输协议基础

### RTMP
TODO

### HLS
TODO
#### m3u8
TODO

### RTSP
TODO
#### RTCP
TODO
#### RTP
TODO


### 音视频同步基础

#### DTS

#### PTS

#### 音视频同步方式

- Audio Master: 同步视频到音频
- Video Master: 同步音频到视频
- External Clock Master: 同步音频和视频到外部时钟

一般情况下：
Audio Master > Video Master > External Clock Master


### 直播简要流程
```mermaid
graph LR
A[摄像头采集]-->B[YUV420P]
B --> C[H264压缩]
C --> D[RTMP传输]
D --> E[H264解码]
E --> F[YUV420p]
F --> G[RGB显示]
```


### WebRTC

TODO


### ffmpeg
http://ffmpeg.org

TODO

### librtmp
TODO

### srs
https://github.com/ossrs/srs

TODO

### nginx-rtmp-module
https://github.com/arut/nginx-rtmp-module

TODO

### janus
TODO

### live555

TODO

### ijkplayer
https://github.com/Bilibili/ijkplayer







