---
title: ipv6 Addr under Openwrt
date: 2019-12-05 14:07:44
tags: 路由器
categories: Linux
---
<font face="微软雅黑"> </font>
<center>设备如何通过路由器获得ipv6地址</center>

<!-- more -->

<!-- TOC -->

- [问题描述](#%e9%97%ae%e9%a2%98%e6%8f%8f%e8%bf%b0)
- [Openwrt设置](#openwrt%e8%ae%be%e7%bd%ae)

<!-- /TOC -->

# 问题描述

使路由器下的每台设备都分配到公网ipv6地址，便于远程访问。

# Openwrt设置
**方法一：**
1. DHCP->混合模式

**方法二：**
2. VLAN设置，将Raspi连接的LAN口设置到与WAN同一VLAN。使用USB连接手机，查看Raspi的 ipv6地址。
