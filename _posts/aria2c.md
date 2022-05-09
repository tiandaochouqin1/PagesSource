---
title: aria2c远程离线下载
tags:
  - 下载
  - 网络
categories: 网络
date: 2020-04-02 12:42:38
---
<font face="微软雅黑"> </font>
<center>配置ariac远程离线下载 </center>

<!-- more -->

Aria2 是一个功能非常强大且功能非常齐全的下载工具，它支持 `BT、磁力、HTTP、FTP` 等下载协议，常用做离线下载的服务端。

[Aria2c项目](https://github.com/aria2/aria2/releases/latest)

[Aria2 新手入门教程](https://p3terx.com/archives/aria2-started-guide.html)


# Windows

## 直接使用

```
aria2c url
aria2c -i export.txt //比如从BIlibili Evolved导出的链接
aria2c -p url

```
## aria2 RPC

把 aria2c.exe 放到任意文件夹，比如 D:/aria2。

1. 在同目录下新建几个文件：
 aria2.session（下载历史，空文件就行）
2. aria2.conf（配置文件）:见最后一章。

3. 开机自启动或WinS W加入服务；
   `aria2c  --conf-path=aria2.conf`

4. UI环境下管理下载
  

## 隐藏cmd窗口运行
建立的 HideRun.vbs，复制如下代码保存。

    CreateObject("WScript.Shell").Run "aria2c.exe --conf-path=aria2.conf",0
打开 HideRun.vbs 这个文件，即可实现隐藏 cmd 窗口运行 Aria2。

### 开机启动
设置开机启动不必担心浪费系统资源，因为 Aria2 非常轻量，只占用 2M 多的内存。

打开 “运行” 对话框（WIN+R），输入以下任一命令，回车或点击 “确定” 即可打开 “系统启动文件夹”。

    shell:Common Startup
    %programdata%\Microsoft\Windows\Start Menu\Programs\Startup

## Aria2 管理面板
Aria2 是命令行下载器，是没有界面的，需要配合前端面板才能更加便利的使用。

**WebUI**
最简单的方式就是访问这几个网址：

1. AriaNg：http://ariang.mayswind.net/latest （强烈推荐）

2. WebUI-Aria2：https://ziahamza.github.io/webui-aria2

3. Photon-WebUI：https://alanzhangzm.github.io/Photon-WebUI

4. YAAW 中文版：http://aria2c.com/

5. P3TERX  http://p3terx.gitee.io/ariang/#!/downloading


## Aria2 NG 部署
是一个静态网页

* 网页（WebUI）
AriaNg 是一个 Aria2 的 Web 前端，你可以在项目的 [releases](https://github.com/mayswind/AriaNg/releases/tag/1.1.4) 页面下载到它，其中 AllI­nOne 版可以在本地直接打开使用，而标准版适合部署到 Web 服务器。

* 本地程序
[AriaNg Native](https://github.com/mayswind/AriaNg-Native) 是 Web 前端的本地化程序，增加了一些额外的功能。它支持 Win­dows 和 ma­cOS ，下载安装后打开就能使用，不需要浏览器

* 在 AriaNg （或其它前端面板）中对 Aria2 设置的修改属于临时修改
* 修改配置后需要重启
* RPC 请求方式只建议使用POST，否则可能导致 BT 种子无法传递到服务端。


## 其它本地程序

- AriaNg Native	非常优秀的本地前端程序，需要配合 Aria2 后端程序使用。
- PanDownload	可能是最好用的第三方百度网盘下载工具，基于 Aria2 ，也可作为远程 Aria2 的前端，仅支持下载百度网盘的资源。不支持 RPC https 连接。
- SpeedPan	另一款基于 Aria2 的第三方百度网盘下载工具，支持 RPC https 连接。
- Motrix	基于 Aria2 的全能的下载工具，支持下载 HTTP、FTP、BT、磁力链、百度网盘等资源，但不支持连接其它 Aria2 后端，仅能作为本地下载工具。
- Photon	基于 Aria2 的下载工具，功能简单，仅能作为本地下载工具。

浏览器插件
Aria2 manager
Aria2助手

## 路由器

开启路由器上的 Aria2 ，使用前端面板比如 AriaNg 连接路由器上的 Aria2 后端程序。

一般进行 BT 下载的速度非常慢，一般只用于取回 VPS 或网盘上的文件。

# aria2c与rclone实现离线下载

在 VPS 上使用 Aria2 一键安装管理脚本进行安装，使用前端面板比如 `Ar­i­aNg` 连接到 `VPS 上的 Aria2 后端程序`，将链接传到VPS下载。

下载完成后可以使用 Xftp、FileZilla 这类`SFTP`  工具进行取回，或者在 VPS 上搭建 `Nextcloud、File Browser` 等网盘服务进行下载。如果你的 VPS 上装有`宝塔面板`，可以从管理后台进行下载。

## 离线下载到网盘
下载到VPS，自动上传到网盘。
一些大容量不限速的网盘（比如 OneDrive）没有离线下载功能。

[Aria2 + Rclone 实现 OneDrive、Google Drive 等网盘离线下载](https://p3terx.com/archives/offline-download-of-onedrive-gdrive.html)


Aria2 有一个配置项 `on-download-complete`，在下载完后执行一个脚本。当下载完成后 Aria2 会给脚本传递 3 个变量 $1、$2、$3 分别为 gid 、文件数量、文件路径

Aria2 一键安装管理脚本 整合了 Aria2 完美配置 ，以及Rclone 自动上传脚本就是其中之一。


1. 安装 Aria2
这里使用 Aria2 一键安装管理脚本，执行下面的代码下载并运行脚本，出现脚本操作菜单输入 1 开始安装。

    bash <(wget -qO- git.io/aria2.sh)
2. 安装和配置 Rclone
[官方文档](https://rclone.org/docs/)
官方提供了一键安装脚本：

    curl https://rclone.org/install.sh | sudo bash
安装完后，输入 rclone config 命令进入交互式配置选项。
RcloneOneDrive使用[rclone](https://rclone.org/downloads/)获取`access_token`；Windows下执行`rclone authorize "onedrive"`。
也有`ID+Secret key`选项。
GoogleDrive可直接配置。
[《Rclone 安装配置教程》](https://p3terx.com/archives/rclone-installation-and-configuration-tutorial.html)配置的详细过程。

1. 配置自动上传脚本
Rclone 自动上传脚本默认不启用，所以需要手动启用。
输入nano /root/.aria2/autoupload.sh打开自动上传脚本进行编辑，脚本中有中文注释，按照自己的实际情况进行修改，一般只需要修改下面2个部分。
```
# Rclone 配置时填写的网盘名(name)
DRIVE_NAME='Onedrive'
# 网盘目录。即上传目标路径，留空为网盘根目录，末尾不要有斜杠。
DRIVE_PATH='/DRIVEX/Download'
```
输入nano /root/.aria2/aria2.conf打开 Aria2 配置文件进行修改。或使用Aria2 一键安装管理脚本中的手动修改选项打开配置文件进行修改。找到“下载完成后执行的命令”，把delete.aria2.sh替换为`autoupload.sh`。
下载完成后执行的命令:
    `on-download-complete=/root/.aria2/autoupload.sh`
4. 重启 Aria2
    `service aria2 restart`
5. 使用
当你进行完以上所有操作，现在下载文件就会自动上传至相应的网盘。


## 谨慎使用自动脚本

配置错误导致自动上传并删除了众多系统文件！！系统无法启动
1. 最开始将网盘名称配置错误；用来下载bilibili文件失败，`/root/Download`中无文件，但是在session中有操作记录；网盘中也无文件。
在这一步中还有另外一个错误：

删系统的的操作应该是在第二步出现的。
2. 改正了网盘名称，并改了上传路径为根目录；下载仍然失败，seesion中无记录。`网盘中出现Linux 所有系统文件目录`。过了一会一些命令无法使用，如`ls、reboot、shutdown`，断开ssh后无法再连上；在面板中强制关机并重启；仍然无法连接，无法Ping通。
**最可能是在配置网盘上传目标路径为空 ' '（根目录）时，错误地配置了下载目录为 ''，即包含Linux所有文件目录。**但是查看备份在网盘的autoupload.sh文件，路径并没错误。也可能是改了多次此文件。

使用的DO的系统；bing/NASA图片网站配置丢失

## docker 版
部署在树莓派上。
[Aria2 Pro - 更好用的 Aria2 Docker 容器镜像](https://p3terx.com/archives/docker-aria2-pro.html)
[项目地址](https://github.com/P3TERX/docker-aria2-pro)

[AriaNg前端](http://ariang.mayswind.net/latest/#!/downloading)

```
docker run -d \
    --name aria2-pro \
    --restart unless-stopped \
    --log-opt max-size=1m \
    --network host \
    -e PUID=$UID \
    -e PGID=$GID \
    -e RPC_SECRET=888888 \
    -e RPC_PORT=6800 \
    -e LISTEN_PORT=6888 \
    -v ~/aria2-config:/config \
    -v ~/rclone-downloads:/downloads \
    -e SPECIAL_MODE=rclone \
    p3terx/aria2-pro
```
下载rclone要15分钟以上。

### AriaNG服务端部署
```
docker run -d \
  --name ariang \
  --log-opt max-size=1m \
  --restart unless-stopped \
  -p 6880:6880 \
  p3terx/ariang
```

### 配置rclone
```
docker exec -it aria2-pro rclone config
```
https://p3terx.com/archives/rclone-installation-and-configuration-tutorial.html

最后修改 Aria2 Pro 配置文件目录下`script.conf`文件中的网盘名称(drive-name)和网盘路径(drive-dir)这两个选项的值。

### 域名解析失败
下载任务提示域名解析失败。使用windows版aria2c下载正常。
给树莓派系统设置了新的dns，未解决问题。

进入docker，在 `config/aria2.conf` 去掉以下两行的注释，重启aria2即可
```
async-dns=true
async-dns-server=114.114.114.114

```
**还需要打开log记录的注释**

# 百度云转存到其它网盘
使用PanDownload、SpeedPan。
[百度网盘转存到 OneDrive 、Google Drive 等其他网盘](https://p3terx.com/archives/baidunetdisk-transfer-to-onedrive-and-google-drive.html)

云盘工具发送下载请求 → Aria2 下载到 VPS → Rclone 上传到 OneDrive 或 Google Drive。

Linux或低性能设备使用：轻量百度客户端[BaiduPCS-Go](https://github.com/iikira/BaiduPCS-Go)；有[web版](https://github.com/liuzhuoling2011/baidupcs-web)（尝试无法登录，bug?）。

# aria2配置

```

## '#'开头为注释内容, 选项都有相应的注释说明, 根据需要修改 ##
## 被注释的选项填写的是默认值, 建议在需要修改时再取消注释  ##

# 日志
log=aria2.log
log-level=error

## 文件保存相关 ##

# 文件的保存路径(可使用绝对路径或相对路径), 默认: 当前启动位置
dir=E:\迅雷下载
# 启用磁盘缓存, 0为禁用缓存, 需1.16以上版本, 默认:16M
disk-cache=32M
# 文件预分配方式, 能有效降低磁盘碎片, 默认:prealloc
# 预分配所需时间: none < falloc ? trunc < prealloc
# falloc和trunc则需要文件系统和内核支持
# NTFS建议使用falloc, EXT3/4建议trunc, MAC 下需要注释此项
file-allocation=falloc
# 断点续传
continue=true

## 下载连接相关 ##

# 最大同时下载任务数, 运行时可修改, 默认:5
max-concurrent-downloads=3
# 同一服务器连接数, 添加时可指定, 默认:1
max-connection-per-server=5
# 最小文件分片大小, 添加时可指定, 取值范围1M -1024M, 默认:20M
# 假定size=10M, 文件为20MiB 则使用两个来源下载; 文件为15MiB 则使用一个来源下载
min-split-size=10M
# 单个任务最大线程数, 添加时可指定, 默认:5
split=5
# 整体下载速度限制, 运行时可修改, 默认:0
#max-overall-download-limit=0
# 单个任务下载速度限制, 默认:0
max-download-limit=0
# 整体上传速度限制, 运行时可修改, 默认:0
max-overall-upload-limit=1M
# 单个任务上传速度限制, 默认:0
#max-upload-limit=0
# 禁用IPv6, 默认:false
disable-ipv6=true

## 进度保存相关 ##

# 从会话文件中读取下载任务
input-file=aria2.session
# 在Aria2退出时保存`错误/未完成`的下载任务到会话文件
save-session=aria2.session
# 定时保存会话, 0为退出时才保存, 需1.16.1以上版本, 默认:0
save-session-interval=60

## RPC相关设置 ##

# 启用RPC, 默认:false
enable-rpc=true
# 允许所有来源, 默认:false
rpc-allow-origin-all=true
# 允许非外部访问, 默认:false
rpc-listen-all=true
# 事件轮询方式, 取值:[epoll, kqueue, port, poll, select], 不同系统默认值不同
#event-poll=select
# RPC监听端口, 端口被占用时可以修改, 默认:6800
rpc-listen-port=6800
# 设置的RPC授权令牌, v1.18.4新增功能, 取代 --rpc-user 和 --rpc-passwd 选项
#rpc-secret=<TOKEN>
# 设置的RPC访问用户名, 此选项新版已废弃, 建议改用 --rpc-secret 选项
#rpc-user=<USER>
# 设置的RPC访问密码, 此选项新版已废弃, 建议改用 --rpc-secret 选项
#rpc-passwd=<PASSWD>

## BT/PT下载相关 ##

# 当下载的是一个种子(以.torrent结尾)时, 自动开始BT任务, 默认:true
follow-torrent=true
# BT监听端口, 当端口被屏蔽时使用, 默认:6881-6999
listen-port=51413-52333
# 单个种子最大连接数，0表示不限制，默认:55
bt-max-peers=0
# 打开DHT功能, PT需要禁用, 默认:true
enable-dht=true
# 打开IPv6 DHT功能, PT需要禁用
enable-dht6=true
# DHT网络监听端口, 默认:6881-6999
dht-listen-port=6881-6999
# 本地节点查找, PT需要禁用, 默认:false
#bt-enable-lpd=false
# 种子交换, PT需要禁用, 默认:true
enable-peer-exchange=true
# 期望下载速度, 对少种的PT很有用, 默认:50K
bt-request-peer-speed-limit=50M
# 客户端伪装, PT需要
peer-id-prefix=-UT3500-
user-agent=uTorrent/3500(43580)
# 最小分享率。当种子的分享率达到这个数时, 自动停止做种, 0为一直做种, 默认:1.0
seed-ratio=0
# 最小做种时间。此选项设置为0时，将在BT任务下载完成后不进行做种。
seed-time=0
# 强制保存会话, 即使任务已经完成, 默认:false
# 较新的版本开启后会在任务完成后依然保留.aria2文件
force-save=true
# BT校验相关, 默认:true
#bt-hash-check-seed=true
# 继续之前的BT任务时, 无需再次校验, 默认:false
bt-seed-unverified=true
# 保存磁力链接元数据为种子文件(.torrent文件), 默认:false
#bt-save-metadata=false
# 删除未选择文件
bt-remove-unselected-file=true

## BT 服务器地址 ##
bt-tracker=udp://tracker.coppersurfer.tk:6969/announce,udp://tracker.opentrackr.org:1337/announce,udp://9.rarbg.to:2710/announce,http://tracker.internetwarriors.net:1337/announce,udp://exodus.desync.com:6969/announce,udp://explodie.org:6969/announce,http://explodie.org:6969/announce,http://tracker1.itzmx.com:8080/announce,udp://tracker1.itzmx.com:8080/announce,udp://ipv4.tracker.harry.lu:80/announce,udp://denis.stalker.upeer.me:6969/announce,udp://tracker.port443.xyz:6969/announce,udp://open.demonii.si:1337/announce,http://tracker.port443.xyz:6969/announce,udp://tracker.torrent.eu.org:451/announce,udp://tracker.tiny-vps.com:6969/announce,udp://tracker.iamhansen.xyz:2000/announce,udp://thetracker.org:80/announce,udp://retracker.lanta-net.ru:2710/announce,udp://open.stealth.si:80/announce,udp://bt.xxx-tracker.com:2710/announce,http://private.minimafia.nl:443/announce,http://prestige.minimafia.nl:443/announce,http://open.acgnxtracker.com:80/announce,udp://tracker.vanitycore.co:6969/announce,udp://zephir.monocul.us:6969/announce,udp://tracker.cyberia.is:6969/announce,https://tracker.fastdownload.xyz:443/announce,https://opentracker.xyz:443/announce,http://tracker3.itzmx.com:6961/announce,http://opentracker.xyz:80/announce,http://open.trackerlist.xyz:80/announce,http://torrent.nwps.ws:80/announce,udp://tracker2.itzmx.com:6961/announce,udp://tracker1.wasabii.com.tw:6969/announce,udp://tracker.gbitt.info:80/announce,udp://tracker.filepit.to:6969/announce,udp://tracker.dyn.im:6969/announce,udp://tracker.dler.org:6969/announce,udp://torrentclub.tech:6969/announce,udp://pubt.in:2710/announce,udp://bittracker.ru:6969/announce,https://tracker.gbitt.info:443/announce,http://tracker2.itzmx.com:6961/announce,http://tracker1.wasabii.com.tw:6969/announce,http://tracker.torrentyorg.pl:80/announce,http://tracker.gbitt.info:80/announce,http://tracker.city9x.com:2710/announce,http://tracker.btsync.gq:233/announce,http://torrentclub.tech:6969/announce,http://t.nyaatracker.com:80/announce,http://retracker.mgts.by:80/announce,http://open.acgtracker.com:1096/announce,http://fxtt.ru:80/announce,http://0d.kebhana.mx:443/announce,wss://tracker.openwebtorrent.com:443/announce,wss://tracker.fastcast.nz:443/announce,wss://tracker.btorrent.xyz:443/announce,udp://tracker4.itzmx.com:2710/announce,udp://tracker.justseed.it:1337/announce,udp://packages.crunchbangplusplus.org:6969/announce,https://1337.abcvg.info:443/announce,http://tracker4.itzmx.com:2710/announce,http://tracker.tfile.me:80/announce.php,http://tracker.tfile.me:80/announce,http://tracker.tfile.co:80/announce,http://share.camoe.cn:8080/announce,http://peersteers.org:80/announce,http://omg.wtftrackr.pw:1337/announce
```
内置了一些 BT 服务器地址，以保证 BT 的下载速度和成功率。