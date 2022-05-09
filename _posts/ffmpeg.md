---
title: ffmpeg
tags:
  - [工具]
  - [Linux]
categories: [Linux]
date: 2020-04-28 10:57:56
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->
- [ffmpeg](#ffmpeg)
  - [ffmepg-python](#ffmepg-python)
- [高效转码](#高效转码)
- [常用命令](#常用命令)
  - [查看文件信息](#查看文件信息)
  - [裁剪长度](#裁剪长度)
  - [格式转换](#格式转换)
  - [连接合并多个视频](#连接合并多个视频)
    - [cancat协议](#cancat协议)
    - [concat分离器](#concat分离器)
    - [concat编码器](#concat编码器)
  - [画面拼接](#画面拼接)
  - [音频与视频](#音频与视频)
  - [画面与分辨率](#画面与分辨率)
  - [码率与倍速](#码率与倍速)
  - [倒放](#倒放)
- [录屏](#录屏)
- [容器与编码器](#容器与编码器)
  - [容器对应的编码格式](#容器对应的编码格式)
  - [编码格式对应的容器](#编码格式对应的容器)
- [批量修改元数据](#批量修改元数据)
- [视频转gif](#视频转gif)
- [其它视频处理软件](#其它视频处理软件)
  - [MediaCoder](#mediacoder)
  - [Vegas](#vegas)
  - [EDIUS](#edius)
  - [手机版](#手机版)
- [素材](#素材)


# ffmpeg
Annie使用aria2可满速下载，但是有些视频下载后为多个视频片段，需要合并。

[视频处理入门——阮一峰](http://www.ruanyifeng.com/blog/2020/01/ffmpeg.html5)
[FFmpeg命令行语法之-filter_complex](https://www.jianshu.com/p/b30f07055e2e)
[ffmpeg Documentation](http://ffmpeg.org/ffmpeg.html)

## ffmepg-python
[python-ffmpeg](https://github.com/kkroening/ffmpeg-python):与命令行相比，可读性强，更易用。`input-filter-output-run`组织命令。`filter`字段可使用ffmpeg中所有的filter，也可单独使用ffmpeg中的filter。

```
stream = ffmpeg.input('dummy.mp4')
stream = ffmpeg.filter(stream, 'fps', fps=25, round='up')
stream = ffmpeg.output(stream, 'dummy2.mp4')
ffmpeg.run(stream)
```

# 高效转码
目前大多数视频的编码格式是`h264和aac`，音频的编码格式是`mp3或aac`。因此，只要不同的容器格式之间能同时支持一样的编码格式，就能够进行“高效转码”。
利用了编码格式相同而容器格式又不同这一点，转换的只是容器格式。
直接更改文件后缀，部分播放器也能播放。
1. 查看文件的编码格式:
  `ffprobe .\input.mp4`
2. 容器：
  `ffmpeg -formats`
3. 编码格式：
   `ffmpeg -codecsv`

FFmpeg 内置的视频编码器。
```
libx264：最流行的开源 H.264 编码器
NVENC：基于 NVIDIA GPU 的 H.264 编码器
libx265：开源的 HEVC 编码器
libvpx：谷歌的 VP8 和 VP9 编码器
libaom：AV1 编码器

```
使用`-c copy`是复制音视频编码。`-c`”`参数包括了音视频的全部编解码器。
`-c:v`来限定只处理视频画面，`-c:a`来限定只处理视频里的音频声音，`-c:s`来限定只处理字幕


# 常用命令
## 查看文件信息
`ffprobe 1.mp4`
或`ffmpeg -i 1.mp4`

## 裁剪长度
`ffmpeg -ss START -t DURATION -i INPUT -vcodec copy -acodec copy OUTPUT`
-ss 开始时间，如： 00:00:20，表示从20秒开始；
-t 时长，如： 00:00:10，表示截取10秒长的视频；
-vcodec copy 和 -acodec copy表示所要使用的视频和音频的编码格式，这里指定为copy表示原样拷贝；

## 格式转换
`-f`参数来进行转码。强制输出什么格式的文件，让ffmpeg自行挑选编解码器进行转码输出。
速度约为4Mb/s，1.3倍速左右。
**转换容器格式**
转换容器格式（transmuxing）。下面是 mp4 转 webm 的写法。
`ffmpeg -i input.mp4 -c copy output.webm`
**转换编码格式**
转换编码格式（transcoding）。比如转成 H.264 编码，一般使用编码器libx264，所以只需指定输出文件的视频编码器即可。
`ffmpeg -i [input.file] -c:v libx264 output.mp4`

## 连接合并多个视频
### cancat协议
`ffmpeg -i "concat:input1.mpg|input2.mpg|input3.mpg" -c copy output.mpg`
适用场景是：
1. `视频容器是MPEG-1, MPEG-2 PS或DV`。其实可以直接用cat或者copy之类的命令来对视频直接进行合并。
2. 对于非 MPEG 格式容器，但是是 `MPEG 编码器`（H.264、DivX、XviD、MPEG4、MPEG2、AAC、MP2、MP3 等），可以`包装进 TS 格式`的容器再合并。
**包装：**
```
ffmpeg -i input1.flv -c copy -bsf:v h264_mp4toannexb -f mpegts input1.ts
```
**合并**（可输出不同格式）：
```
ffmpeg -i "concat:input1.ts|input2.ts|input3.ts" -c copy -bsf:a aac_adtstoasc -movflags +faststart output.mp4
```

### concat分离器

filelist.txt:
```
file 'input1.mp4'
file 'input2.mp4'
```
文件名中如果有奇怪的字符则需要定义转义。
```
ffmpeg -f concat -i filelist.txt -c copy output.mp4

```
提示`不安全的文件名`：加上`-safe 0`参数。

### concat编码器
待补充。


## 画面拼接
**横向合并视频**
`ffmpeg -i input1.mp4 -i input2.mp4 -lavfi hstack output.mp4`
上面的命令虽然可以合并视频，两个视频可以正常播放，但是只保留了前面一个的音频。
下面会介绍怎么避开这个坑。

input1和input2必须同样的高度，如果不一样的高度可以使用-shortest参数来保证同样的高度。
**纵向合并视频**
`ffmpeg -i input1.mp4 -i input2.mp4 -lavfi vstack output.mp4`

**网格合并视频**
当多个视频时，还可以合并成网格状，比如2x2，3x3这种。但是视频个数不一定需要是偶数，如果是奇数，可以用黑色图片来占位。

`ffmpeg -f lavfi -i color=c=black:s=1280x720 -vframes 1 black.png`
该命令将创建一张1280*720的图片

然后就可以使用下面这个命令来合并成网格视频了，如果只有三个视频，可以选择上面创建的黑色图片替代。
```
ffmpeg -i top_left.mp4 -i top_right.mp4 -i bottom_left.mp4 -i bottom_right.mp4 \
-lavfi "[0:v][1:v]hstack[top];[2:v][3:v]hstack[bottom];[top][bottom]vstack"
-shortest 2by2grid.mp4
```
上面创建的是正规的2x2网格视频。
`hstack表示水平方向；vstack垂直方向。`
如果把第一个视频摆放在第一行的中间，然后把第二、三个视频摆放在第二行。那么就可以使用下面两个命令了。

```
ffmpeg -f lavfi -i color=c=black:s=640x720 -vframes 1 black.png

ffmpeg -i black.png -i top_center.mp4 -i bottom_left.mp4 -i bottom_right.mp4 -lavfi "[0:v][1:v][0:v]hstack=inputs=3[top];[2:v][3:v]hstack[bottom];[top][bottom]vstack" -shortest 3_videos_2x2_grid.mp4

```

**叠加视频或素材**
加了两个水印：
```
ffmpeg -i input.mp4 -i image1.png -i image2.png -filter_complex  [1:v]scale=100:100[img1];[2:v]scale=1280:720[img2];[0:v][img1]overlay=(main_w-overlay_w)/2:(main_h-overlay_h)/2[bkg];[bkg][img2]overlay=0:0 -y output.mp4
```
[1:v]这个里头两个参数，1表示的是操作对象的编号。在本例中0就是原始视频文件input.mp4，1就是image1.png，2就是image2.png，3就是output.mp4。而另一个参数v表示操作对象里的视频信息。
[img1]是这个操作过滤器的名字。（当然名字可以随便起）
所以这头一句[1:v]scale=100:100[img1]的意思就是对图片imagei.png进行调节尺寸的操作，并将这个操作的结果命名为img1。后面的[2:v]和[img2]也是一个意思。
overlay前面[0:v][img1]凑一起是什么意思呢。0自然就是指的原始视频，这句的意思就是将[img1]叠加到0对象的视频上。后一个对象在上层。
又把这个操作的结果命名为[bkg]，那么接下来[bkg][img2]的意思就很明了了。就是把image2.png再叠加上去，image2.png是在最上层的，如果位置重合的话，他会遮盖 image1.png的水印。

## 音频与视频
**合并音频和视频**
`ffmpeg -i video.mp4 -i audio.wav -c:v copy -c:a aac -strict experimental output.mp4`

如果视频中已经包含了音频，这个时候还可以替换视频中的音频，使用下面命令行。
`ffmpeg -i video.mp4 -i audio.wav -c:v copy -c:a aac -strict experimental -map 0:v:0 -map 1:a:0 output.mp4`

`-strict experimental`是ffmpeg原生aac编码器。
**合并两个音频**
`ffmpeg -i input1.mp3 -i input2.mp3 -filter_complex amerge -ac 2 -c:a libmp3lame -q:a 4 output.mp3`

**获取视频中的音频**
`ffmpeg -i input.mp4 -vn -y -acodec copy output.m4a`

**去掉视频中的音频**
`ffmpeg -i input.mp4 -an -vcodec copy output.mp4`

**合并两个视频并保留两个视频中的音频**
1. 合并两个视频，但是发现只有一个声音。无所谓。
2. 抽取两个视频中的音频，然后合并成一个音频。
3. 将这个音频替换到之前的合并视频中。

## 画面与分辨率
**裁剪画面**
```
ffmpeg -i 1.mp4 -strict -2 -vf crop=960:1080:960:0 out2.mp4

ffmpeg  -i  intput.avi  -vf  crop=iw/2:ih/2  output.avi 
```
`crop=width:height:x:y`。其中width 和height 表示裁剪后的尺寸，x:y 表示裁剪区域的左上角坐标,如不指定x:y，则为居中。

**调整分辨率**

`ffmpeg -i a.mov -vf scale=853:480 -acodec aac -vcodec h264 out.mp4`
参数`scale=853:480i` 当中的宽度和高度实通常只需指定一个，可以传入 “-1” 表示由原始视频的宽高比自动计算而得。即参数可以写为：scale=-1:480，或 ：scale=480:-1 。

## 码率与倍速
**指定码率帧数文件大小**
帧率
`-r 24`
码率
分为视频的码率 `-b:v`，音频的码率`-b:a`,单位kbps,
设置视频码率1500，音频码率48
`ffmpeg -y -i demo-b.mp4 -b:v 1500K -b:a 48K demo-bv1500-ba-48.mp4`

文件大小：
实际上是截取指定大小的视频。
`ffmpeg -y -i demo.mp4 -fs 500K demo-fs-500K.mp4 `

**倍速播放**
2倍播放：
`ffmpeg -i input.mkv -filter_complex "[0:v]setpts=0.5*PTS[v];[0:a]atempo=2.0[a]" -map "[v]" -map "[a]" output.mkv`

`ffmpeg -i input.mkv -filter:v "setpts=0.5*PTS" -filter:a "atempo=2.0,atempo=2.0" output.mkv`
如果担心会出现丢帧的情况，可以使用 -r 指定输入帧数，如果源视频是30fps，我们想4倍播放：
`ffmpeg -i input.mkv -r 120 -filter:v "setpts=0.25*PTS" output.mkv`
## 倒放
```
ffmpeg -i 2.mp4 -vf reverse -y reverser.mp4
```

# 录屏
- 基本命令：
```
ffmpeg -f gdigrab -i desktop out.mpg
```
1080p屏幕，此时视频码率为2.5Mb/s

- 以15的帧率抓屏20秒，抓屏范围，以点（100,60）开始，大小600x480，保存为视频格式是264的mp4文件
```
ffmpeg -f gdigrab -video_size 600x480 -offset_x 100 -offset_y 60 -t 20 -r 15 -i desktop -vcodec libx264 x264.mp4

```

# 容器与编码器 
## 容器对应的编码格式
封装格式(container format)可以看作是编码流(音频流、视频流等)数据的一层外壳，将编码后的数据存储于此封装格式的文件之内。

不同封装格式适用于不同的场合，支持的编码格式不一样，几个常用的封装格式如下：

|名称(文件扩展名)|推出机构|流媒体|支持的视频编码|支持的音频编码|目前使用领域|
|---|---|---|---|---|---|
|AVI(.avi)|Microsoft 公司|不支持|几乎所有格式|几乎所有格式|BT 下载影视|
|Flash Video(.flv)|Adobe 公司|支持|Sorenson/VP6/H.264|MP3/ADPCM/Linear PCM/AAC 等|互联网视频网站|
|MP4(.mp4)|MPEG 组织|支持|MPEG-2/MPEG-4/H.264/H.263 等|AAC/MPEG-1 Layers I,II,III/AC-3 等|互联网视频网站|
|MPEGTS(.ts)|MPEG 组织|支持|MPEG-1/MPEG-2/MPEG-4/H.264|MPEG-1 Layers I,II,III/AAC|IPTV，数字电视|
|Matroska(.mkv)|CoreCodec 公司|支持|几乎所有格式|几乎所有格式|互联网视频网站|
|Real Video(.rmvb)|Real Networks 公司|支持|RealVideo 8,9,10|AAC/Cook Codec/RealAudio Lossless|BT 下载影视|

## 编码格式对应的容器

mkv是个万能的容器格式。下面说的所有编码格式，mkv几乎都能“装”，就不再列出了。

**视频**：

1. h264（又称mpeg-4 avc、mpeg-4 part 10）：mp4、flv、avi、mov、wmv、m4v、f4v、3gp、ts

2. mpeg4（不只一种，这里指mpeg-4 part 2、divx、xvid）：mp4、avi、mov、wmv、m4v、3gp、ts

3. h265（又称hevc、mpeg-h part 2）：mp4、avi、mov、ts

4. vp8：avi、wmv、ts、webm

5. vp9：mp4、avi、wmv、ts、webm

**音频**：（【】左边是视频容器格式，【】右边是音频容器格式）

1. aac：mp4、flv、avi、mov、wmv、3gp、m4v、f4v、ts【】aac、m4a、wma、ac3

2. mp3：mp4、avi、mov、wmv、f4v、ts【】mp3、wma、ac3

3. ac-3：mp4、avi*、mov、wmv、m4v、ts【】ac3、m4a、wma

4. flac：mp4*、avi*、wmv、ts【】flac、m4a、wma、ac3^

5. vorbis：mp4、avi*、mov、wmv、ts、webm【】ogg、wma、ac3^

6. opus：mp4*、ts、webm【】ogg、ac3^

*的意思是需要进一步使用相应参数；
^的意思是能转码成功，但很可能播放器不能播放
一般听感来说，上述中opus编码格式是有损编码里最好的，其次是vorbis，之后是aac、mp3之类


# 批量修改元数据
网上下载的课程（mp3）被打上了乱七八糟的标签，需要去除。

1. 修改py文件中的路径和输出文件名；
2. python读取文件夹下符合条件的mp3，然后生成对应的bat命令并保存；
3. 运行bat命令即可。

`ffmpeg -i {} -c copy -metadata album="" -metadata artist="" -metadata comment="" -metadata composer="" -metadata album_artist="" {}_.m4a`
```python
import os
from os import path

wdr = path.normpath(r'D:\mydir\MP3')
videoList = os.listdir(wdr)
#获取文件夹下所有文件列表

ffmpegCmd = 'ffmpeg -i {} -c copy -metadata album="" -metadata artist="" -metadata comment="" -metadata composer="" -metadata album_artist="" {}_.m4a '
#设置ffmpeg命令模板
cmd = f'cd "{wdr}"\n{path.splitdrive(wdr)[0]}\npause\n'
#第1步，进入目标文件夹

def comprehensionCmd(e):
#手写一个小函数方便后面更新命令
    root,ext = path.splitext(e)
    return ffmpegCmd.format(e,root)

videoList = [comprehensionCmd(e) for e in videoList if not('_' in e)]
#第3和第4步

cmd += '\n'.join(videoList)
# 将各个ffmpeg命令一行放一个

cmd += '\npause'

output = open('videoConv.bat','w')
output.write(cmd)
output.close()
#命令写入文件
```

# 视频转gif
[格式说明](https://superuser.com/questions/556029/how-do-i-convert-a-video-to-gif-using-ffmpeg-with-reasonable-quality)：
```
ffmpeg -ss $INPUT_START_TIME -t $LENGTH -i $INPUT_FILENAME \
-vf "fps=$OUTPUT_FPS,scale=$OUTPUT_WIDTH:-1:flags=lanczos,split[s0][s1];[s0]palettegen[p];[s1][p]paletteuse" \
-loop $NUMBER_OF_LOOPS $OUTPUT_FILENAME

# Change these placeholders:
# * $INPUT_START_TIME - number of seconds in the input video to start from.
# * $LENGTH - number of seconds to convert from the input video.
# * $INPUT_FILENAME - path to the input video.
# * $OUTPUT_FPS - ouput frames per second. Start with `10`.
# * $OUTPUT_WIDTH - output width in pixels. Aspect ratio is maintained.The lanczos scaling algorithm is used in this example.
# palettegen and paletteuse filters will generate and use a custom palette generated from your input. These filters have many options.
# split filter will allow everything to be done in one command and avoids having to create a temporary PNG file of the palette.
# * $NUMBER_OF_LOOPS - use `0` to loop forever, or a specific number of loops.
# * $OUTPUT_FILENAME - the name of the output animated GIF.


```

示例：
```
ffmpeg -ss 32.5 -t 7 -i "sample_recording.mp4" \
-vf "fps=10,scale=720:-1:flags=lanczos,split[s0][s1];[s0]palettegen[p];[s1][p]paletteuse" \
-loop 0 sample_recording.gif
```


# 其它视频处理软件 
## MediaCoder
功能非常强大的带GUI的视频处理软件。

## Vegas
相比PR，Vegas就单独一个软件来说，功能比较全面

由于界面设计原因，入门也比较简单。

## EDIUS
相比PR来说，PR功能更强大，Edius使用方便。
PR优点就是功能多，插件多，模版多，和Adobe家软件衔接的很好。
Edius的优点是界面友好，上手方便，对电脑要求低。

## 手机版
Quik
HANI
InShot
Videoshop

# 素材
[大拍档剪辑助手](http://spdpd.net/)
无版权音乐
https://www.zhihu.com/question/266065731