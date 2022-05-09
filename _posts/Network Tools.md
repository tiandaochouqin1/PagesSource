---
title: 网络环境测试命令与工具
date: 2019-11-29 22:14:26
tags: 
- [网络]
- [Linux]
categories: 网络
---
<font face="微软雅黑"> </font>

<!-- more -->
<!-- TOC -->

- [带宽测试](#带宽测试)
- [吞吐量测量](#吞吐量测量)
- [其他工具](#其他工具)

<!-- /TOC -->
***

[网络基本功](https://wizardforcel.gitbooks.io/network-basic/14.html) 
[网络基本功](https://www.bookstack.cn/read/network-basic/0.md) 

[op 10 Basic Network Troubleshooting Tools](https://www.pluralsight.com/blog/it-ops/network-troubleshooting-tools)

[Traceroute（路由追踪）的原理及实现](https://juejin.im/post/5a9c007a518825558001b05d)

## 带宽测试
1. ping
ping这一工具返回的时间，虽然通常被描述为传输延时，实际上是发送，传输，队列延时之和。上一节中，我们通过ping来粗略计算带宽。这一过程可通过如下方式改进：首先计算链路近端的路径行为，然后计算远端路径，然后用两者差异来估算链路带宽。
2. pathchar
将上述过程自动话完成的一个工具是pathchar。pathchar在路径的一端即能检测各链路的带宽。方法与之前描述的ping相类似，但是pathchar使用各种大小不一的报文。
3. bing
pathchar的一个替代工具是bing。pathchar估算的是一条路径上各链路的带宽，而bing用来测量点到点的带宽。通常，如果你不知道路径上的各条链路，需要首先执行traceroute命令。之后可以运行bing来指定链路的近端和远端。

## 吞吐量测量
1. ttcp
运行这一程序首先需要在远端设备运行server，通常用-r和-s选项。之后运行client，用-t和-s选项，以及主机名或地址。数据从client端发送至server端，测量性能之后，在各端返回结果，之后终止client端和server端。例如，server端如下所示：

2. netperf
该程序是由HP创造，该程序免费可用，运行于一些Unix平台，有支持文档，也被移植到Windows平台。虽然不像ttcp那样无处不在，但它的测试范围更加广泛。
与ttcp不同，客户端和服务器端是分开的程序。服务器端是netserver，能够单独启动，或通过inetd启动。客户端是netperf。

3. iperf
如果ttcp和netperf都不符合你的要求，那么可以考虑iperf。iperf也可以用于测试UDP带宽，丢失率，和抖动。Java前端让该工具便于使用。该工具同样移植入windows。

## 其他工具
1. treno使用的方法类似于traceroute来计算块容量，路径MTU，以及最小RTP。
2. 通过netstat进行流量测量:
    在理想的网络环境下，如果把overhead算在内，吞吐量是很接近于带宽的。但是吞吐量往往低于期望值，这种情况下，你会想要知道差异在哪。如之前所提到的，可能与硬件或软件相关。但通常是由于网络上其他数据流的影响。如果你无法确定原因，下一步就是查看你网络上的数据流。
    有三种基本方法可供采用。第一，最快的方法是使用如netstat这样的工具来查看链路行为。或通过抓包来查看数据流。最后，可使用基于SNMP的工具如ntop。

3. Tracert/traceroute

    Windows：`tracert`
    Linux：`traceroute`

4. Ipconfig/ifconfig

5. nslookup

6. putty/Tera term

7. Subnet and IP Calculator
Windows工具

8. Speedtest.net/pingtest.net

9. pathping/mtr

10. route