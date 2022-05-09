---
title: C语言和Python版的DRCOM —— 校园网drcom客户端破解
date: 2019-11-24 17:27:35
tags: 路由器
categories: Linux
---
<font face="微软雅黑"> </font>
<center>可分享热点的DRCOM客户端</center>

<!-- more -->
<!-- TOC -->

- [1 问题](#1-问题)
- [2 Python版Drcom](#2-python版drcom)
- [3 C语言版](#3-c语言版)
  - [openwrt步骤](#openwrt步骤)
  - [总结](#总结)

<!-- /TOC -->
***

# 1 问题
使用drcom校园网开启热点后，连接设备多余1个则会断网。

# 2 Python版Drcom
[CQU_drcom](https://github.com/purefkh/CQU_drcom)
[Drcom_generic](https://github.com/drcoms/drcom-generic)

在Window10上可以登录使用，**无法破解连接设备数量限制**，即WIFI热点只能连接一个设备，后续连接的设备会将已连接设备挤下。drcom不会掉线。
原因：设备连接数量限制应该在服务端，不依赖Dr.com客户端实现。故可推知Python版本的drcom的其它项目应当也无效，故在路由器上使用C语言版drcom版。

# 3 C语言版
[dogcom](https://github.com/mchome/dogcom)

按照教程[drcom openwrt路由器教程](https://www.right.com.cn/forum/thread-215978-1-1.html)，使用wireshark抓包，得到[drcom.conf](https://github.com/tiandaochouqin1/useful),与Python版的[drcom.py](https://github.com/tiandaochouqin1/useful/drcom.py)的配置相同，~~故应当也不能破解WiFi连接数量限制，不再在openwrt行进行尝试。~~其中主要包括DHCP、DNS服务器等配置。
内容如下：

```C++
server = '202.202.0.163'
username = '***'
password = '0000'
CONTROLCHECKSTATUS = '\x20'
ADAPTERNUM = '\x05'
host_ip = '202.202.10.77'
IPDOG = '\x01'
host_name = 'fuyumi'
PRIMARY_DNS = '202.202.0.33'
dhcp_server = '202.202.10.1'
AUTH_VERSION = '\x0a\x00'
mac = 0xb06ebf2e102b
host_os = 'Windows 10'
KEEP_ALIVE_VERSION = '\xd8\x02'
ror_version = False
```

## openwrt步骤

1. 抓包。使用wireshark抓取drcom认证包,然后使用[配置生成器](https://drcoms.github.io/drcom-generic/)得到正确的drcom.conf文件；
2. 下载已编译好的[dogcom.ipk](https://github.com/mchome/openwrt-dogcom/releases)，安装，上传配置文件drcom.conf，启动。
    `/usr/dogcom -m dhcp -c /usr/drcom.conf -v //verbose详细模式`
3. 添加启动项。
    `/usr/dogcom -m dhcp -c /usr/drcom.conf -e -d //external外部模式，daemon守护进程模式`

---

## 总结

1. 经破解后的老版本drcom客户端仍可正常开启热点；
2. Python版无法正常使用的原因可能是：
    1. 套用的配置不适合；
    2. 设备无线网卡或系统的问题。
---
20200620
使用c语言版DRCOM在路由器登录网络，wifi偶尔出现断流，网线直连未发现问题。原因未知，有可能是因连接设备数量超过限制而被下线。未深入分析原因（可根据log分析）。