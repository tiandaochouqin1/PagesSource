---
title: 文件备份与云端存储
date: 2020-03-20 16:47:26
tags: 
- Windows
- 网络
- 网盘
categories: 其它
---
<font face="微软雅黑"> </font>
<center>本地备份程序，Onedrive和开源个人网盘，Rclone挂载</center>

<!-- more -->

---

推荐方案：`GoodSync（本地）+OneDrive(国外)+天翼云（国内）+NAS`

---
# 备份方法
## 网盘备份
Onedrive：教育版1T邮箱；可自由选择同步目录。文件同步到多个备份设备上。
Onedrive版本控制功能：可搭配office 365使用。
NextCloud:开源网盘，自建。
[主流⽹盘与NAS对⽐](https://www.coolapk.com/feed/19321375?shareKey=MGQ0ODNkMTk2OTFmNWVkYTFiNWQ~)

### mklink
将其他目录同步到OneDrive。
`mklink [[/d] | [/h] | [/j]] <Link> <Target>`

## 本地备份
### Windows自带的备份功能

可备份到Onedrive或其它卷。

### Raid盘
多硬盘组Raid，提高安全性。

### 任务计划
### 命令
编写命令行后加入任务计划执行。
使用[`ROBOCOPY`](https://zh.wikihow.com/%E5%9C%A8%E5%91%BD%E4%BB%A4%E6%8F%90%E7%A4%BA%E7%AC%A6%E4%B8%AD%E5%A4%8D%E5%88%B6%E6%96%87%E4%BB%B6#ROBOCOPY.E5.91.BD.E4.BB.A4_sub)。copy、xcopy等。

    robocopy "C:\Users\My Documents" "D:\backup\My Documents" /mir 
    镜像目录，会删除目标目录下的不一致文件。

### 工具软件

Window manager 的超级复制工具。

## GoodSync
非常强大！[GoodSync](https://masuit.com/1785)
1. 备份和双向同步功能。
2. 本地、FTP/SFTP、主流网盘均支持。
3. 支持同步排除规则。
4. 文件（夹）对比
5. 详细的日志记录
6. 强大的同步与触发规则
7. 局域网/广域网设备间点对点同步

## 跨平台备份程序
[restic](https://github.com/restic/restic)：加密备份，支持多种云盘和Rclone。
[duplicati](https://github.com/duplicati/duplicati)：增量加密备份，支持多种云盘和Rclone。
[Linux版onedrive](https://github.com/abraunegg/onedrive)

[restic示例](https://www.moerats.com/archives/897)：
```
restic init --repo /srv/restic-repo   //初始化数据库
restic -r /srv/restic-repo --verbose backup ~/work  //指定备份
restic snapshots -r /srv/restic-repo  //查看快照的ID
restic -r /srv/restic-repo restore latest --target /tmp/restore-art --path "/home/art" --host luigi //恢复部分文件 luigi
```

# 网盘列表
## Onedrive文件列表

`CloudFlare worker` 可使用GoIndex/[FODI](https://github.com/vcheckzen/FODI) 部署GoogleDrive/OneDrive
[Fast OneDrive Index，OneDrive 秒级列表程序](https://github.com/vcheckzen/FODI)
[教程](https://logi.im/back-end/fodi-on-cloudflare.html)


一、OneIndex
这个部署最是简单，只要支持PHP 5.6+、curl就可以。只可绑定1个网盘。
项目已开源：https://github.com/donwa/oneindex

二、OneManager
部署简单，支持PHP就行。只可绑定1个网盘。
项目已开源：https://github.com/qkqpttgf/OneManager-php

三、OneList
基于GoLang，功能较强。可绑定多个网盘。
项目已开源：https://github.com/MoeClub/OneList/tree/master/Rewrite
部署参考：宝塔面板安装OneList（onedrive目录程序），并设置反代

四、PyOne
PyOne是一款基于Python-Flask的onedrive文件本地化浏览系统，使用MongoDB储存文件列表，使用redis缓存数据，支持绑定多个网盘，支持搜索文件，支持移动文件（仅限单文件）。
项目已开源：https://github.com/abbeyokgo/PyOne

五、CuteOne
CuteOne是一款基于Python3的onedrive文件本地化浏览系统。
多盘负载、在线查看、在线上传、下载、多盘同步、主从同步、在线分享、文件夹权限管理、 会员功能、等级制度、付费查看、密码查看、支付模块、主题切换、极速缓存、模块化插件化管理、
项目已开源: https://github.com/Hackxiaoya/CuteOne

六、ShareList
ShareList 是一个易用的网盘工具，支持快速挂载 GoogleDrive、OneDrive ，可通过插件扩展功能。
项目已开源: https://github.com/reruin/sharelist

七、OLAINDEX
基于Laravel开发，支持多类型帐号登录，多种主题显示，功能强大。
项目已开源：https://github.com/WangNingkai/OLAINDEX

八、FODI
Fast OneDrive Index / FODI，无需服务器的 OneDrive 快速列表程序
项目已开源：https://github.com/vcheckzen/FODI

九、CuteOneP
CuteOneP是CuteOne的PHP版本，沿用一致的UI风格，保持代码精简 框架可扩展。
项目已开源: https://github.com/Hackxiaoya/CuteOneP

Cloudreve

## 网盘目录

1. OneDrive_SCF/[OneManager](https://github.com/qkqpttgf/OneManager-php).
腾讯SCF/herohu部署
2. [Onelndex-Serviceless](https://github.com/LiuChangFreeman/OneIndex-Serverless).
阿里云函数部署
3. [OnePoint](https://github.com/ukuq/onepoint)
nowsh/腾讯SCF部署
4. FODI

[OneDrive](../onedrive) ： 支持FLV在线观看。
[CF workers 后端](https://logi.im/back-end/fodi-on-cloudflare.html)+[github前端](https://github.com/vcheckzen/FODI)（前端可放到githubPages）
[API获取](https://logi.im/fodi/get-code/)：此处用的是别人的Client ID + Client Secret 来生成Refresh Token。


## 网盘转移工具

MOVER.IO 文件迁移服务,支持OneDrive/GoogleDrive/Dropbox等转到Onedrive。免费无限制。

[airexplorer](https://www.airexplorer.net/zh-cn/clouds/)支持各种网盘，包括百度云盘。转存Google drive时速度慢，会中断

GoogleDrive 共享共享链接不能直接保存到共享团队盘。

## Oneindex
OneIndex是一个基于php的目录程序，将OneDrive的文件目录给列出来，文件不占用服务器的空间，仅仅占用OneDrive容量，流量也不用走服务器流量。支持部分音视频/图片格式在线浏览和下载，同时还支持图床。可上传服务器文件到OneDrive。

oneindex不支持flv在线预览（部分版本支持）。

项目地址: https://github.com/donwa/oneindex

环境要求
1. PHP空间，PHP 5.6+ 需打开curl支持
2. OneDrive 账号 (个人、企业版或教育版/工作或学校帐户)。此处使用Office E5账号。
3. OneIndex 程序

### 部署

```
# 创建好网站进入域名根目录
# 使用宝塔的远程下载
https://github.com/donwa/oneindex/archive/master.zip
# 下载好后解压复制所有文件到网站根目录
#给config和cache两个目录赋予权限
chmod -R 777 config cache

```

打开该域名，就进入安装的页面了，这个地方我们先不急着在前端配置。先在后端nginx编写伪静态，在该网站的伪静态添加

```
if (!-f $request_filename)
        {
            set $rule_0 1$rule_0;
        }
        if (!-d $request_filename)
        {
            set $rule_0 2$rule_0;
        }
        if ($rule_0 = "21")
        {
        rewrite ^/(.*)$ /index.php?/$1 last;
        }

```

目的是为了避免链接中存在`/?/`

### 安装
登录你的域名看到OneIndex的安装页面。

点击下一步开始配置。

填写完成后，通过`域名/admin`访问管理页面，不出意外的话就安装成功啦！

### 注意事项
1. 注意PHP版本 5.6版本可用；
2. 注意使用的MS账号。此处账号为`**@*.onmicrosoft.com`。新开隐身窗口操作。
3. 安装失败可重新获取onedrive 的 PHP开发的 Secret和应用 ID。
4. 打开onedrive进行初始化(从office.com/?auth 进入SharePoint/onedrive)；初始化后才能修改5T容量。
5. 给账号分配许可证。
6. [oneindex](https://github.com/donwa/oneindex);[Office 的加载项开发](https://www.misterma.com/archives/852/)；[Outlook 开发](https://blog.curlc.com/archives/599.html)；[Office Add-in tutorial](https://docs.microsoft.com/en-us/office/dev/add-ins/)。
7. 管理员账号绑不上时（提示安装失败），换其他账号。

### 配置
**文件目录**
有时候你不想将onedrive里的所有文件分享给别人，你可以选择在onedrive中新建一个专门用于共享的目录（比如/share），并修改设置中的「onedrive起始目录」为/share。

**缓存**
直接打开https://你的域名/?/admin/cache
复制其中的代码

**登录后端**

```
 crontab -e
 #添加（以下基于lnmp一键脚本）
 */10 * * * * php /home/wwwroot/你的域名/one.php cache:refresh

```
10分钟刷新一下目录，也可以在页面缓存里直接点击重建所有缓存.

### 图床
oneindex的图床非常人性化，只需要访问oneindex的域名/images即可开始使用
文件全部保存在自己的onedrive的网盘中，不用担心丢失。

# Sharelist

[较新版本](https://github.com/reruin/sharelist/issues/162)支持flv在线预览（部分版本支持）。

配置文件路径`sharelist/cache/config.json`。
配置完成后`chmod 444 config.json`,天翼云挂载路径仍然会被被重置为根目录，config.json被改变。（20210109）

支持 **本地磁盘、OneDrive、GoogleDrive、WebDav、Sftp** 可上传文件/目录


1. 安装
2. 访问`ip:33001`
3. 选择网盘与配置方式。口令：后台管理密码。

- 用途：使用GoogleDrive 或 OneDrive分享出的文件夹ID。
- 特性：不占服务器空间；可挂载多个GD、OD目录；直链下载；在线预览；自定义中转。
项目地址：https://github.com/reruin/sharelist



## docker
https://reruin.github.io/sharelist/docs/#/zh-cn/installation?id=docker

配置文件在本机： /etc/sharelist

```
docker run -d -v /etc/sharelist:/sharelist/cache -p 33001:33001 --name="sharelist" reruin/sharelist

```


heroku和pm2部署已废弃。


## Onedrive
回调地址错误导致绑定失败[Issue](https://github.com/reruin/sharelist/issues/160)。

所有非标准的uri都不能作为重定向的地址，因而你需要：
  1. 务必使用 `https://xxx.com/a` 地址开启挂载过程（而非 http://ip:33001/a），`https://***.com:33001`也不行。
  2. Azure应用注册界面里，把 https://xxx.com/a作为重定向URI。

需要配置nginx和gttps解决。还是使用其它程序挂载onedrive吧。

## 掉线重新配置
退出microsoft登录，对应网盘配置填/ ，进入挂载向导。访问https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade 登录，查看app id，新建secret。填入即可。

## Ali阿里云盘
refresh_token 参数可在 Chrome -> F12 -> Application -> Local Storage -> token -> refresh_token 中寻找

# 私有网盘
要求：`多平台（包括浏览器管理）+对接其它云存储+版本控制+在线功能`
需要选择一家能稳定访问的大硬盘VPS。

自建Nextcloud影音中心:Aria2离线下载+PotPlayer和Kodi本地观看

[Google Trends 比较](https://trends.google.com/trends/explore?q=ownCloud,Nextcloud,Seafile,Pydio,Cloudreve)
[nextcloud-vs-owncloud](https://civihosting.com/blog/nextcloud-vs-owncloud/)
[5款开源网盘](https://www.laobuluo.com/3032.html)

- GCE流量太贵。
- DO的Ubuntu机器上无法安装snap，提示需要更新内核；无法更新内核。
- 使用文件安装的方法安装失败。

## 网盘

[**Next Cloud**](https://github.com/nextcloud/server)
跨平台跨设备文件同步、共享、版本控制、团队协作等功能。通过Rclone挂载其它网盘。（有提供免费容量的[服务商](https://nextcloud.com/signup/)）
[snap安装版](https://github.com/nextcloud/nextcloud-snap)
安装时需要创建 nextcloud 用的数据库以及用户密码。

[**owncloud**](https://github.com/owncloud/core)
个人与团队。支持将文件上传到公有云服务。

**seaflie**
文件同步、共享、跨平台访问、团队协作等功能。文件切割保存。
**Z-File**
支持后端对接各种云存储服务，比如OneDrive 国际/家庭/个人版
**Cloudreve**
私有云网盘，也可作为小型公共云盘给公司、团队使用。对接其它云存储。
**FileRun**
是一个功能强大的在线文件管理器，也可以当成网盘使用。自建在线办公。
对接国内外多家云存储平台。

## VPS列表程序
查看VPS目录。
**Directory Lister**
最简洁目录列表程序
**h5ai**
h5ai也是一个目录列表程序，但是功能上增加了文件上传、预览、分类导航等等。
**Evoluted Directory Listing**
删除/上传/创建文件夹等等。可以当私人网盘使用，而且程序就一个index.php文件，很简洁

## 在线分享网盘
[文叔叔](https://www.wenshushu.cn/)
[奶牛快传](https://cowtransfer.com/)
[airportal](https://airportal.cn/)
[send-anywhere](https://send-anywhere.com/)
[send.firefox](https://send.firefox.com/)
[dropbox transfer](https://www.dropbox.com/transfer/)

# Rclone 与webdav

[Rclone](https://github.com/rclone/rclone)，支持挂载多数国外网盘。Rclone还支持SFTP 、FTP 、HTTP挂载。

Webdav:基于Web的分布式编写和版本控制（WebDAV）是超文本传输协议（HTTP）的扩展，有利于用户间协同编辑和管理存储在万维网服务器文档。
WebDAV可以被映射为一个本地文件夹。
利用这个协议你同步文件不需要任何某个特定的客户端，甚至用电脑的资源管理器就可以使用部分功能。
**WebDAV功能包括**：
* 锁定（也称为并发控制），可以防止意外覆盖文件；
* XML属性，方便对元数据进行操作（例如存储和检索），以便可以组织有关其他数据的数据；
* DAV协议，可进行属性设置，删除和检索；
* DASL（DAV搜索和定位）协议，该协议允许基于属性值进行搜索以在Web上定位资源；
* 命名空间操作，支持复制和移动操作。可以创建和列出类似于文件系统目录的集合。

**支持的网盘**

| 云盘 | 简介 |
|----|----|
|坚果云|每月1GB的上传流量和3GB的下载流量|
|OneDrive| https://d.docs.live.net/+special ID（个人版）|
|GoogleDrive|国内不可用|
|Box|https://www.box.com/dav|
|Yandex|https://webdav.yandex.com|
|TeraCloud|yura.teracloud.jp。90 天内未登陆将被删除|
|4share|https://webdav.4shared.com/|

**私有云盘** 支持：
nextcloud
owncloud
seaflie
Firerun

## 挂载
**Linux:**
[Linux VPS挂载Google Drive和Dropbox-实现VPS主机数据同步备份](https://wzfou.com/linux-vps-drive/)
[VPS挂载国内外网盘实现免费扩容工具:Rclone,COS-Fuse和OSSFS](https://wzfou.com/rclone-cos-fuse-ossfs/)

**Windows:**
[本地网络磁盘RaiDrive挂载Dropbox,Google Drive,OneDrive支持WebDAV,FTP,SFTP](https://wzfou.com/raidrive/)
[Raiddrive](https://www.raidrive.com)Windows平台的客户端。
2020年3月份收到邮件说挂载 Onedrive 、Google Derive 要收费了

**Android**：
FileManager+
ES Explorer：可FTP；不支持自定义的Webdav。
**其它客户端和平台：**
[其它支持的客户端](https://teracloud.jp/en/clients.html)。Winscp等。

## 映射到资源管理器

映射网络驱动器或添加网络位置两种方式。
这种方法可能造成资源管理器卡顿。

Onedrive的操作步骤：
1. 登陆 https://onedrive.live.com
2. 查看地址栏 https://onedrive.live.com/?id=root&cid=1234567890ABCDEF
3. 按照链接样式拼接网络驱动地址,并复制到粘贴板.
https://d.docs.live.net/1234567890ABCDEF
4. 打开资源管理器 --> 映射网络驱动器
5. 选择驱动器符号,粘贴拼接的地址,勾选登陆时重新连接
6. 登陆
7. 重命名驱动器(可选)

## 挂载到Windows

**拖拽复制必卡死**
在网络带宽不是非常大时，RaidDrive或资源管理器自带的映射均容易造成卡死，Rclone的方式能稍微缓解。与网盘容量或里面的文件也有关系。
推荐使用命令行或GUI操作。

rclone ： 
https://rclone.org/downloads/
https://github.com/rclone/rclone

配置文件路径`"C:\Users\Administrator\.config\rclone\rclone.conf"`

需安装依赖winfsp ：
https://github.com/billziss-gh/winfsp/releases
http://www.secfs.net/winfsp/download/    

GUI：
Rclone GUI : https://github.com/mmozeiko/RcloneBrowser


```
rclone mount OneDrive:/ O: --cache-dir D:\OnedriveTmp --allow-other --vfs-cache-mode writes --allow-non-empty
```
其中`--cache-dir` 为执行`rclone copy/sync`，先将文件拷贝到中转文件见`D:\OnedriveTmp`。

示例
`rclone copy C:\Users\Administrator\Downloads\Programs\MicrosoftEdgeSetup.exe O:\`

[vfs-cache-mode](https://rclone.org/commands/rclone_mount/):
* off： In this mode the cache will read directly from the remote and write directly to the remote without caching anything on disk. （本地不做任何缓存，所有文件直接从云端获取并写入。建议网速特别好时（复制粘贴大文件时建议至少100M管以上速度）使用。
* minimal： This is very similar to “off” except that files opened for read AND write will be buffered to disks. This means that files opened for write will be a lot more compatible, but uses the minimal disk space. （和off类似，但是已经打开的文件会被缓存到本地。个人推荐，小文件基本够用，但是如果你的网络情况（梯子）不是特别好的话，用writes也行
* writes： In this mode files opened for read only are still read directly from the remote, write only and read/write files are buffered to disk first. （如果文件属性为只读则只从云端获取，不然先缓存在本地进行读写操作，随后被同步。个人推荐使用，但是在直接从本地复制文件到GDrive时还是看网络情况
* full：In this mode all reads and writes are buffered to and from disk. When a file is opened for read it will be downloaded in its entirety first. （所有的读写操作都会缓存到磁盘中。然后才会同步。不是很推荐。会导致所有文件均被缓存到本地。直到达到你缓存总额（--cache-total-chunk-size，默认大小10G）。但是你网速特别差时也可以使用。

## 自启动方法
1. 脚本
```
Option Explicit
Dim WMIService, Process, Processes, Flag, WS
Set WMIService = GetObject("winmgmts:{impersonationlevel=impersonate}!\\.\root\cimv2")
Set Processes = WMIService.ExecQuery("select * from win32_process")
Flag = true
for each Process in Processes
    if strcomp(Process.name, "rclone.exe") = 0 then
        Flag = false
        exit for
    end if
next
Set WMIService = nothing
if Flag then
    Set WS = Wscript.CreateObject("Wscript.Shell")
    WS.Run "rclone mount OneDrive:/ O: --cache-dir D:\OnedriveTmp --allow-other --vfs-cache-mode writes --allow-non-empty", 0
end if
```
2. 使用winsw加入服务
创建后启动失败,log:`Config file "C:\\WINDOWS\\system32\\config\\systemprofile\\.config\\rclone\\rclone.conf" not found - using defaults`
未找到更改此路径的方法。
`rclone config file`查看conf文件路径；将现有现有conf文件创建硬链接到上述文件夹中：
```
mklink /H C:\Windows\System32\config\systemprofile\.config\rclone\rclone.conf C:\Users\Administrator\.config\rclone\rclone.conf
```

`/H`参数：硬链接。

# Office E5 刷api
[Office E5 开发者订阅注册后的自动续期多种续签方式](https://ensu.cc/archives/928/)
https://github.com/wangziyingwen/AutoApiSecret
https://github.com/iyear/E5SubBot https://github.com/rainerosion/E5SubBotForSQLite （有许多bot可用）
https://e5.qyi.io/ https://blog.curlc.com/archives/687.html

Oneindex、PyOne都是调用api
Onelist不是。

1. 首先去https://portal.azure.com/#home 进入 Azure Active Directory注册一个应用，重定向url选web，填入`http://localhost:53682/`(应该是返回到rclone的地址)。

2. 左侧目录的API权限,依次点击`添加权限、 Microsoft Graph 、委托的权限`,然后依次搜索以下这12个权限并勾选:

```
Files.Read.All Files.ReadWrite.All Sites.Read.All Sites.ReadWrite.All
User.Read.All User.ReadWrite.All Directory.Read.Al Directory.ReadWrite.All
Mail.Read Mail.ReadWrite MailboxSettings.Read MailboxSettings.ReadWrite

```
再点一下`代表xxx授予管理员同意`
3. 点击左侧证书和密码,点+新客户端密码,
4. 使用rclone获取`refresh_token`
   `rclone authorize "onedrive" "之前保存的应用id" "之前保存的应用秘钥"`
5. 将获取的`ID`、`secret`、`refresh_token`填入，运行[脚本](https://github.com/wangziyingwen/AutoApiSecret)，`python(3) 1.py`。
脚本中包含10个API,不需要全部调用。
6. 以自己去graph浏览器看一下，学着自己修改要调用什么api(最重要的是调用outlook、onedrive) developer.microsoft.com/zh-CN/graph/graph-explorer/preview
7. 可以使用GitHub action 运行此脚本。
8. 可按照[官方教程](https://docs.microsoft.com/en-us/office/dev/add-ins/quickstarts/word-quickstart?tabs=yeomangenerator)使用`Visual Studio`开发word/excel等扩展。

# Linux备份
http://cn.linux.vbird.org/linux_basic/0580backup.php
待续...
