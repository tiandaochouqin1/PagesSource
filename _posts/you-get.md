---
title: 网络视频下载 - annie/you-get/pytube
date: 2019-06-02 17:00:46
tags: 
- 工具
- 网络
categories: 其它
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->

<!-- TOC -->

- [需求描述](#需求描述)
- [Annie](#annie)
- [you-get](#you-get)
  - [you-get介绍](#you-get介绍)
  - [you-get使用方法](#you-get使用方法)
- [其它工具](#其它工具)
  - [pytube](#pytube)
  - [youtube-dl](#youtube-dl)
  - [livestreamer/streanlink](#livestreamerstreanlink)

<!-- /TOC -->
***

# 需求描述

批量下载在线视频。

# Annie
[Annie](https://github.com/iawia002/annie)支持国内主流视频网站和U2B。
使用方法与`you-get`类似，无图形界面。

安装：

```
scoop install annie #Windows下使用包管理工具scoop/choco安装
#发行版有 `annie.exe` 可直接下载使用。

```

常用命令：

```
annie url1 url2 url3  //多个链接
annie -i url   //只查看信息
annie -p url   //下载播放列表
annie -F a.txt #从文件读取多个链接

```

列表参数：

```
-start
    Playlist video to start at (default 1)
-end
    Playlist video to end at
-items
    Playlist video items to download. Separated by commas like: 1,5,6,8-10

```

下载参数：

```
-f string
        Select specific stream to download
-p	Download playlist
-n int
        The number of download thread (only works for multiple-parts video) (default 10)
-c string
        Cookie
-r string
        Use specified Referrer
-cs int
        HTTP chunk size for downloading (in MB) (default 0)

```

支持Cookies、代理、多线程、B站短链接等。



# you-get
## you-get介绍

[you-get官网](https://you-get.org)

**用途：**

- 下载视频、音频。支持`国内与国外`大部分网站。such as YouTube, Youku, Niconico, and a bunch more.
- 本地播放器上播放流视频. No web browser, no more ads.
- 下载图片 (of interest) by scraping a web page.
- 下载任意non-HTML contents, i.e., binary files

**依赖项：**

- Python 3.2 or above
- FFmpeg 1.0 or above [安装方法](http://blog.gregzaal.com/how-to-install-ffmpeg-on-windows/) 下载高品质视频以及合并视频
- (Optional) RTMPDump

安装

    pip3 install you-get

## you-get使用方法

bash/cmd
**示例：**

    you-get --tags=000 -x 127.0.0.1:1080 https://www.youtube.com/watch?v=spUNpyF58BY

    you-get -l --format=dash-flv720  https://www.bilibili.com/video/av31816280/?p=1

**查看视频信息**

    you-get -i url  #or --playlist
 得到多种视频源信息，如下
    - itag:          18
      container:     mp4
      quality:       medium
根据条件筛选：设置 --itag=18

**下载单个视频**

    you-get url  #自动下载可用的字幕文件

**下载播放列表**

    you-get -l url  #下载url以及其所有续集

**指定格式**

    --format=dash-flv720    #从视频信息中得到可用格式

**指定列表下载范围**

    for i in $(seq 1 3); do you-get https://www.bilibili.com/video/av33087749/?p=$i; done

**设置路径与文件名**

    you-get -o ~/Videos -O zoo.webm 'https://www.youtube.com/watch?v=spUNpyF58BY'

**使用代理**

    you-get -x 127.0.0.1:8087 'https://www.youtube.com/watch?v=spUNpyF58BY'

**暂停与恢复**

Ctrl+C 停止；再次以相同参数运行可以继续之前未完成的任务，已完成的部分则会跳过；使用 --force/-f 强制重新下载。

**下载其他资源**

    you-get https://stallman.org/rms.jpg

**搜索并下载视频**

    you-get "Richard Stallman eats" #默认下载Google搜索第一条

**观看视频**
使用-p指定打开链接的程序。
Use the --player/-p option to feed the video into your media player of choice, e.g. mpv or vlc, instead of downloading it:

    you-get -p vlc 'https://www.youtube.com/watch?v=jNQXAC9IVRw'

Or, if you prefer to watch the video in a browser, just without ads or comment section:

    you-get -p chromium 'https://www.youtube.com/watch?v=jNQXAC9IVRw' 

It is possible to use the -p option to start another download manager, e.g., you-get -p uget-gtk 'https://www.youtube.com/watch?v=jNQXAC9IVRw', though they may not play together very well.

**使用本地Cookies**

不是所有的视频都是公开的，有些视频可能需要登录，这时就需要使用浏览器Cookies。支持Mozilla cookies.sqlite and Netscape cookies.txt.
    you-get via the --cookies/-c option.

***


# 其它工具
## pytube

[pytube地址](https://github.com/nficano/pytube)

**基本语法与函数：**
下载单个视频：

```
from pytube import YouTube
>>> YouTube('https://youtu.be/9bZkp7q19f0').streams.first().download()
>>> yt = YouTube('http://youtube.com/watch?v=9bZkp7q19f0')
>>> yt.streams
... .filter(progressive=True, file_extension='mp4')
... .order_by('resolution')
... .desc()
... .first()
... .download()

```

下载播放列表：

```
>>> from pytube import Playlist
>>> pl = Playlist("https://www.youtube.com/watch?v=Edpy1")
>>> pl.download_all()
>>> # or if you want to download in a specific directory
>>> pl.download_all('/path/to/directory/')

```
多过滤条件：

    >>> yt.streams.filter(subtype='mp4', progressive=True).all()
    >>> # this can also be expressed as:
    >>> yt.streams.filter(subtype='mp4').filter(progressive=True).all()

命令行中/bash：

    $ pytube http://youtube.com/watch?v=9bZkp7q19f0 --itag=22
    To view available streams:
    $ pytube http://youtube.com/watch?v=9bZkp7q19f0 --list

**使用**

    from pytube import YouTube
    pprint-pretty print 不必要，仅仅为了让输出更好看，每个视频文件占一行
    from pprint import pprint
    yt = YouTube("http://www.youtube.com/watch?v=Ik-RsDGPI5Y")
    显示视频文件名
    print(yt.filename)
    设置视频文件名
    yt.set_filename('myFirstVideo')
    根据文件类型过滤视频文件
    pprint(yt.filter('flv')) 
    由于排序是按清晰度从低到高，所以可以用 -1 索引到最高清版本
    print(yt.filter('.mp4')[-1])
    根据清晰度过滤文件
    pprint(yt.filter(resolution='480p'))
    通过文件类型和清晰度指定下载的视频
    video = yt.get('mp4','720p')
    如果有多个相同类型，或者相同清晰度的文件，则不能仅指定一种格式来下载视频，例如下面一行可能会报错：
    video = yt.get('mp4')
    其实，上面的 video 完全可以用过滤+索引的方式获得，不一定非得用 get 方法
    video = yt.filter('.mp4')[-1]
    下载到指定路径
    video.download('/home/Desktop')

## youtube-dl

[项目地址](https://ytdl-org.github.io/youtube-dl/index.html)
两个GUI版本：
[Youtube-DLG](https://mrs0m30n3.github.io/youtube-dl-gui/)

[VDM](https://github.com/ingbyr/VDM/releases/)
VDM需要安装JRE8，为youtube-dl源。

类似于you-get。国内网站支持较少。
支持多线程。

## livestreamer/streanlink

[项目地址](https://github.com/streamlink/streamlink)

主要支持国外的视频网站。
