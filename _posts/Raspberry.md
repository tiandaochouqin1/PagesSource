---
title: Raspberry - Openwrt 和 Debian Aarch64
date: 2019-11-24 14:26:05
tags: 路由器
categories: Linux
---
<font face="微软雅黑"> </font>
<center>Raspi 4 刷Openwrt作为旁路由、ss-libev编译</center>

<!-- more -->
- [需求](#需求)
  - [问题描述](#问题描述)
  - [原因分析](#原因分析)
- [编译Openwrt](#编译openwrt)
  - [编译环境](#编译环境)
  - [编译步骤](#编译步骤)
  - [make编译](#make编译)
  - [清理](#清理)
  - [构建单个包](#构建单个包)
  - [多线程编译](#多线程编译)
  - [openwrt常用组件](#openwrt常用组件)
- [编译ss-libev](#编译ss-libev)
  - [特性](#特性)
  - [编译](#编译)
  - [问题](#问题)
  - [编译失败](#编译失败)
  - [配置Raspi作为旁路由](#配置raspi作为旁路由)
- [使用SDK包单独编译软件](#使用sdk包单独编译软件)
  - [刷入三方编译附件](#刷入三方编译附件)
  - [自己编译的固件](#自己编译的固件)
  - [IPV6的三种配置方式](#ipv6的三种配置方式)
- [总结](#总结)
  - [失败原因总结](#失败原因总结)
  - [3. 暂时使用pi作为路由器。](#3-暂时使用pi作为路由器)
  - [PI的问题](#pi的问题)
  - [换回K2](#换回k2)
- [Debian-Pi-Aarch64系统](#debian-pi-aarch64系统)
  - [Raspberry系统连接wifi](#raspberry系统连接wifi)
  - [账户及密码](#账户及密码)
  - [K2更换系统后Raspi无法连接WIFI](#k2更换系统后raspi无法连接wifi)
- [Ubuntu官方系统](#ubuntu官方系统)
  - [无法连接wifi](#无法连接wifi)
  - [串口连接](#串口连接)
    - [接线](#接线)
    - [树莓派配置](#树莓派配置)
    - [终端连接设置](#终端连接设置)
    - [netplan](#netplan)
  - [root用户](#root用户)
  - [Ubuntu Arm镜像源](#ubuntu-arm镜像源)
- [内核移植](#内核移植)

***
出连接问题时用HDMI连显示器。
虚拟机编译太慢了。

已编译好的[openwrt镜像](https://downloads.openwrt.org/snapshots/targets/bcm27xx/bcm2711/)：无luci界面。
[自编译 OpenWrt固件](https://mlapp.cn/369.html)：无ipv6


高性能、集成[Debian-Pi-Aarch64 ★ 全新树莓派64位系统](https://github.com/openfans-community-offical/Debian-Pi-Aarch64)

# 需求

## 问题描述

基于ipv6的ss路由器上网带宽不足。

**1. 斐讯K2：**
CPU：       `mt7620,单核580MHz，MIPS 24K。`
系统环境：  `Openwrt，自己编译的，ss-libev+luci-app-shadowsocks-without-ipset,宿舍。`
使用情况：
                使用fast.com测速。
                白天测速 max download=4 Mbs,max upload=12 Mbs;
                上传速度12Mbs时，CPU占用率97%；download 4Mbs时，CPU占用率25%。
                夜间测速max download=12 Mbs，max upload=12 Mbs。此时CPU=95%。

**2. 台式电脑**
CPU：      `i7 7700,4核 4.0Ghz`
系统环境：  `windows10，ss，实验室。`
使用情况：  
                白天 Max download=7Mbs, Max Upload=100Mbs/200Mbs；
                Download 7Mbs时，CPU=1.8%，Upload 100+Mbs时，CPU=12%（单核吃满）。
                夜间 Max Download=80Mbs，Max Upload=100Mbs。

## 原因分析  

1. 与网络环境有关，可能校内Ipv6网络总带宽有限，故夜晚速度快。
2. 路由器CPU性能不够。故Raspberry Pi4用作旁路由，弥补K2 CPU性能短板。

# 编译Openwrt


[Openwrt_Stable Release builds](https://downloads.openwrt.org/snapshots/targets/bcm27xx/bcm2711/)
[openwrt官网编译指南](https://openwrt.org/zh-cn/doc/howto/build)
[在源码中修改默认参数](https://wp.gxnas.com/2367.html)

## 编译环境

虚拟机VMware，Ubuntu 18，4核2G。编译环境所需硬盘空间15G+。

**注意**

1. 以非root用户来进行工作；
2. 在<buildsystem root>目录中完成所有命令，例如~/openwrt/trunk。
3. 需要外网环境下载依赖，第一次部署环境耗时较长。后续编译一次大约半小时，4线程十分钟左右。

## 编译步骤

1. 更新OpenWrt源。
2. 更新feeds的package 包.
3. 配置编译。
4. 开始编译。这会自动编译工具链，交叉编译源代码，打包包文件，最后生成可以可以刷写的镜像文件。
5. 进入安装OpenWrt环节

        git clone https://www.github.com/openwrt/openwrt //主干代码
        git clone https://www.github.com/openwrt/openwrt -b chaos_calmer //某个发布版源码
        git pull //更新源码
        ./scripts/feeds update -a //更新源
        ./scripts/feeds install -a //安装源(或者 'install <PACKAGENAME>' )
        make menuconfig //进入图形配置界面

## make编译

        make defconfig //将缺省设置生成.config
        make oldconfig //备份.config->.config.old  
**make**
Menuconfig拥有一个文本界面，它包括选择要处理的目标平台，要编译的软件包，要被包含进固件文件的软件包和一些内核设置等。

        make menuconfig
这会自动更新你现在存在的配置的依赖，方便你你准备编译你更新过的镜像。
**高亮错误**

        make V=99 2>&1 | tee build.log | grep -i error
**固件目录**
编译成功后的固件bin文件在 /openwrt/trunk/bin 目录下

## 清理

**Clean**

        make clean 
deletes contents of bin and build_dir directories.

**Dirclean**

        make dirclean
deletes contents of /bin and /build_dir directories and additionally /staging_dir and /toolchain (=the cross-compile tools). 'Dirclean' is your basic “Full clean” operation.
**Distclean**

        make distclean
nukes everything you have compiled or configured and also deletes all downloaded feeds contents and package sources.

## 构建单个包

为OpenWrt开发或打包软件时，可以方便地只构建关心的包（以cups包为例）：

        make package/cups/compile V=99
For the package mc (midnight commander), which is contained the feed packages it looks like this:

        make package/feeds/packages/mc/compile v=99

## 多线程编译

        make -j 3
使用标准公式 <你的CPU个数 + 1>
构建过程中若产生随机错误，尝试去掉-j选项重新构建

## openwrt常用组件

添加luci网页界面

        LuCI --> Collections -->luci
添加简体中文

        LuCI --> Modules --> Translations -->Chinese(zh-cn)
添加网页界面主题

        LuCI --> Themes -->luci-theme-openwrt (喜欢哪个主题就选择那个主题)

其它

        Base system->取消选中dnsmasq->选中**dnsmasq-full**
        因为dnsmasq-full支持ipset功能，对于基于域名的xx很有用。

        Network->File Transfer中选中**curl wget** 【两个下载工具】

        Network->IP Addresses and Names中选中**bind-dig,ddns-scripts_No-IP_com**。（用来支持no-ip.com的ddns服务）
        一个是测试工具；另一个是某个ddns支持，还有其他ddns支持，也可以酌情选中。

        Network->Routing and Rediction中选中**ip-full**,这个很关键。

        Network中选中**iperf3,ipset**。一个是测试工具；另一个是ipset，用于支持基于域名的xx。

        Network->Web Servers/Proxies选中那些**SS**，如果您需要使用SS的话。

        Utilities->Editors中选nano （也可以选**vim**）。

        Utilities->Shells中选中**bash**。

#  编译ss-libev

[ss-libev for Opwenwrt](https://github.com/shadowsocks/openwrt-shadowsocks)
本项目是 shadowsocks-libev 在 OpenWrt 上的移植

## 特性

软件包只包含 shadowsocks-libev 的可执行文件, 可与 luci-app-shadowsocks 搭配使用
可编译两种版本

shadowsocks-libev

        客户端/
        └── usr/
            └── bin/
                ├── ss-local       // 提供 SOCKS 代理
                ├── ss-redir       // 提供透明代理, 从 v2.2.0 开始支持 UDP
                └── ss-tunnel      // 提供端口转发, 可用于 DNS 查询
shadowsocks-libev-server

        服务端/
        └── usr/
            └── bin/
                └── ss-server      // 服务端可执行文件

## 编译

从 OpenWrt 的 SDK 编译

        # 以 ar71xx 平台为例
        tar xjf OpenWrt-SDK-ar71xx-for-linux-x86_64-gcc-4.8-linaro_uClibc-0.9.33.2.tar.bz2
        cd OpenWrt-SDK-ar71xx-*
        # 添加 feeds
        git clone https://github.com/shadowsocks/openwrt-feeds.git package/feeds
        # 获取 shadowsocks-libev Makefile
        git clone https://github.com/shadowsocks/openwrt-shadowsocks.git package/shadowsocks-libev
        # 选择要编译的包 Network -> shadowsocks-libev
        make menuconfig
        # 开始编译
        make package/shadowsocks-libev/compile V=99

**软件配置参考项目主页说明**

## 问题

在menuconfig中**未找到shadowsocks-libev**。Network->Web Servers/Proxies下有ss-libev-redir,ss-libev-tunnel,ss-libev-local等组件。编译openwrt安装后的管理配置界面与ss-libev不一样。

## 编译失败

可能是未使用相应的的openwrt SDK。目前使用的是官网最原始的源，编译速度慢可能也与此有关。

## 配置Raspi作为旁路由

详细配置见另一篇博文：[旁路由类别及配置](https://tiandaochouqin1.github.io/test/Router-on-a-stick)

过程：使用ss-libev-redir,ss-libev-tunnel,ss-libev-local等组件编译openwrt并刷入，分别配置参数。
结果：旁路由不可用。

# 使用SDK包单独编译软件

（本章更新日期为20191125）

[Using SDK](https://oldwiki.archive.openwrt.org/doc/howto/obtain.firmware.sdk)
默认SDK不带有软件包,需要单独下载软件源（ Makefiles for packages to compile must be checked out from the OpenWrt repository and placed into the package/ directory first.）。

SDK是一个可再定位的，预编译好的OpenWrt工具链，适用于在不从头开始编译整个系统的前提下，针对一个特定平台交叉编译单个用户空间包。

使用SDK的原因：

- 为了保证二进制和特性兼容性，针对特定的发行版编译自定义软件
- 编译更新版本的指定包文件
- 使用自定义的补丁或者不同特性来重新编译已经存在的包


1. 编译自定义软件；
2. 便以特定版本的软件。

[raspi4固件和SDK](https://downloads.openwrt.org/snapshots/targets/bcm27xx/bcm2711/)(20200505更新SDK地址)
下载SDK，下载ss-libev,在menuconfig中可以选择ss-libev,按照步骤编译。安装到openwrt上。

## 刷入三方编译附件

刷入[自编译 OpenWrt R9.11.9 固件](https://mlapp.cn/369.html)后WiFi问题解决。但是无法上网。

连接WiFi时也无法进入后台管理系统。

安装 `luci-ss-withou-ipset,ss-libev` 。无法启动ss。可能是因为安装ss时缺少部分依赖。

无法获取ipv4/ipv6地址。

                IPv4 WAN 状态 未连接
                IPv6 WAN 状态 未连接

上级路由无法设置LAN和VLAN，故放弃。

## 自己编译的固件

更改WiFi设置：**频率：2.4G 模式：Legacy 信道：12(2467MHz)**，可以连接。方法来自 [自编译 OpenWrt R9.11.9 固件](https://mlapp.cn/369.html)。

**LAN设置**

                桥接eth0 和WLAN
                路由通告服务、DHCPv6 服务、NDP 代理全部选为**混合模式**。这样 WAN 6 和 LAN 就都可以获得公网 IPv6。
                DHCPv6 模式 选择 无状态的 + 有状态的

「中继模式」和「混合模式」的区别。使用「混合模式」的话，路由器也会获得一个 IPv6 地址，但是使用「中继模式」的话，路由器就没有了这个地址。

**WAN设置**

        添加wan接口和wan6接口配置，可以正常获取ip地址。
        wan和wan6选择br-lan，不勾选桥接。

max download=16Mbs，相应cpu=2%;max upload=20MBs,CPU=7%。速度合格，暂时使用。

## IPV6的三种配置方式

科技网或者教育网有原生的IPv6，其他方式例如VPN或者隧道等亦可以获得IPv6网络的访问。本文介绍了如何通过OpenWRT，让接入的终端也可以获得IPv6网络的访问。主要实现方式有以下几种：

1. IPv6 中继
2. IPv6 穿透
3. IPv6 NAT

另外一篇文章[ipv6to ipv4](https://tiandaochouqin1.github.io/ipv6toipv4)
链接[OpenWRT IPv6 三种配置方式](http://blog.kompaz.win/2017/02/22/OpenWRT%20IPv6%20%E9%85%8D%E7%BD%AE/)

# 总结

## 失败原因总结

1. 固件可能不支持ipv6，未编译相关包。
2. 固件不兼容或者Bug，开启的WiFi无信号，刷入[自编译 OpenWrt固件](https://mlapp.cn/369.html)后WiFi问题解决。目前[官方openwrt固件](https://openwrt.org/toh/raspberry_pi_foundation/raspberry_pi)只支持到raspi 3B+。

               因为博通闭源驱动的原因，树莓派 4B 的板载无线网卡只建议工作在 2.4Ghz 模式下，5Ghz 可能不太稳定。并且小苏不建议大家手动调整 4B 的无线网卡工作频率/模式/信道等参数(调整 SSID，无线密码等参数不受影响)，调整后可能会造成无线网卡不可用；
**频率：2.4G 模式：Legacy 信道：12(2467MHz)**
3. 无法更改K2的LAN/VLAN配置,未知原因。故旁路由两种方案均无法实施。

```C++
原因：Your JFFS2-partition seems full and overlayfs is mounted read-only. please try to remove files from /overlay/upper/... and reboot
```

`重刷固件后可以更改配置，采用更改DHCP和DNS的方案，但是配置完之后k2与pi均不能上网。原因可能是该方案针对ipv4,ipv6未正确配置。VLAN方案未尝试。`
1. 只有ipv6网络环境，可能配置上存在问题。
2. 善用搜索引擎。
关键词：bcm2711 openwrt
找到已编译好的[raspi4固件和SDK](https://downloads.openwrt.org/snapshots/targets/bcm27xx/bcm2711/)（**不带luci界面**）
3. 暂时使用pi作为路由器。
---

## PI的问题

1. 有时候会出现设备无法获取ipv4或者ipv6地址，无法上网。
2. 可能出现正常获取ip后download=100Kbs，upload正常。断开重连几次或者连接保持一段时间后可能正常。
3. 重启pi后WiFi无法上网，需要网线连接才能进入管理界面，并可能需要重启网络，后才能上网。
4. rtl8811芯片的CF-811AC USB网卡无法在openwrt上驱动。rtl8188eu芯片安装驱动后并未识别。

## 换回K2

K2网速可稳定4MBs，不会出现断网现象，满足基本需求。（K2超频580MHz->620MHz);

---


Raspi刷入openwrt镜像（已备份的和新下载的）无法进入后台。https://github.com/SuLingGG/OpenWrt-Rpi
[自编译 OpenWrt固件](https://mlapp.cn/369.html)可用，但不支持IPV6。

斐讯K2上传下载速度均有 16Mb/s。

不经过路由器使用ipv6代理上下行均为100Mb/s。


# Debian-Pi-Aarch64系统
高性能、集成[Debian-Pi-Aarch64 ★ 全新树莓派64位系统](https://github.com/openfans-community-offical/Debian-Pi-Aarch64)

## Raspberry系统连接wifi
```
vi /boot/wpa_supplicant.conf
```
```
country=CN
  ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
  update_config=1
  network={
  ssid="octoiot4dalian_third"
  psk="octoiot4lifedalian"
  key_mgmt=WPA-PSK
  priority=1
 }
```

重启，自动连接WiFi。
## 账户及密码

默认账户pi账户支持ssh登录，root账户密码请登陆后使用命令 “sudo passwd root” 执行设置，或使用命令 “sudo -i” 来切换到root用户。
**默认密码**
```
系统默认账户：pi ，默认密码：raspberry

web可视化管理 pi:raspberry
容器云管理 admin:password
ssh pi:raspberry 

```
## K2更换系统后Raspi无法连接WIFI

解决：K2的系统中5G与2.4G热点的命名反了`OP->OP5`。Raspi搜不到5G信号，更改**热点名**后解决。
ifconfig wlan0命令检查连接是否成功。inet addr字段中应该有一个IP地址。

[其他尝试](https://www.lxx1.com/4954)：
- 统一加密方式。
- 可登录ssh时：
```
sudo vi /etc/wpa_supplicant/wpa_supplicant.conf

```
- 重启wlan0以重新加载wpa_supplicant文件：
```
sudo ifdown wlan0
sudo ifup wlan0
或
sudo ifconfig wlan0 up
sudo ifconfig wlan0 down
```

# Ubuntu官方系统
ubuntu-20.04.1-preinstalled-server-arm64+raspi
## 无法连接wifi
- 按照[官方指南](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#3-wifi-or-ethernet)配置`network-config` 文件，无法连接WIFI。

```
wifis:
  wlan0:
  dhcp4: true
  optional: true
  access-points:
    "home network":
      password: "123456789"
```

- 使用diskgenius进入文件系统，修改`/etc/netplan/50-cloud-init.yaml`文件，也无法连接上wifi。

## 串口连接
![树莓派GPIO定义](../images/rasp_serial.png)

### 接线
串口线的VCC不接，GND接14号GND，RXD接8号TXD，TXD接10号RXD(RXD和TXD是交叉接的)，将usb那头插入电脑。

### 树莓派配置
将系统镜像写入SD卡后，进入boot盘，编辑config.txt文件，在文件最后添加`enable_uart=1`”`，然后保存。(支持UBUNTU/OPENFANS的系统)

### 终端连接设置
uart转usb线一般都是ch340芯片，终端可能需要安装ch340驱动。此处使用Mobaterm不需要额外安装驱动。

1. 打开设备管理器配置端口的波特率为115200；
2. 终端设置端口号和波特率，即可连接。


### netplan

串口连接进入系统，（已按照上面的方法修改了netplan文件）直接执行
```
sudo netplan apply
systemctl daemon-reload
```
WIFI连接成功。

原来无法连接的原因可能是权限不够导致修改的配置没有被应用。

## root用户
```
sudo vim /etc/ssh/sshd_config

PermitRootLogin 参数修改为yes即可；
```

然后设置密码
```
sudo passwd root
```

## Ubuntu Arm镜像源
ubuntu arm镜像源需要把原版`Ubuntu源`中的`ubuntu`替换为`ubuntu-ports`
```
sudo vim /etc/apt/sources.list
```

```
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse

```

# 内核移植
[自行移植树莓派64位内核系统的方法介绍](https://shumeipai.nxez.com/2017/11/07/build-ubuntu-64-bit-system-on-raspberry-pi-by-yourself.html)
[Kernel building - Official](https://www.raspberrypi.org/documentation/linux/kernel/building.md)


# 关闭指示灯
电源、状态指示灯

https://www.cnblogs.com/rootming/p/12245502.html

如果要每次开机生效, 可编辑/etc/rc.local文件



```

echo 0 | sudo tee  /sys/class/leds/led0/brightness
echo none | sudo tee  /sys/class/leds/led0/trigger
echo none | sudo tee  /sys/class/leds/led1/trigger
echo 0 | sudo tee /sys/class/leds/led1/brightness
```


# 香橙派OrangePi
1. http://www.orangepi.cn/html/hardWare/computerAndMicrocontrollers/service-and-support/Orange-Pi-Zero-2.html
2. [OrangePi_Zero2_H616_用户手册_v3.2](../files/OrangePi_Zero2_H616_用户手册_v3.2.pdf)


## 启动与登录
官方系统：默认 root 和 orangepi 用户的密码都为 orangepi 。64位的（`getconf LONG_BIT、file /bin/ls`）

串口连接：3个独立的针脚。tx和rx需和反接（和树莓派一样）。

网络连接：不能使用修改/etc/network/interfaces 方式

```
root@orangepi:~# nmcli dev wifi connect wifi_name password wifi_passwd
Device 'wlan0' successfully activated with 'cf937f88-ca1e-4411-bb50-61f402eef293'.

自动连接：
nmcli connection modify wifi_name connection.autoconnect yes

ls /etc/NetworkManager/system-connections/
```

## 内核编译
1. 内核代码(？) https://github.com/orangepi-xunlong/orangepi-build


## 查看cpu温度
```
 watch -n 0.1 echo CPU: $[$(cat /sys/class/thermal/thermal_zone0/temp)/1000]°
```
