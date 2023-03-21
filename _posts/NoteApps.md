---
title: 笔记软件比较
date: 2019-06-08 17:29:38
tags: 工具
categories: 其它
---
<font face="微软雅黑"> </font>
<center> 主要比较了：有道云笔记、印象笔记、为知笔记、Onenote</center>

<!-- more -->

<!-- TOC -->

- [各个笔记软件对比及定位](#各个笔记软件对比及定位)
  - [自身需求](#自身需求)
  - [选择](#选择)
- [各个工具的入口以及出口比较](#各个工具的入口以及出口比较)
- [ArchiveBox](#archivebox)
  - [安装](#安装)
  - [配置](#配置)
  - [archive数据网盘备份](#archive数据网盘备份)

<!-- /TOC -->

1. [参考](http://www.360doc.com/content/18/0214/21/37716914_730013574.shtml)
2. https://www.ruanyifeng.com/blog/2021/08/best-note-taking-software-for-programmers.html

# 各个笔记软件对比及定位
目前主流的笔记软件主要有有道云笔记、印象笔记、为知笔记、Onenote四款

[开源joplin](https://joplinapp.org/)，与evernote很像，数据存储在自己的云服务中，全平台

| 分类 | 为知笔记 | OneNote | 有道云 | 印象笔记 |
|---|---|---|---|---|
|同步功能|强（vip）|弱|强|强|
|多目录分层|强|弱|强|不支持|
|标签|强|不支持（标记功能）|弱|强|
|多平台|强|弱|强|强|
|分享功能|支持（vip）|支持（outlook）|支持|支持outlook|
|云协作|支持（vip）支持|支持|支持 未体验|
|回收站|支持|支持|支持|支持|
|Office套件|弱|强|强|弱|
|批量导出|差|一般|一般|强|
|**定位**|资料制作管理，KPM工具|资料管理，再加工，数字笔记本|资料收集制作管理，KPM|便笺型知识管理软件|

上面的对比是根据我个人的使用体验来做出划分，其中评价一栏是有点主观的，每个工具都有自己的优点以及缺点，只是有些工具擅长做一些事情罢了，就比如为知笔记是典型的KPM工具， 而印象笔记也是可以做KPM工具的，只不过体验要差点，因人而异！另外可以看看有道云笔记、印象笔记、为知笔记、Onenote竞品分析报告加深理解
另外可以看下网友对印象笔记，有道云，为知笔记的移动端截图
## 自身需求
选择一款使用它，不要错把手段单做目标了

- 多终端同步
- 方便的资料收集，包括网页微信，微博以及其他
- 多目录分层以及标签功能
- 开发的入口以及出口，导入以及批量导出
- 安全性（长久）

## 选择

**- 为知**
强在管理、插件、同步与编辑，弱在方便与手写，定位：海量型资料管理软件

为知笔记对我而言最大的问题在于笔记的批量导出，以及公司前景。wiz中单个笔记不能导出为word，只支持单个导出为pdf，这是我不能忍受的。

可自己部署docker服务端,保存微信推文仍然需要另购许可。

1. `为知笔记 | 为知笔记服务端docker镜像使用说明  <https://www.wiz.cn/zh-cn/docker>`__
2. `为知笔记 | Docker 服务端私有部署 - 收藏服务设置说明  <https://www.wiz.cn/zh-cn/docker-collector-service>`__
   原理：private server的可访问地址 + private 的收藏token填入到绑定微信公众号的官方账号中

server 许可可破解 http://www.zhanghaobk.com/archives/docker-da-jian-wei-zhi-bi-ji--bing-po-jie-vip 

可发布到博客(cnblog、csdn、wordpress) https://www.wiz.cn/wiz-plugin-blog-writter.html

**- OneNote**
强在批注、整理、手写与编辑，弱在编辑阅读开关与管理，定位：草稿型OCR整理软件

笔记编辑是最强大的，没有之一。同步速度有点感人，不过它对笔记的管理理念基于书本以及论文，按照笔记本，章节，页面来组织，而非其他笔记软件基于一个资料库。移动端支持还是有点差。

**- 有道云**
类似为知以及印象笔记的综合体

多目录以及标签支持，而且支持批量导出为pdf，勉强可以吧。

**- 印象笔记**
强在人性化、提醒、扫描与营销，弱在编辑与海量，定位：便笺型知识管理软件

网页剪辑方便，公众号直接保存推文。

！！！广告垃圾

**- Joplin**
类似evernote。使用私有存储用——webdav、onedrive、云存储等。可自定义编辑器，网页剪藏功能稍差。

可导入md、enex等

# 各个工具的入口以及出口比较

| 功能 | 印象笔记 | OneNote | 为知 | 有道云 |
|---|---|---|---|---|
|批量导出|enex格式|note格式|私有格式|私有格式，pdf|
|导入|enex格式，note格式|note格式|enex格式，私有格式|enex格式，私有格式|
|批量导入文件夹|支持|不支持|支持|支持|

**印象笔记的入口以及出口是最开放的**，它的格式可以被其他笔记导入，而其他笔记的私有格式迁移成本太大。

# ArchiveBox
https://github.com/ArchiveBox/ArchiveBox#output-formats

适合收藏网页。保存为pdf、html等格式。
1M腾讯云保存经常失败。设置超时时间为120后解决。



## 安装
Open source self-hosted web archiving. Takes URLs/browser history/bookmarks/Pocket/Pinboard/etc., saves HTML, JS, PDFs, media, and more...

1. Install Docker on your system (if not already installed).
2. Create a new empty directory and initalize your collection (can be anywhere).
mkdir ~/archivebox && cd ~/archivebox
3. docker run -v $PWD:/data -it archivebox/archivebox init --setup

4. docker run -v $PWD:/data -p 8000:8000 archivebox/archivebox

## 配置
配置文件： 指定目录下的 ArchiveBox.conf

指定默认导出格式（关闭后网页仍会显示所有格式）：

```
cat ArchiveBox.conf
[SERVER_CONFIG]
SECRET_KEY = 3BsnjdP0y27TleNNy6JYc73nEKEaxOhwoOQc9f4dc_QCNcYPyH

文件中加入开关：

SAVE_WGET = False
SAVE_WARC = False
SAVE_SCREENSHOT = False
SAVE_DOM = False
SAVE_GIT = False
SAVE_MEDIA = False
SAVE_PDF = False
SAVE_ARCHIVE_DOT_ORG = False
SAVE_READABILITY = False


```

开关选项说明： https://github.com/ArchiveBox/ArchiveBox/wiki/Configuration#archive-method-toggles

也可直接更改 `archivebox/config.py` (docker 中位于 /app/archivebox/config.py)

## archive数据网盘迁移与备份
打包/data/archivebox(容器目录/data映射而来)即可。

### 备份到onedrive

