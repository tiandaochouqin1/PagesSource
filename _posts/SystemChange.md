---
title: VPS自定义系统
tags:
  - Windows
  - Linux
  - 系统
  - VPS
categories: VPS
date: 2020-04-04 14:05:38
---
<font face="微软雅黑"> </font>
<center> Linux dd Windows方法与镜像制作</center>

<!-- more -->
`DD`可更换Linux、Windows等系统。

Windows7可使用老版本[Onedrive](https://cn.uptodown.com/windows)；设置`aria2`后可实现远程下载。


## DD Windows

[一些 Windows DD 镜像](https://moeking.me/2018/11/12/422/)

下载脚本：
```
wget --no-check-certificate -qO InstallNET.sh 'https://moeclub.org/attachment/LinuxShell/InstallNET.sh'

```
dd:
```
wget --no-check-certificate -qO InstallNET.sh 'https://moeclub.org/attachment/LinuxShell/InstallNET.sh' && chmod a+x InstallNET.sh && bash InstallNET.sh -dd '直链'

```
or

`wget -O- "直链" | gunzip | dd of=/dev/sda`

脚本+dd：
```
wget --no-check-certificate -qO InstallNET.sh 'https://moeclub.org/attachment/LinuxShell/InstallNET.sh' && bash InstallNET.sh -dd 'https://moeclub.org/onedrive/IMAGE/Windows/win7emb_x86.tar.gz'
```

1. DO一定要加IP参数/不加IP参数时重启需要输入参数，并且输入后会提示镜像损坏，无法继续。
2. 打开使用VNC，使用键盘操作再次配置ipv4信息，然后才能上网。
   
DNS：
8.8.8.8
8.8.8.4


bash InstallNET.sh --ip-addr 157.245.173.207 --ip-gate 157.245.160.1 --ip-mask 255.255.240.0 -dd 'https://moeclub.org/onedrive/IMAGE/Windows/win7emb_x86.tar.gz'


## Windows DD 镜像

Windows 7 Ultimate SP1 x86 精简

KVM虚拟化VPS适用
直链: https://share.moeking.me/Microsoft%20Windows/DD/Win7Ultx86Lite.gz
Username: Administrator
Password: Windows7x86-Chinese
老司机@hostloc制作

Windows 7 Ultimate SP1 x64 精简

KVM/Xen/Hyper-V虚拟化VPS适用
直链: https://share.moeking.me/Microsoft%20Windows/DD/Win7Ultx64Lite.gz
Username: Administrator
Password: www.nat.ee
老司机@hostloc制作

Windows 7 Ultimate SP1 x64 精简 带IIS

KVM/Xen/Hyper-V/VMware虚拟化VPS适用 集成万能驱动 2019-9-15版本
直链: https://share.moeking.me/Microsoft%20Windows/DD/Win7Ultx64Lite-IIS.gz
Username: Administrator
Password: www.nat.ee
碧莲(老司机)@hostloc制作
 
KVM/Xen虚拟化VPS适用 2019-9-21版本
直链: https://share.moeking.me/Microsoft%20Windows/DD/Win7Ultx64Lite-IIS-2.gz
Username: Administrator
Password: www.nat.ee
碧莲(老司机)@hostloc制作

Windows 7 Ultimate SP1 x64 精简 支持UEFI

KVM/Xen虚拟化VPS适用 支持Oracle机器
直链: https://share.moeking.me/Microsoft%20Windows/DD/Win7Ultx64Lite-UEFI.gz
Username: Administrator
Password: www.nat.ee
碧莲(老司机)@hostloc制作

Windows 7 Enterprise SP1 x64 精简
KVM/Xen虚拟化VPS适用
直链: https://share.moeking.me/Microsoft%20Windows/DD/Win7Entx64Lite.gz
Username: Administrator
Password: www.nat.ee
碧莲(老司机)@hostloc制作

Windows 7 Enterprise SP1 x64 精简 支持UEFI
KVM/Xen虚拟化VPS适用 支持Oracle机器
直链: https://share.moeking.me/Microsoft%20Windows/DD/Win7Entx64Lite-UEFI.gz
Username: Administrator
Password: www.nat.ee
碧莲(老司机)@hostloc制作


## 镜像制作

