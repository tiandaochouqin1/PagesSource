---
title: Linux服务器实用脚本集合
date: 2019-12-05 15:52:35
tags: VPS
categories: Linux
---
<font face="微软雅黑"> </font>
<center>常用的服务器工具安装</center>

<!-- more -->
- [常用一键安装脚本](#常用一键安装脚本)
  - [V2ray](#v2ray)
  - [BBR](#bbr)
  - [SS](#ss)
  - [KCPTUN](#kcptun)
- [管理](#管理)
  - [一键脚本](#一键脚本)
  - [宝塔](#宝塔)
  - [OneinStack](#oneinstack)
  - [LNMP](#lnmp)
  - [其它](#其它)
- [常用检测脚本汇总](#常用检测脚本汇总)
- [VPS速度测试工具](#vps速度测试工具)
  - [在线测试工具](#在线测试工具)
- [VPS性能测试工具](#vps性能测试工具)
  - [手动检测命令](#手动检测命令)
  - [VPS性能综合跑分工具 UNIXBENCH](#vps性能综合跑分工具-unixbench)
  - [一键脚本](#一键脚本-1)
- [VPS主机真伪检测](#vps主机真伪检测)
  - [总结](#总结)


# 常用一键安装脚本
## V2ray
脚本中的caddy会占用80端口，可能导致lnmp安装失败。
- [v2ray](https://github.com/233boy/v2ray/wiki/V2Ray%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85%E8%84%9A%E6%9C%AC)

```
bash <(curl -s -L https://git.io/v2ray.sh)

```
V2Ray 配置文件路径：`/etc/v2ray/config.json`
Caddy 配置文件路径：`/etc/caddy/Caddyfile`
脚本配置文件路径: `/etc/v2ray/233blog_v2ray_backup.conf`

caddy的域名伪装可改用nginx设置。
## BBR
- [秋水逸冰BBR](https://teddysun.com/489.html)
- [BBR合集](https://ssr.tools/1217)
  使用bbr暴力魔改版或锐速。

```
wget --no-check-certificate -O tcp.sh https://github.com/cx9208/Linux-NetSpeed/raw/master/tcp.sh && chmod +x tcp.sh && ./tcp.sh

```
## SS
Shadowsocks服务端有Python、libev、go版本。
- [Docker ss/ssr](https://teddysun.com/536.html)
- [ss-go](https://doubibackup.com/kd691l4o.html)
- [ss-go2](https://github.com/shadowsocks/go-shadowsocks2)

```
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubiBackup/doubi/master/ss-go.sh && chmod +x ss-go.sh && bash ss-go.sh

```

## KCPTUN
- [kcptun](http://www.chinagfw.org/2016/07/kcptun-shadowsocks.html)

```
wget https://github.com/xtaci/kcptun/releases/download/v20191127/kcptun-linux-amd64-20191127.tar.gz

```
kcptun对u2b加速效果明显。
使用方法：

1. [Linux服务端](https://github.com/xtaci/kcptun)需要手动设置为自启动服务。客户端bat命令启动或设置为系统服务。
2. 使用[shell脚本](http://requiemweb.top/blogs/2)安装Linux服务端。客户端使用windows[kcptun图形客户端](https://github.com/dfdragon/kcptun_gclient/releases)。


```
wget https://github.com/kuoruan/shell-scripts/raw/master/kcptun/kcptun.sh &&
chmod +x kcptun.sh && bash kuptun.sh
```


# 管理
**新机安装，否则会影响现有的环境。**
[国内外VPS主机管理面板和一键安装脚本](https://51.ruyo.net/5322.html)


## 一键脚本
国内版
http://lnmp.org/ （用的人很多，适应性很强）
https://oneinstack.com/ （用户也很多，博主一直使用这个）
http://teddysun.com/lamp （很好用的LAMP一键包） http://teddysun.com/lamp-yum （适合小内存≥64M）
http://bbs.aliyun.com/read/151729.html （阿里云论坛看到的）
http://blog.linuxeye.com/31.html  （多种配置，软件较新）
http://lnmpp.net/ （支持postgresql，支持ARM）
http://www.hhvmc.com/thread-17-1-1.html （有hhvm的一键包）
http://shuang.ca/llnmp/  http://llsmp.cn/（有LiteSpeed的一键包）
https://www.lxconfig.com/thread-69-1-1.html （有openresty的一键包）
http://blog.7qy.com/html/1575.html （有cherokee的一键包）
http://lamp.phpstudy.net/ （有Lighttpd的一键包）
http://www.upupw.net/ （Windows平台环境搭建）
https://www.appnode.com （免费版不支持面板）
http://www.ltmp.cc/ （LTMP支持CentOS/RadHat）
http://bet.xrbk.top/ （BET面板 支持CentOS）
国外版
http://centminmod.com/ （据说很适合wordpress）
https://vpssim.com/  （很强大的一键包）
http://tuxlite.com/ （适用于Debian系列）
https://github.com/Xeoncross/lowendscript  （lowendscript演变来的）
https://github.com/alexandreteles/monkeyServer（Monkey Web Server轻量级的web服务器）

## 宝塔
[宝塔](https://www.bt.cn/)
[海外版-不需要登录账号](https://www.aapanel.com/)
[开心版](https://www.duangvps.com/%E5%AE%9D%E5%A1%94%E6%9C%80%E6%96%B0%E7%A0%B4%E8%A7%A3%E7%89%88%EF%BC%81)
宝塔Linux面板是提升运维效率的服务器管理软件，支持一键LAMP/LNMP/集群/监控/网站/FTP/数据库/JAVA等100多项服务器管理功能。
有20个人的专业团队研发及维护，经过200多个版本的迭代，功能全，少出错且足够安全，已获得全球百万用户认可安装。面板


## OneinStack
https://github.com/oneinstack/oneinstack
https://oneinstack.com/auto/

```
wget -c http://mirrors.linuxeye.com/oneinstack-full.tar.gz && tar xzf oneinstack-full.tar.gz && ./oneinstack/install.sh --nginx_option 1 --php_option 7 --phpcache_option 1 --phpmyadmin  --db_option 2 --dbinstallmethod 1 --dbrootpwd oneinstack --pureftpd  --redis  --memcached  --reboot 
```

## LNMP

[LNMP一键安装包](https://lnmp.org/faq/v1-5-auto-install.html)和[无人值守安装命令](https://lnmp.org/auto.html)。

```
wget http://soft.vpser.net/lnmp/lnmp1.6.tar.gz -cO lnmp1.6.tar.gz && tar zxf lnmp1.6.tar.gz && cd lnmp1.6 && LNMP_Auto="y" DBSelect="2" DB_Root_Password="lnmp.org" InstallInnodb="y" PHPSelect="9" SelectMalloc="1" CheckMirror="n" ./install.sh lnmp
```

GCP f1机器上安装失败。`MySQL`安装失败，选择默认的`5.5`版本，并升级机器为`g1`后安装成功。


## 其它
- DirectAdmin
DirectAdmin 是一套国外开发的功能非常强劲的虚拟主机在线管理系统，通过这个管理系统您可以方便的管理您的服务器，设置EMAIL、设置DNS、开通FTP、在线文件管理、数据库管理等，方便管理员、客户及代理商在线操作虚拟主机信息。
https://www.directadmin.com/


- cPanel
对国际主机市场了解的朋友一定听说过cPanel，它是世界上功能强大，容易使用，因而比较受用户欢迎的虚拟主机控制系统。cPanel 是一套在网页寄存业中最享负盛名的商业软件，是基于于 Linux 和 BSD 系统及以 PHP 开发且性质为闭源软件；提供了足够强大和相当完整的主机管理功能，诸如：Webmail 及多种电邮协议、网页化 FTP 管理、SSH 连线、数据库管理系统、DNS 管理等远端网页式主机管理软件功能。
https://cpanel.net/


- Plesk
Plesk控制面板系列产品是Parallels公司（原名SWsoft）开发的专业主机管理软件，它丰富的工具套件能够帮助用户快速进行数据迁移，不但易于操作，而且可以最小化宕机时间。Plesk专业化的设计和综合的管理性能为主机专业人士提供了最全面和强大的功能，成为用户定制系统以及实现自助管理的完美解决方案。
https://www.plesk.com/

# 常用检测脚本汇总
转自[VPS主机性能和速度测试方法](https://wzfou.com/vps-ceping-gongju/#)
为方便使用，我在这里汇总一下用于VPS各类检测的脚本，有关脚本的详细使用及说明可参阅下文的内容
需要提醒的是，关于IO读写速度的测试，根据以往的经验，像谷歌云服务器、亚马逊服务器等，IO读写速度都比较低，而SSD在IO方面表现出色。另外，国外的VPS主机的速度很大程度上取决于线路的好坏，并且晚上和白天的测试速度会差别比较大。

1. Superspeed.sh
一键测试服务器到国内的速度脚本Superspeed.sh ：

```
wget https://raw.githubusercontent.com/oooldking/script/master/superspeed.sh
chmod +x superspeed.sh
./superspeed.sh

```

2. bench.sh
一键检测VPS的CPU、内存、负载、IO读写、机房带宽等脚本：bench.sh

```
#命令1：
wget -qO- bench.sh | bash
#或者
curl -Lso- bench.sh | bash

#命令2：
wget -qO- 86.re/bench.sh | bash
#或者
curl -so- 86.re/bench.sh | bash

#备注：
bench.sh 既是脚本名，同时又是域名。如果以上失效，请使用以下地址下载再执行脚本：
#下载地址：
https://github.com/teddysun/across/blob/master/bench.sh

```

3. SuperBench.sh
可以看作bench.sh强化版：SuperBench.sh
新增 Virt 检测服务器类型参数。常见 openvz，kvm，独服都能检测出来。同时整合上面的Superspeed.sh一键测试服务器到国内的速度脚本：

```
wget -qO- https://raw.githubusercontent.com/oooldking/script/master/superbench.sh | bash
#或者
curl -Lso- https://raw.githubusercontent.com/oooldking/script/master/superbench.sh | bash

```

4. Zench
Zench可以看作是Bench.sh 和 SuperBench的结合版本，加入 Ping 以及 路由测试 功能，会生成测评报告，可以很方便地分享给其他朋友看自己的测评数据 ：

```

wget -N --no-check-certificate https://raw.githubusercontent.com/FunctionClub/ZBench/master/ZBench-CN.sh && bash ZBench-CN.sh
#项目：https://github.com/FunctionClub/ZBench

```

5. speedtest-cli
一键带宽检测工具：speedtest-cli
安装命令：

```
sudo apt-get update
apt-get install python-pip
sudo pip install speedtest-cli
#CentOS
yum update
yum -y install epel-release
yum install python-pip
pip install speedtest-cli

```

使用方法：
```
speedtest-cli
#后面也可以接以下参数：
-h, --help show this help message and exit
--share 分享你的网速，该命令会在speedtest网站上生成网速测试结果的图片。
--simple Suppress verbose output, only show basic information
--list 根据距离显示speedtest.net的测试服务器列表。
--server=SERVER 指定列表中id的服务器来做测试。
--mini=MINI URL of the Speedtest Mini server
--source=SOURCE Source ip address to bind to
--version Show the version number and exit

```

6. unixbench
VPS性能综合跑分工具：unixbench
命令如下：
```
wget --no-check-certificate https://github.com/teddysun/across/raw/master/unixbench.sh
chmod +x unixbench.sh
./unixbench.sh

```

7. mPing
一键测试回程Ping值工具：mPing
```
        wget https://raw.githubusercontent.com/helloxz/mping/master/mping.sh
        bash mping.sh

```

8. Serverreview
Serverreview-benchmark综合评测工具
这是一个老外写的VPS主机综合评测工具，主要评测的项目有VPS主机磁盘IO、内存读写、CPU性能以及Benchmark性能，还有美国、欧洲、亚洲等不同节点的下载速度。[主页](https://github.com/sayem314/serverreview-benchmark)
脚本使用使用方法：
```
#简略版
yum install curl -y
curl -LsO git.io/bench.sh; chmod +x bench.sh && ./bench.sh -a share
#完整版
yum install curl -y
curl -LsO git.io/bench.sh; chmod +x bench.sh && ./bench.sh -a share

```

9. LemonBench
LemonBench 工具(别名LBench、柠檬Bench)，是一款针对Linux服务器设计的服务器性能测试工具。通过综合测试，可以快速评估服务器的综合性能，为使用者提供服务器硬件配置信息。
```

#脚本备用下载：https://www.ucblog.net/shell/LemonBench.sh

#快速测试
#如果你的服务器上安装有 curl 工具，请使用以下命令执行脚本：
curl -fsSL https://ilemonrain.com/download/shell/LemonBench.sh | bash -s fast

#如果你的服务器上安装有 wget 工具，请使用以下命令执行脚本：
wget -qO- https://ilemonrain.com/download/shell/LemonBench.sh | bash -s fast

#完整测试
#如果你的服务器上安装有 curl 工具，请使用以下命令执行脚本：
curl -fsSL https://ilemonrain.com/download/shell/LemonBench.sh | bash -s full

#如果你的服务器上安装有 wget 工具，请使用以下命令执行脚本：
wget -qO- https://ilemonrain.com/download/shell/LemonBench.sh | bash -s full

```
# VPS速度测试工具
## 在线测试工具
1. 在线测试工具，可以方便得到服务器的响应时间，这一招对于国外的VPS特别有效果。以下是搜集整理的实用在线网站速度测试工具网站：

```
http://ping.chinaz.com/
http://www.ipip.net/ping.php
https://www.17ce.com/
http://www.webkaka.com/
http://ce.cloud.360.cn/

```
这几个在线测速工具各有各的优缺点，推荐使用ipip.net测试服务器IP和路由追踪，用17ce.com测试网页加载速度，用ping.chinaz.com用国内不同地方的Ping值。
2. 本地测试软件。这里推荐使用WinMTR，这是一款方便易用的路由跟踪工具。该软件可以帮助用户直接查看各个节点的响应时间及丢包率，非常适合windows下客户做路由追踪及PING进行测试。

下载地址：https://www.ucblog.net/wzfou/WinMTR-CN-IP.zip
项目主页：https://github.com/oott123/WinMTR
带地图版：https://cdn.ipip.net/17mon/besttrace.exe

启用WinMTR，点击可以更新IP地址。
输入你想要追踪的域名或者服务器IP，接着你就可以看到数据包经过的节点还有丢包等情况，同时支持导出文本。
相关的参数说明如下：

        Hostname：到目的服务器要经过的每个主机IP或名称
        Nr：经过节点的数量；以上图百度为例子：一共要经过10个节点，其中第一个是出口的路由器
        Loss%：ping 数据包回复失败的百分比；藉此判断，那个节点（线路）出现故障，是服务器所在机房还是国际路由干路
        Sent：已传送的数据包数量
        Recv：成功接收的数据包数量
        Best：回应时间的最小值
        Avrg：平均回应时间
        Worst：回应时间的最大值
        Last：最后一个数据包的回应时间

# VPS性能测试工具

## 手动检测命令

1. CPU：`cat /proc/cpuinfo 或者 lscpu`。进入[CPU benchmark](http://www.cpubenchmark.net/cpu_list.php)，查看CPU的参数与性能。
2. 测试磁盘IO：
`dd if=/dev/zero of=test bs=64k count=4k oflag=dsync`
1. 测试VPS网络：
`wget http://cachefly.cachefly.net/100mb.test`


## VPS性能综合跑分工具 UNIXBENCH

UnixBench是一个类unix系（Unix，BSD，Linux）统下的性能测试工具，一个开源工具，被广泛用与测试linux系统主机的性能。Unixbench的主要测试项目有：系统调用、读写、进程、图形化测试、2D、3D、管道、运算、C库等系统基准性能提供测试数据。命令如下：

```
        wget --no-check-certificate https://github.com/teddysun/across/raw/master/unixbench.sh
        chmod +x unixbench.sh
        ./unixbench.sh

```

测试项目说明如下：

```
Dhrystone 2 using register variables
此项用于测试 string handling，因为没有浮点操作，所以深受软件和硬件设计（hardware and software design）、编译和链接（compiler and linker options）、代码优化（code optimazaton）、对内存的cache（cache memory）、等待状态（wait states）、整数数据类型（integer data types）的影响。

Double-Precision Whetstone
这一项测试浮点数操作的速度和效率。这一测试包括几个模块，每个模块都包括一组用于科学计算的操作。覆盖面很广的一系列 c 函数：sin，cos，sqrt，exp，log 被用于整数和浮点数的数学运算、数组访问、条件分支（conditional branch）和程序调用。此测试同时测试了整数和浮点数算术运算。

Execl Throughput
此测试考察每秒钟可以执行的 execl 系统调用的次数。 execl 系统调用是 exec 函数族的一员。它和其他一些与之相似的命令一样是 execve（） 函数的前端。

File copy
测试从一个文件向另外一个文件传输数据的速率。每次测试使用不同大小的缓冲区。这一针对文件 read、write、copy 操作的测试统计规定时间（默认是 10s）内的文件 read、write、copy 操作次数。

Pipe Throughput
管道（pipe）是进程间交流的最简单方式，这里的 Pipe throughtput 指的是一秒钟内一个进程可以向一个管道写 512 字节数据然后再读回的次数。需要注意的是，pipe throughtput 在实际编程中没有对应的真实存在。

Pipe-based Context Switching
这个测试两个进程（每秒钟）通过一个管道交换一个不断增长的整数的次数。这一点很向现实编程中的一些应用，这个测试程序首先创建一个子进程，再和这个子进程进行双向的管道传输。

Process Creation
测试每秒钟一个进程可以创建子进程然后收回子进程的次数（子进程一定立即退出）。process creation 的关注点是新进程进程控制块（process control block）的创建和内存分配，即一针见血地关注内存带宽。一般说来，这个测试被用于对操作系统进程创建这一系统调用的不同实现的比较。

System Call Overhead
测试进入和离开操作系统内核的代价，即一次系统调用的代价。它利用一个反复地调用 getpid 函数的小程序达到此目的。

Shell Scripts
测试一秒钟内一个进程可以并发地开始一个 shell 脚本的 n 个拷贝的次数，n 一般取值 1，2，4，8。（我在测试时取 1， 8）。这个脚本对一个数据文件进行一系列的变形操作（transformation）。

```

根据你的VPS性能不同，一般需要半个小时以上才会得到跑分结果，分数越高就表示性能越好。
## 一键脚本


一键测试VPS主机的基本配置、机房带宽、Ping值、IO性能、UnixBench跑分等，测试过程花费的时间比较长，需要耐心等待。
- 普通模式（测试机器配置， IO ，带宽和全国 ping 值）：

```
        wget -N --no-check-certificate https://raw.githubusercontent.com/91yun/91yuntest/master/test_91yun.sh && bash test_91yun.sh

```        
- 简单模式（测试机器配置， IO ，带宽和全国 ping 值）：

```
        wget -N --no-check-certificate https://raw.githubusercontent.com/91yun/91yuntest/master/test_91yun.sh && bash test_91yun.sh s

```
- 完全模式（测试机器配置， IO ，带宽、全国 ping 值、unixbench跑分）：
        
```
        wget -N --no-check-certificate https://raw.githubusercontent.com/91yun/91yuntest/master/test_91yun.sh && bash test_91yun.sh a

```
# VPS主机真伪检测

**检测VPS真实内存。**首先用命令查看真实的内存：free -m，接着切换至内存目录：cd /dev/shm，然后进行数据写入，标识 count=100 为写入100M，你可以修改为主机商标注的内存上限一点点：

        dd if=/dev/zero of=./memtest bs=1M count=100
        #注意完成后，执行删除：
        rm ./memtest
一旦出现错误：dd: error writing ‘./memtest’: No space left on device，就说明内存大小低于我们测试的数值，你可以继续降低数值，直到得到真实的内存。

**检测VPS虚拟技术。**命令如下：

        wget -N --no-check-certificate https://raw.githubusercontent.com/91yun/code/master/vm_check.sh && bash vm_check.sh
测试结果会显示是KVM、Xen还是OpenVZ。

## 总结
UnixBench性能跑分受版本影响较大。UnixBench目前有不同的版本，而网上不少的版本也是经过人工修改过的，可能测试的项目不同导致的结果也会不同。大家在测试时记得找一个参照对比。

IO读写速度受母机的影响比较大。有一些超售的服务器，由于用户众多，IO速度很慢，像这样的就要小心你的“邻居”了。使用一键脚本检测时，如果用在国内的VPS时，在网络测速中会出现卡死的情况。
