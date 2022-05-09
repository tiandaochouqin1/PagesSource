---
title: ADB应用管理
date: 2020-03-14 13:18:52
tags: 其它
categories: 其它
---
<font face="微软雅黑"> </font>
<center>选用 shizuku A类授权+Appops/小黑屋/冰箱/</center>

<!-- more -->
https://github.com/mzlogin/awesome-adb



# ADB是什么

 Android 调试桥([adb](https://developer.android.google.cn/studio/command-line/adb)) 是一个通用命令行工具，其允许您与模拟器实例或连接的 Android 设备进行通信。它可为各种设备操作提供便利，如安装和调试应用，并提供对 Unix shell（可用来在模拟器或连接的设备上运行各种命令）的访问。
 [Google 提供的 SDK Platform Tools](https://dl.google.com/android/repository/platform-tools-latest-windows.zip)
[ADB_Android developer](https://developer.android.com/studio/command-line/adb?hl=zh-cn#howadbworks)
## 三种授权级别
以 adb 来进行隐藏操作，达到系统原本达不到的目的，实现方式其实有许多种。为了方便介绍，在这里我先简单分成以下三类：
`A类`：通过 adb 启动一个 .sh 脚本进行提权，从而获得极高的权限，对 app 持有生杀大权。这一类因其难度较低，能实现的功能也比较全面，所以相对普遍。缺点是重启后就需要重新进行 adb 提权操作。
`B类`：**需要删除手机现有所有账户！！**。通过 adb 将一个 app 任命为「设备管理员」，为你掌管设备权限，权限也比较高。重启后不会失效，但是任命的步骤繁琐、在一些国产 ROM 上有兼容问题。
`C类`：通过 adb 赋予 app 部分敏感权限，权限较低，获得的能力也极为有限。但好在步骤不繁琐、重启也不会失效。

|  | 监控 app | 隐私控制 | 压制毒瘤| | | | | | |
|--- |---|---|---|---|---| ---| ---| ---|---|
| | 了解应用运行状况 | 权限控制（App Ops） | 应用待机Standby | 强制打盹（Doze） | 强行停止（Force Stop） | 冻结/停用（Disable） | 额外功能 | 备注 | 价格 |
|黑阈（A） | ⭕️ | ⭕️ | |⭕️  | ⭕️ | ⭕️ | 执行 adb 指令 | 政策多变 | 高级功能|
|Shizuku Manager（A） |  |  |  |  |  |  | 专授权给其它应用 |  | 免费|
|App Ops（A） |  | ⭕️ |  |  |  |  | 支持设备管理员授权 |  | 免费|
|冰箱（AB） |  |  |  |  |  | ⭕️ |  |  | 冻结10个应用的限制|
|小黑屋（AB） |  |  |  |  |  | ⭕️ | 静默安装 |  | 解锁静默安装等功能|
|权限狗（A） |  | ⭕️ |  |  |  |  |  |   | 免费|
|炼妖壶（B？） |  |  |  |  |  | ⭕️ | 双开/静默安装 |   | 免费|
|绿色守护（C） | ⭕️ |  |  | ⭕️ |  |  | 强制后台纯净 |  | 免费+捐赠|
|BBS（C） | ⭕️ |  |  |  |  |  |  |  | XDA Lab 可免费下载|
|Naptime（C） |  |  |  | ⭕️ |  |  |  |  | 免费+捐赠|
|Blocker（A） |  |  |  |  |  |  | 控制应用服务 | 重打包 App | 免费|

## A类
**以 Shizuku Manager 为例**
提到A类激活方式，除了凶名远扬的黑阈，大家比较熟知的可能就是来自人气开发者 Rikka 的 Shizuku Manager。与 Riru 系列的思路相似，Shizuku Manager 也是「占坑后提供 API 分配授权」的典范。如果需要使用多个A类授权的 app，比如冰箱、App Ops 等，那么先激活 Shizuku Manager ，再透过它对其它 app 进行授权会是一个比较省心的方案。

方式：将手机连上电脑，输入对应指令 `adb shell sh /sdcard/Android/data/moe.shizuku.privileged.api/files/start.sh` 即可。其余 app 的激活指令各不相同，但一般都以 .sh 结尾，会在引导界面给出。

注意事项：`不要改动手机 USB 默认选项、不要关闭开发者选项或者是 adb 调试，这些将会导致授权失效`。

花式玩法：市面上有售许多「黑阈激活器」之类的小玩意，为了尝鲜我也买了一个。想在没有电脑的情况下使用，除了激活器以外还需要一个 USB-A 口电源和一条 A2C 之类的数据线，使用起来也相当繁琐。还有就是一些A类授权的应用在提权完毕后，可以代替电脑上的终端来「执行指令」，给其它A类、B类、C类进行授权。比如黑阈在完成提权后，可以激活 Shizuku Manager，甚至简单输入 reboot 来重启。

## B类
**以小黑屋为例**
**需要删除手机现有所有账户！！**。
冰箱和小黑屋都是冻结类优化 app 中的翘楚。小黑屋同时支持单独使用A类激活和B类激活，也支持使用冰箱、Shizuku Manager 等激活。

注意事项：国产厂商以及三星可能修改了许多 Android 的底层机制，导致使用这个具有风险。请先查看 小黑屋的文档 和 [冰箱的文档](https://github.com/heruoxin/Ice-Box-Docs/blob/master/Device%20Owner%20%EF%BC%88%E5%85%8D%20root%EF%BC%89%E6%A8%A1%E5%BC%8F%E8%AE%BE%E7%BD%AE.md) 。

炼妖壶是使用google工作资料管理权限，不需要adb授权。

## C类
**以绿色守护为例**
绿色守护的鼎鼎大名，我想没有哪个玩家还没听说过。就算是非 root 模式，绿色守护也能起到一定的辅助优化作用，其中个人觉得最为突出的便是嗜睡模式，强制手机在熄灭屏幕后进入 Doze ，可以起到显著节电的效果。

类似功能的还有来自国外著名内核、应用开发者 Franco 的 Naptime。BBS 是 BetterBatteryStats 的简称，主要作用是检测 CPU 的 Deep Sleep 时长、Alarms 以及 Wakelock 唤醒锁的发生情况，帮助玩家抓住幕后的唤醒凶手，可谓是神探一名。

方式：大体上来说，还是复制粘贴 adb 命令，不过这些命令的作用不是激活脚本或是任命设备管理员，而是赋予某个 app 某个敏感权限罢了。

以 绿色守护 为例，
连接手机至电脑后，依次在终端输入：

```
adb -d shell pm grant com.oasisfeng.greenify android.permission.WRITE_SECURE_SETTINGS
adb -d shell pm grant com.oasisfeng.greenify android.permission.DUMP
adb -d shell pm grant com.oasisfeng.greenify android.permission.READ_LOGS 
adb -d shell pm grant com.oasisfeng.greenify android.permission.GET_APP_OPS_STATS

```

之后可以强行停止一次绿色守护再运行，确保权限生效。
Naptime、BBS 的授权过程也类似，app 的引导界面也附上了相应的 adb 指令。

# 权限管理软件

## shizuku
[帮助](https://shizuku.rikka.app/zh-hans/)
给其他软件授权，如appops，冰箱。
重启手机后需要重新获取权限：

```
    adb shell sh /sdcard/Android/data/moe.shizuku.privileged.api/files/start.sh

```


**MIUI 11**及更新版本系统需要关闭MIUI优化，故不再使用此工具。

每次更换usb连接选项后shizuku需重新授权：

```
adb tcpip 5555
```
```
adb shell sh /data/user_de/0/moe.shizuku.privileged.api/start.sh

```

## App Ops
[帮助](https://appops.rikka.app/zh-hans/)
管理应用权限。需要从shizuku获取权限
如果使用 adb，每次开机都需要使用 adb 进行启动 Shizuku 的步骤（但 appops 设置总是生效）

## SystemUI Tuner 管理状态栏

```
adb shell pm grant com.zacharee1.systemuituner android.permission.WRITE_SECURE_SETTINGS
adb shell pm grant com.zacharee1.systemuituner android.permission.PACKAGE_USAGE_STATS
adb shell pm grant com.zacharee1.systemuituner android.permission.DUMP

```

mute volumn zen 分别代表 静音 震动 免打扰
无法隐藏的图标：HD
健康使用手机可在应用管理中隐藏

# ADB命令

[玩转ADB命令](https://blog.csdn.net/zhonglunshun/article/details/78362439)

    pm diable-user 应用名
    pm enable 应用名

    pm install 安装应用
    pm unitall 卸载应用
    pm clear 清除应用缓存

## 包名查询
pm list 安装包名查询

    adb shell pm list package 列出所有应用
    adb shell pm list package -3 列出第三方应用
    adb shell pm list instrumentation 列出所有测试包

ES文件浏览器可查看系统/第三方应用包名。

```
adb shell pm disable-user com.android.mediacenter 音乐
adb shell pm disable-user com.huawei.himovie 视频
adb shell pm disable-user com.huawei.android.hwouc  系统更新
adb shell pm disable-user com.android.browser  自带的华为浏览器
adb shell pm disable-user com.huawei.vassistant  语音助手
adb shell pm disable-user com.huawei.phoneservice 服务
adb shell pm disable-user com.huawei.vdrive 驾驶模式
adb shell pm disable-user com.android.hwmirror 镜子
adb shell pm disable-user com.huawei.android.thememanager 主题
adb shell pm disable-user com.hicloud.android.clone 克隆
adb shell pm disable-user com.android.email 邮件
adb shell pm disable-user com.iflytek.speechsuite 讯飞语音引擎
adb shell pm disable-user com.baidu.input_huawei 百度输入法华为版
adb shell pm disable-user com.huawei.remoteassistant 亲情关怀
pm disable-user com.huawei.android.pushagent    推送服务
pm disable-user com.huawei.bd    用户体验计划
pm disable-user com.android.keyguard 华为杂志锁屏

```


## EMUI关闭系统更新

1. 下载ADB工具包   huawei_ADB.zip (639.35 KB, 下载次数: 1269)
2. 打开调试模式，可手机在开发者选项里如下设置：`开启usb调试；”仅充电”模式下允许adb调试（使用后不要关闭！）`
3. 下载好的ADB工具包解压到桌面双击运行 cmd.exe
输入： `adb devices` 回车,显示已连接设备
输入：`adb shell` 回车，进入shell
输入：`pm disable-user com.huawei.android.hwouc` 回车
效果如下：
`package com.huawei.android.hwouc  new state:disabled-user`
4. 可以在应用管理中 重新启用。

更新冻结其他应用

```

pm disable-user com.huawei.skytone 天际通数据服务
pm disable-user com.huawei.parentcontrol   学生模式

pm disable-user com.huawei.android.FloatTasks 悬浮导航
pm disable-user com.huawei.securitymgr  隐私空间
pm disable-user com.google.android.gms    Google Play 服务
pm disable-user com.google.android.gsf    Google 服务框架
pm disable-user com.android.exchange     exchange服务

pm disable-user com.huawei.intelligent   智能助手 情景智能/负一屏
pm disable-user com.android.emergency  个人紧急信息
pm disable-user com.huawei.bd 华为用户体验
pm disable-user com.huawei.search    智慧搜索
pm disable-user com.android.simappdialog     sim卡应用

```
## MIUI冻结应用
[免root 使用ADB命令卸载系统预装APP](https://www.52pojie.cn/thread-888403-1-1.html)
[使用adb卸载MIUI自带app](https://mashiro.best/archives/adb-uninstall-app)

输入: `adb devices` 回车,显示已连接设备
输入: `adb shell` 回车，进入shell
使用命令`pm disable-user + name`：

```
com.android.browser  （浏览器）
com.miui.systemAdSolution  （小米系统广告解决方案，必删）
com.miui.analytics  （小米广告分析，必删）
com.miui.player  （小米音乐）
com.miui.video  （小米视频）
com.xiaomi.gamecenter.sdk.service  （小米游戏中心服务）
com.xiaomi.gamecenter  （小米游戏中心）
```

```
com.sohu.inputmethod.sogou.xiaomi  （搜狗输入法）
com.miui.notes  （小米便签）
com.miui.translation.youdao  （有道翻译）
com.miui.translation.kingsoft  （金山翻译）
com.android.email  （邮件）
com.xiaomi.scanner  （小米扫描）
com.miui.hybrid  （混合器）
com.miui.bugreport  （bug 反馈）
com.milink.service  （米连服务）
com.miui.gallery  （相册）
com.miui.yellowpage  （黄页）
com.xiaomi.midrop  （小米快传）
com.miui.virtualsim  （小米虚拟器）
com.xiaomi.payment  （小米支付）
com.mipay.wallet  （小米钱包）
com.android.soundrecorder  （录音机）
com.miui.screenrecorder  （屏幕录制）
com.android.wallpaper  （壁纸）
com.miui.voiceassist  （语音助手）
com.miui.fm  （收音机）
com.miui.touchassistant  （悬浮球）
com.android.cellbroadcastreceiver  （小米广播）
com.xiaomi.mitunes  （小米助手）
com.xiaomi.pass  （小米卡包）
com.android.thememanager  （个性主题管理）
com.android.wallpaper  （动态壁纸）
com.android.wallpaper.livepicker  （动态壁纸获取）
com.miui.klo.bugreport  （KLO bug 反馈）

```

# scrcpy

[Scrcpy用电脑控制Android手机](https://xushanxiang.com/2019/11/android-scrcpy.html)
[scrcpy](https://github.com/Genymobile/scrcpy#features)
启用adb调试。
不需要任何root权限，不需要在手机里安装任何程序。scrcpy同时适用于GNU / Linux，Windows和macOS。

## usb连接
adb usb查看是否连接成功，运行scrcpy即可
查看已连接设备命令adb devices

## 局域网连接

1. 确保PC和手机在同一Wifi中
2. 手机先通过USB与PC相连
3. 在PC上运行 adb tcpip 服务端口，如端口为5555
4. 拔下手机的USB连接
5. 在PC上运行 adb connect 手机IP:服务端口
6. 像往常一样运行 scrcpy相关命令
7. 若要切换回USB模式：adb usb

默认的scrcpy比特率是8Mbps，降低比特率和分辨率可能是一个很好的折中方案。

scrcpy –bit-rate 2M –max-size 800

scrcpy -b2M -m800 # 简写

## 一些scrcpy命令
1. 启动scrcpy

scrcpy
2. 如果有多个设备，需要指定序列号，序列号可以从adb devices获得

scrcpy -s 6a86de95﻿
3. 设置端口

scrcpy -p 27184
4. 查看帮助

scrcpy --help
5. 设置码率（默认8M）

scrcpy -b 8M
6. 限制投屏尺寸

scrcpy -m 1024
7. 裁剪投屏屏幕(长:宽:偏移x:偏移y)

scrcpy -c 800:800:0:0
8. 投屏并录屏

scrcpy -r file.mp4
9. 不投屏只录屏

scrcpy -Nr file.mp4
10. 手指触摸的时候显示轨迹球

scrcpy -t
11. 显示版本信息

scrcpy -v
12. 关闭设备屏幕
使用命令行选项启动镜像时，可以关闭设备屏幕：
scrcpy --turn-screen-off
scrcpy -S
或者随时按Ctrl + o。要重新打开它，请按POWER键（或Ctrl + p）。

## 快捷键

|Action|Shortcut|Shortcut (macOS)|
|---|---|---|
|切换全屏模式|Ctrl+f|Cmd+f|
|将窗口调整为1:1 (完美像素)|Ctrl+g|Cmd+g|
|调整窗口大小以删除黑色边框|Ctrl+x | Double-click¹|Cmd+x | Double-click¹|
|返回到HOME|Ctrl+h | Middle-click|Ctrl+h | Middle-click|
|返回|Ctrl+b | Right-click²|Cmd+b | Right-click²|
|Click on APP_SWITCH|Ctrl+s|Cmd+s|
|点击菜单|Ctrl+m|Ctrl+m|
|调节音量|Ctrl+↑ (up)|Cmd+↑ (up)|
|调节音量|Ctrl+↓ (down)|Cmd+↓ (down)|
|点击手机电源|Ctrl+p|Cmd+p|
|Power on（打开）|Right-click²|Right-click²|
|关闭设备屏幕（保持镜像）|Ctrl+o|Cmd+o|
|展开通知面板|Ctrl+n|Cmd+n|
|折叠通知面板|Ctrl+Shift+n|Cmd+Shift+n|
|将设备剪贴板复制到计算机|Ctrl+c|Cmd+c|
|将计算机剪贴板粘贴到设备|Ctrl+v|Cmd+v|
|将计算机剪贴板复制到设备|Ctrl+Shift+v|Cmd+Shift+v|
|启用/禁用FPS计数器（在标准输出上）|Ctrl+i|Cmd+i|

# EMUI
## 华为荣耀6
fast boot:关机键 + 音量-
recovery:关机键 + 音量+
强刷：三键同时持续按10秒以上

开机显示`func  9 boot kernel`，无法进入recovery，可进入fastboot。
[线刷](https://club.huawei.com/thread-12409925-1-1.html)官方包无效：（此处可能已经刷入了官方的recovery）
然后三键强刷卡刷包：
直接使用下载的包刷失败。解压下载的包，使用SD卡，新建目录/dload/，放入UPDATE.APP和au_temp.cfg（未知是否必须）。

升级成功，手机数据还在，recovery为官方，root权限没了。

[重新获取root权限](https://club.huawei.com/thread-14504243-1-1.html)需要刷入第三方recovery+卡刷root包。

