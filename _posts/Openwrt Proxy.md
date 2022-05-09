---
title: Openwrt配置代理
date: 2019-11-15 14:50:18
tags: 路由器
categories: Linux
---
<font face="微软雅黑"> </font>
<center>Openwrt ss-libev介绍与使用</center>

<!-- more -->

<!-- TOC -->

- [shadowsocks-libev](#shadowsocks-libev)
- [#Transparent Proxy](#transparent-proxy)
    - [SOCKS5 Proxy](#socks5-proxy)
    - [Port Forward](#port-forward)
    - [Access Control](#access-control)
    - [Zone WAN](#zone-wan)
    - [Zone LAN](#zone-lan)
- [Pure DNS](#pure-dns)
    - [DNS cache poisoning](#dns-cache-poisoning)
    - [ChinaDNS](#chinadns)
    - [DNS bridge](#dns-bridge)
    - [dnsmasq](#dnsmasq)

<!-- /TOC -->

转自[OpenWrt Router](https://seanxp.com/2017/02/OpenWrt-Router/)

该方案提供一套 OpenWrt 完整的网络访问策略，使用 shadowsocks-libev 实现网络代理，dnsmasq + ChinaDNS 实现国内外代理分流与 DNS 防污染。

# shadowsocks-libev
[openwrt-shadowsocks](https://github.com/shadowsocks/openwrt-shadowsocks)，是一个专门为 OpenWrt 路由器定制过的 SS 软件包。

1. 可以使用SDK编译需要的包
2. 可以使用命令直接安装：

        opkg update
        opkg install shadowsocks-libev luci-app-shadowsocks ChinaDNS luci-app-chinadns --force-checksum
3. 可以直接下载编译过的ipk包
在 OpenWrt 中直接安装，例如K2，选择适配自己路由器芯片架构（可通过网络查询）的最新的 SS 软件包和相关依赖库。
`https://downloads.openwrt.org/snapshots/targets/ramips/mt7620/`//可下载SDK包`openwrt-imagebuilder-ramips-mt7620.Linux-x86_64.tar.xz`
此网页同时提供
[kmod](https://downloads.openwrt.org/snapshots/targets/ramips/mt7620/kmods/)
[packages](https://downloads.openwrt.org/snapshots/targets/ramips/mt7620/packages)

注意：下载他人的编译的软件包一定要谨慎，并确认可靠性。

另外可以下载 `luci-app-shadowsocks`，luci-app-shadowsocks 软件包是 shadowsocks-libev 的 LuCI 控制界面，方便用户控制和使用「透明代理」「SOCKS5 代理」「端口转发」功能。
下载完 ipk 软件包以后，scp/rsync 拷贝到 OpenWrt 并安装，安装时注意依赖关系，按顺序安装，如果出现错误可根据错误提示 Google 解决：

        opkg install libev*.ipk libmbedtls*.ipk libpcre*.ipk libsodium*.ipk
        opkg install shadowsocks-libev*.ipk luci-app-shadowsocks*.ipk
        configure shadowsocks-libev
登录 LuCI 后台（web 浏览器输入路由器地址），输入 root 密码，在菜单栏会看到新安装的 `Services - ShadowSocks`；
`Services -> ShadowSocks -> Servers Manage`，添加 SS 服务器信息（可购买或自行VPS搭建），可以同时添加多个服务器的配置信息。且服务器地址必须填入实际 IP 地址而非域名；
`Services -> ShadowSocks -> General Settings`，`Transparent Proxy`（透明代理）选择刚才添加的服务器，`SOCKS5 Proxy`（SOCKS5 代理）也选择刚才添加的服务器，`Port Forward` 不修改（依然是 Disable，具体原因会讲，将使用 dns-forwarder 代替）；
查看配置信息，确认无误后，点击 Save & Apply，路由器会自动启动 SS，且默认开机自启动；
局域网下，其他设备，关闭之前配置的代理信息，打开浏览器，「It just works!」；
这样配置，「It just works!」，但是不够优雅，因为这样是 SS 全局代理，虽然可以代理国外网站，但是国内网站的速度由于走代理会慢不少。因此下面使用 `ChinaDNS + DNS-Forwarder`实现国内外 IP 分流。

# #Transparent Proxy
**透明代理（Transparent Proxy）**：对应程序 `ss-redir`，是 shadowosocks 代理的基础服务，提供 TCP/UDP 透明代理。ss-redir 将客户端的原始数据封装成 shadowsocks 协议内容，转发（常用 ss-redir 作为 iptables REDIRECT 的代理服务器程序）给 shadowsocks server，实现透明转发。

**主服务器：**透明代理使用的默认服务器（TCP 透明代理、端口转发两个功能会使用这里选择的服务器），此处从列表中选择你需要使用的服务器。
**UDP服务器：**UDP 透明代理使用的服务器，可以和主服务器一致，也可以不同（也就是可以使用不同的服务器分别代理 TCP 和 UDP 连接）。在代理一些外服网络游戏的时候可能比较有用，其他多数情况下可以关闭。开启此项需要服务器支持，服务器端需要开启 UDP 功能。
**本地端口：**shadowsocks 服务需要占用一个路由器端口，推荐保留默认，但不能和其他运行的程序有冲突，也不能和下面「SOCKS5 代理」和「端口转发」中的本地端口冲突。
**ss-redir 与 ss-tunnel 的关系：**ss-redir已经支持udp了，还需要ss-tunnel吗？
## SOCKS5 Proxy
**SOCKS5 代理**，对应程序 `ss-local`，实现 shadowsocks 的 SOCKS5 代理功能，路由器作为 SOCKS5 代理服务器。默认端口 1080。
SOCKS 是一种网络传输协议，主要用于客户端与外网服务器之间通讯的中间传递。SOCKS 是 “SOCKetS” 的缩写。当防火墙后的客户端要访问外部的服务器时，就跟 SOCKS 代理服务器连接。这个代理服务器控制客户端访问外网的资格，允许的话，就将客户端的请求发往外部的服务器。这个协议最初由 David Koblas 开发，而后由NEC的 Ying-Da Lee 将其扩展到版本4。最新协议是版本5，与前一版本相比，增加支持 UDP、验证，以及 IPv6。
根据OSI模型，SOCKS是**会话层**的协议，位于表示层与传输层之间。（shadowsocks 只代理传输层数据 TCP/UDP，使用网路层 ICMP 协议的 ping 命令无法走 shadowsocks 代理，即使可以代理访问 google.com 也无法 ping 通）

## Port Forward
**Port Forward（端口转发）**，对应程序 ss-tunnel，通常用作转发 DNS。ss-tunnel 将监听 `5300` 端口（可修改），将监听到的数据包经过 SS 代理，转发给 `8.8.4.4:53`（可修改），因此可以通过此方法防止 DNS 污染。
在局域网下的其他电脑进行指定端口进行 DNS 解析，解析出来的 IP 可通过网络查询是否是真实IP。
`dig @192.168.1.1 www.youtube.com -p5300`
注意：由于性能问题（ss-tunnel 在使用混淆之后的udp通讯经常被阻断），本文方案中的端口转发改用 DNS-Forwarder，因此这里 Port Forward 设置为 disable。

## Access Control
**Access Control（访问控制）**，这里可以选择配置 SS 的代理范围。分外网（Zone WAN）与内网（Zone LAN）两部分。

## Zone WAN
**被忽略IP列表（Bypassed IP List）**，可以指定一份避免走代理的 IP 列表，例如构造列表 `/etc/shadowsocks/ignore.list`，里面枚举了国内的 IP 列表，可以避免访问国内网站走代理。（这种方法已不再使用，而是改为 ChinaDNS方案，IP 列表为 `/etc/chinadns_chnroute.txt`，原理类似）
创建国内IP段列表，用于忽略国内目标主机。防止走代理，可以将 IP 添加到 ignore.list 中。

        mkdir /etc/shadowsocks
        wget -O- 'https://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest' | awk -F\| '/CN\|ipv4/ { printf("%s/%d\n", $4, 32-log($5)/log(2)) }' > /etc/shadowsocks/ignore.list
**代理方法**（Access Control - Zone WAN - Proxy method）选择忽略列表（ignore list），并在 –custom– 中填入 `/etc/shadowsocks/ignore.list`。代理协议（Proxy protocol）选择 TCP+UDP。
注意：本文使用 ChinaDNS 方案，安装 ChinaDNS 后，这里的 ignore list 选择 ChinaDNS 即可。

**额外被忽略IP**（Bypassed IP）：这个列表中的 IP 强制绕过 shadowsocks 代理，如果有多组 shadowsocks 服务器的可以将所有服务器的 IP 填进去，如果是单服务器不用填；其他按需添加。
强制走代理IP（Forwarded IP）：这个列表中的 IP 强制走 shadowsocks 代理，填入 `8.8.8.8` 和 `8.8.4.4`，其他按需添加。

## Zone LAN
内网区域（Zone LAN），控制特定内网 IP 的代理方式。

网络接口：shadowsocks 只作用于勾选了的网络接口。默认 OpenWrt 只有一个LAN区域，此处也只会显示一个接口，也就是：**“桥接：br-lan”**；一般情况勾选它就行。当路由配置了多个LAN区域时候，则会显示多个。
代理类型：内网区域默认的代理方式。
正常代理：使用外网区域设置的方式。
直接连接：忽略外网区域的设置，不走代理。
全局代理：忽略外网区域的设置，强制走代理。
代理自身：路由设备自身的代理方式。
内网主机：单独配置内网特定IP的代理方式。

# Pure DNS
## DNS cache poisoning
只要安装好第一节的 `shadowsocks-libev`，就已经可以访问众多网站了。但是依然有几个问题：

**全局代理的弊端**
最简单的配置，直接添加 SS 服务器信息，并打开（Transparent Proxy）和（SOCKS5 Proxy），这样的路由器是 SS 全局代理。可以通过添加 ignore list 的办法禁止国内 IP 走代理。
1. **DNS 污染**，只要是 53 端口的 DNS 报文，都会面临 DNS 污染的情况。
    1. **直接污染**：使用国外的知名 DNS 服务器（8.8.8.8 / 8.8.4.4），会在出口被篡改 DNS 报文的 IP 地址（DNS 攻击）。
    2. **间接污染**：使用国内的知名 DNS 服务器（114.114.114.114 / 119.29.29.29），在访问国外网站时会上溯至国外根服务器访问，依然面临 DNS 报文中 IP 地址被篡改的问题，并且有投毒效果，一人访问，多人中毒。
2. **域名服务器缓存污染**（DNS cache poisoning），又称域名服务器缓存投毒（DNS cache poisoning），是指一些刻意制造或无意中制造出来的域名伺服器封包，把域名指往不正确的IP位址。一般来说，在互联网上都有可信赖的网域伺服器，但为减低网络上的流量压力，一般的域名服务器都会把从上游的域名服务器获得的解析记录暂存起来，待下次有其他机器要求解析域名时，可以立即提供服务。一旦有关网域的局域域名服务器的缓存受到污染，就会把网域内的电脑导引往错误的服务器或伺服器的网址。
对所有经过出口的 DNS 报文（UDP:53）进行 IDS 入侵检测，匹配到黑名单关键词时，立刻伪装目标域名的解析服务器返回虚假的查询结果。由于通常的域名查询没有任何认证机制，而且域名查询通常基于无连接不可靠的UDP协议，查询者只能接受最先到达的格式正确结果，并丢弃之后的结果。

想要检测目前的 DNS 是否被篡改，很简单的方法：

        dig www.youtube.com
        dig @119.29.29.29 www.youtube.com
        dig @114.114.114.114 www.youtube.com
解析到的 IP 地址，上网检索 IP 地址或 DNS 污染知名 IP 即可，网络上有收录注明污染 IP 地址列表。

## ChinaDNS
`openwrt-chinadns`
`luci-app-chinadns`
ChinaDNS 用于解决 DNS 污染问题，同时可加速国内网站的访问。
**其原理如下：**
提供至少一个国内 DNS 服务器和一个国外 DNS 服务器（通过设置 ChinaDNS 的上游服务器），ChinaDNS 收到来自用户的 DNS 请求后，同时向这两个服务器发 DNS 请求。如果从国内 DNS 服务器返回的解析结果为国外 IP，则选择国外 DNS 服务器的解析结果，否则选择国内 DNS 的解析结果（解析速度比国外解析快很多），最后返回给用户。
下载对应版本（路由器 CPU 架构）的 ChinaDNS*.ipk 与 luci-app-chinadns*.ipk，并生成国内的 IP 地址列表，用作绕 SS 代理。

        opkg install ChinaDNS*.ipk luci-app-chinadns*.ipk
        wget -O- 'https://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest' | awk -F\| '/CN\|ipv4/ { printf("%s/%d\n", $4, 32-log($5)/log(2)) }' > /etc/chinadns_chnroute.txt
安装完成后，登录路由器的 luci 管理页面 `Services -> ChinaDNS`，勾选 Enable（启用）和 `Enable Bidirectional Filter`（启用双向过滤），其他设置保持默认即可（也可修改本地端口）。
默认开启 `127.0.0.1:5353` 端口，监听 DNS 请求。国内路由表（CHNRoute File）默认位置 `/etc/chinadns_chnroute.txt`，上游服务器 `114.114.114.114,127.0.0.1:5300`。（可将国内 DNS 114.114.114.114 改成当前你所在网络的 ISP 所提供的 DNS 服务器 IP，以加快国内域名的解析速度）。
这里的国外 DNS 设置为 `127.0.0.1:5300`，是因为无法直接设置 8.8.8.8，否则会在网络出口被直接污染，需要通过走代理的方法避免污染。
走代理有两种方法：
1. ss-tunnel，即开启 ShadowSocks - General Settings - Port Forward，将默认监听端口 5300 的 DNS 请求，并转发到目的地（默认为 8.8.4.4:53），实现 DNS 代理。但是部分 ISP 下使用 ss-tunnel 不稳定，导致 ChinaDNS 无法正常工作，其根本原因是这类的 ISP 的 UDP 不稳定。
2. DNS-Forwarder，实现类似 ss-tunnel 的端口转发功能，但是使用 ss 代理并 TCP 协议，可靠的多。
DNS-Forwarder
openwrt-dns-forwarder
luci-app-dns-forwarder

安装 `dns-forwarder*.ipk` 和 `luci-app-dns-forwarder*.ipk`，录路由器的 luci 管理页面，Services -> DNS-Forwarder，点击 Enable，修改监听端口为 5300，DNS Server 为 8.8.8.8，然后点击 Save & Apply。

## DNS bridge
现在只是启动了 ChinaDNS 与 DNS-Forwarder，还没有把 **DNS 桥接**通顺（配置端口映射和转发关系）。

`luci - Network - Interfaces - WAN - Advanced Settings - Use DNS servers advertised by peer` 关闭，避免 WAN 接口连接外网时被上层路由器指定 DNS 服务器。
`luci - Network - DHCP and DNS - Server Settings - DNS forwardings`，设置为 `127.0.0.1#5353`，实现 端口 53 到本机端口 5353 的 DNS 转发，即将路由器接收到的 DNS 请求交给 ChinaDNS 处理。
`luci - Network - DHCP and DNS - Server Settings - Resolv and Hosts Files - Ignore resolve file`关闭，虽然禁止了 WAN 接口指定 DNS，但是本机的 `/etc/resolv.conf` 还在，DNS 将会查询其中的 nameservers，这里也关闭。
局域网内的其他上网设备，将预设的 DNS 删除，则连接 Wi-Fi 后默认 DNS 地址为路由器的 IP 地址，这样就将 DNS 解析交由路由器处理。
**DNS 线路解析：**

- 国外线路：`dnsmasq(:53) –(dnsmasq-china-list)–(DNS forwardings)–> ChinaDNS(:5353) –(CHNRoute File & Upstream Servers)–> ss-tunnel / dns-forwarder (:5300) –( Port Forward / DNS-Forwarder)–> ss server –>8.8.8.8:53`
- 国内线路：`dnsmasq(:53) –(dnsmasq-china-list)–(DNS forwardings)–> ChinaDNS(:5353) –(CHNRoute File & Upstream Servers)–> 114.114.114.114:53`

**dnsmasq(:53)**，路由器的默认 DNS 监听端口，监听局域网内的 DNS 请求，其他设备将 DNS 设置为路由器 IP 后，DNS 请求交由路由器处理；
**dnsmasq-china-list**，提供一份国内域名的加速列表，国内域名可以直接通过指定的 DNS 服务器解析，并由 dnsmasqn 提供长时间的缓存，提高命中率，没有在加速列表中的国内域名，还可能会再走 ChinaDNS 的加速，所以不用担心列表不足；
**DNS forwardings**，/etc/config/dhcp（Network - DHCP and DNS），配置 DNS 转发为 127.0.0.1#5353（ChinaDNS），则路由器会将 53 端口的 DNS 转发给 5353 端口的 ChinaDNS 处理；
**ChinaDNS(:5353) **的上游 DNS 服务器 配置为 114.114.114.114,127.0.0.1:5300，ChinaDNS 根据 CHNRoute File 确认 IP 的位置，国内默认走 114.114.114.114，国外默认走 127.0.0.1:5300；
**DNS-Forwarder(:5300)**监听 5300 端口，并通过转发到 8.8.8.8:53，且在 ShadowSocks - Access Control 已经明确配置 8.8.8.8 必须走代理，最终绕开污染源。
## dnsmasq

Dnsmasq 是一个开源的轻量级 DNS 转发和 DHCP、TFTP 服务器，使用C语言编写。Dnsmasq 针对家庭局域网等小型局域网设计，资源占用低，易于配置。
对于 OpenWrt 来说默认就安装了 dnsmasq，dnsmasq 监听了 53 端口，是系统的默认 DNS server。
Dnsmasq 提供 DNS 缓存和 DHCP 服务功能。作为域名解析服务器（DNS），dnsmasq 可以通过缓存 DNS 请求来提高对访问过的网址的连接速度。作为 DHCP 服务器，dnsmasq 可以用于为局域网电脑分配内网 IP 地址和提供路由。

编辑配置文件 `/etc/dnsmasq.conf`，在最后添加一行：`conf-dir=/etc/dnsmasq.d`
该配置让 dnsmasq 去加载 /etc/dnsmasq.d 目录下所有的配置。例如，生成一个 `/etc/dnsmasq.d/gfw.conf` 文件，把需要代理的域名代理给 ChinaDNS：

        /etc/dnsmasq.d/gfw.conf --- google
        server=/.google.com/127.0.0.1#5353
        server=/.googlecode.com/127.0.0.1#5353
        server=/.googleapis.com/127.0.0.1#5353
        server=/.gmail.com/127.0.0.1#5353
        server=/.youtube.com/127.0.0.1#5353
然后重启 dnsmasq 服务即可：

`/etc/init.d/dnsmasq restart`
因为需要代理的域名很多而且很繁琐，因此这里使用 dnsmasq-china-list 的代理配置：

        mkdir -p /etc/dnsmasq.d/ && mv /etc/dnsmasq.d/
        wget --no-check-certificate https://raw.githubusercontent.com/felixonmars/dnsmasq-china-list/master/accelerated-domains.china.conf
        wget --no-check-certificate https://raw.githubusercontent.com/felixonmars/dnsmasq-china-list/master/bogus-nxdomain.china.conf
        wget --no-check-certificate https://raw.githubusercontent.com/felixonmars/dnsmasq-china-list/master/google.china.conf
配置文件解析：

**accelerated-domains.china.conf**: Acceleratable Domains. The domain should have a better resolving speed or result when using a Chinese DNS server. 指定了国内的域名直接使用 114.114.114.114 即可，加速国内域名访问。
**bogus-nxdomain.china.conf**: Known addresses that are hijacking NXDOMAIN results returned by DNS servers. 一些常用的 DNS 劫持 IP。
**google.china.conf**: Acceleratable Google domains. 加速 Google 域名访问，使用 114 解析 Google 的 IP，只要部分可以，例如 youtube.com 即使 114 也可能是解析错误的。
