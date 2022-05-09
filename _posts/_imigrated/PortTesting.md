---
title: 端口测试工具
date: 2019-11-30 22:53:11
tags: 
  - 网络
  - Linux

categories: 网络
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->

<!-- TOC -->

- [问题描述](#问题描述)
- [测试目标是否在监听端口](#测试目标是否在监听端口)
  - [使用telnet判断](#使用telnet判断)
  - [使用ssh判断](#使用ssh判断)
  - [使用wget判断](#使用wget判断)
  - [专用工具tcping](#专用工具tcping)
  - [netstat扫描监听端口](#netstat扫描监听端口)
  - [Nmap网络扫描和嗅探](#nmap网络扫描和嗅探)
    - [扫描tcp端口](#扫描tcp端口)
    - [nmap端口状态解析](#nmap端口状态解析)
    - [其它功能](#其它功能)
- [总结](#总结)

<!-- /TOC -->
# 问题描述

实现[外网访问Openwrt路由器管理Web及SSH](https://blog.csdn.net/weixin_43100629/article/details/82896163)。
结果：
设置K2端口转发，无法访问。

# 测试目标是否在监听端口

[如何测试端口通不通(四种方法）](https://blog.csdn.net/swazer_z/article/details/64442730)

针对Linux系统：有1、2、3、4四种方法;针对Windows系统：有2、5两种通用方法

一般情况下使用"telnet ip port"判断端口通不通。

**准备环境**

启动一个web服务器，提供端口.

        [wyq@localhost ~]$ python -m SimpleHTTPServer 8080
        Serving HTTP on 0.0.0.0 port 8080 ...
用其它web服务器提供端口也一样，由于python比较方便，这里就用它

## 使用telnet判断

telnet是windows标准服务，可以直接用；如果是linux机器，需要安装telnet.

**用法: telnet ip port**

1）先用telnet连接不存在的端口

        [root@localhost ~]# telnet 10.0.250.3 80
        Trying 10.0.250.3...
        telnet: connect to address 10.0.250.3: Connection refused #直接提示连接被拒绝

2）再连接存在的端口

        [root@localhost ~]# telnet localhost 22
        Trying ::1...
        Connected to localhost. #看到Connected就连接成功了
        Escape character is '^]'.
        SSH-2.0-OpenSSH_5.3
        a
        Protocol mismatch.
        Connection closed by foreign host.

## 使用ssh判断

ssh是linux的标准配置并且最常用

**用法: ssh -v -p port username@ip**

- -v 调试模式(会打印日志).
- -p 指定端口
- username可以随意

1）连接不存在端口

        [root@localhost ~]# ssh 10.0.250.3 -p 80
        ssh: connect to host 10.0.250.3 port 80: Connection refused
        [root@localhost ~]# ssh 10.0.250.3 -p 80 -v
        OpenSSH_5.3p1, OpenSSL 1.0.1e-fips 11 Feb 2013
        debug1: Reading configuration data /etc/ssh/ssh_config
        debug1: Applying options for *
        debug1: Connecting to 10.0.250.3 [10.0.250.3] port 80.
        debug1: connect to address 10.0.250.3 port 80: Connection refused
        ssh: connect to host 10.0.250.3 port 80: Connection refused
2）连接存在的端口

[root@localhost ~]# ssh ... -p

        [root@localhost ~]# ssh ... -p -v
        OpenSSH_.p, OpenSSL ..e-fips Feb
        debug: Reading configuration data /etc/ssh/ssh_config
        debug: Applying options for *
        debug: Connecting to ... [...] port .
        debug: Connection established.
        debug: permanently_set_uid: /
        debug: identity file /root/.ssh/identity type -
        debug: identity file /root/.ssh/identity-cert type -
        debug: identity file /root/.ssh/id_rsa type -
        debug: identity file /root/.ssh/id_rsa-cert type -
        debug: identity file /root/.ssh/id_dsa type -
        debug: identity file /root/.ssh/id_dsa-cert type -
        a
        ^C


## 使用wget判断

wget是linux下的下载工具，需要先安装.
用法: `wget ip:port`

1）连接不存在的端口

        [root@localhost ~]# wget ...:
        ---- ::-- http://.../
        Connecting to ...:... failed: Connection refused.
2）连接存在的端口

        [root@localhost ~]# wget ...:
        ---- ::-- http://...:/
        Connecting to ...:... connected.
        HTTP request sent, awaiting response...

## 专用工具tcping

[下载软件地址](https://elifulkerson.com/projects/tcping.php)，如果无法下载可以从本人资源中下载

## netstat扫描监听端口
netstat参数解释：

        -l  (listen) 仅列出 Listen (监听) 的服务
        -t  (tcp) 仅显示tcp相关内容
        -n (numeric) 直接显示ip地址以及端口，不解析为服务名或者主机名
        -p (pid) 显示出socket所属的进程PID 以及进程名字
        --inet 显示ipv4相关协议的监听

## Nmap网络扫描和嗅探
1. [nmap命令-基础用法](https://www.cnblogs.com/nmap/p/6232207.html)
2. [Nmap](https://nmap.org/download.html)是一款网络扫描和主机检测的非常有用的工具，可以用来作为一个漏洞探测器或安全扫描器。适用于winodws,linux,mac等操作系统

**功能**
1. 是扫描主机端口，嗅探所提供的网络服务
2. 是探测一组主机是否在线
3. 还可以推断主机所用的操作系统，到达主机经过的路由，系统已开放端口的软件版本

### 扫描tcp端口
B机器上使用nmap扫描A机器所有端口（-p后面也可以跟空格）

下面表示扫描A机器的1到65535所有在监听的tcp端口。

`nmap 10.0.1.161  -p1-65535`

指定端口范围使用-p参数，如果不指定要扫描的端口，Nmap默认扫描从1到1024再加上nmap-services列出的端口

nmap-services是一个包含大约2200个著名的服务的数据库，Nmap通过查询该数据库可以报告那些端口可能对应于什么服务器，但不一定正确。

### nmap端口状态解析

| 状态 | 解析 |
|---|---|
|**open**| 应用程序在该端口接收 TCP 连接或者 UDP 报文。 |
|**closed** |关闭的端口对于nmap也是可访问的， 它接收nmap探测报文并作出响应。但没有应用程序在其上监听。|
|**filtered** |于包过滤阻止探测报文到达端口，nmap无法确定该端口是否开放。过滤可能来自专业的防火墙设备，路由规则 或者主机上的软件防火墙。|
|**unfiltered**|未被过滤状态意味着端口可访问，但是nmap无法确定它是开放还是关闭。 只有用于映射防火墙规则集的 ACK 扫描才会把端口分类到这个状态。|
|**open/filtered**|无法确定端口是开放还是被过滤， 开放的端口不响应就是一个例子。没有响应也可能意味着报文过滤器丢弃了探测报文或者它引发的任何反应。UDP，IP协议,FIN, Null 等扫描会引起。|
|**closed/filtered**|（关闭或者被过滤的）：无法确定端口是关闭的还是被过滤的|

### 其它功能

1. 扫描一个IP的多个端口：连续的端口可以使用横线`-`连起来，端口之间可以使用`,`逗号隔开；
2. 扫描多个IP：中间用空格分开；
3. 扫描连续的ip地址：横线连接；
4. 扫描一个子网网段所有IP：`nmap  10.0.3.0/24`；
5. 扫描文件里的IP：`nmap -iL ip.txt`；
6. 扫描地址段时排除某个IP地址:`nmap 10.0.1.161-162  --exclude 10.0.1.162`。

# 总结

提供端口服务，则使用了tcp协议，上面是以web服务器为例。

工具的共同点是：1.以tcp协议为基础；2.能访问指定端口. 遵循这两点可以找到很多工具.

windows下使用tcping比较方便，linux下可以用wget.
