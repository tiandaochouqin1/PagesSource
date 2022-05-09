---
title: Ping 命令
date: 2019-06-04 19:27:20
tags: 网络
categories: 网络
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->

***
# Ping
ping (Packet Internet Groper)，因特网包探索器，用于测试网络连接量的程序。

Ping发送一个ICMP 回声请求消息给目的地并报告是否收到所希望的ICMP echo （ICMP回声应答）。

ICMP协议通过IP协议发送的，IP协议是一种无连接的，不可靠的数据包协议。

ping命令通常用来作为网络可用性的检查。ping命令可以对一个网络地址发送测试数据包，看该网络地址是否有响应并统计响应时间，以此测试网络。

1. 使用ipconfig /all观察本地网络设置是否正确
2. Ping 127.0.0.1，127.0.0.1 回送地址Ping回送地址是为了检查本地的TCP/IP协议有没有设置
3. Ping本机IP地址，这样是为了检查本机的IP地址是否设置有误；
4. Ping本网网关或本网IP地址，这样的是为了检查硬件设备是否有问题，也可以检查本机与本地网络连接是否正常；（局域网中)
5. Ping远程IP地址，这主要是检查本网或本机与外部的连接是否正常。

```
用法: 

    ping [-t] [-a] [-n count] [-l size] [-f] [-i TTL] [-v TOS]

           [-r count] [-s count] [[-j host-list] | [-k host-list]]

           [-w timeout] [-R] [-S srcaddr] [-4] [-6] target_name

```

## psping
1. Microsoft网站下载[PsPing](https://docs.microsoft.com/en-us/sysinternals/downloads/psping)
2. Linux平台中类似的工具非常多，比如nping，nc 等。

### 基本使用
查看帮助: `psping -? [i|t|l|b]`

1. ICMP PING：直接`psping ip`
2. TCP PING:`psping ip:port`
3. TCP and UDP latency usage:`psping -l 1500 ip:port`，`-l`指定数据包大小。
4. TCP and UDP Bandwidth usage:`psping -b -l 1500 ip:port`,多一个`-b`参数。

### 常用参数


```
-h	Print histogram (default bucket count is 20).
-i	Interval in seconds. Specify 0 for fast ping.
-l	Request size. Append 'k' for kilobytes and 'm' for megabytes.
-n	Number of pings or append 's' to specify seconds e.g. '10s'.
-q	Don't output during pings.
-t	Ping until stopped with Ctrl+C and type Ctrl+Break for statistics.
-w	Warmup with the specified number of iterations (default is 1).
-4	Force using IPv4.
-6	Force using IPv6.

```

