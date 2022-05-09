---
title: Ngnix 与 CDN 
date: 2020-03-22 12:55:40
tags: 网络
categories: 网络
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->
- [CDN](#cdn)
  - [动态与静态内容](#动态与静态内容)
    - [静态](#静态)
    - [动态](#动态)
    - [事件驱动](#事件驱动)
  - [CDN概念](#cdn概念)
    - [CDN常见多级缓存](#cdn常见多级缓存)
    - [CDN工作方法](#cdn工作方法)
    - [CDN层级划分](#cdn层级划分)
    - [CDN缓存](#cdn缓存)
      - [CDN缓存策略](#cdn缓存策略)
      - [CDN缓存刷新](#cdn缓存刷新)
  - [NodeCache CDN](#nodecache-cdn)
- [Nginx多端口和反代](#nginx多端口和反代)
  - [LNMP](#lnmp)
  - [Ngnix](#ngnix)
  - [nginx 配置](#nginx-配置)
    - [基本结构](#基本结构)
    - [配置文件的语法规则](#配置文件的语法规则)
  - [配置多个server](#配置多个server)
  - [反代网站](#反代网站)
  - [同端口多域名](#同端口多域名)
- [反向代理其它网站](#反向代理其它网站)
  - [caddy实现](#caddy实现)
  - [Google镜像](#google镜像)
  - [ngx_http_google_filter_module](#ngx_http_google_filter_module)
- [Nginx Log分析](#nginx-log分析)
  - [Nginx日志配置](#nginx日志配置)
  - [log_format 自定义日志格式](#log_format-自定义日志格式)
  - [访问日志实例](#访问日志实例)
  - [常用命令](#常用命令)
  - [error_log](#error_log)
- [泛域名证书wildcardssh](#泛域名证书wildcardssh)
  - [acme.sh](#acmesh)
  - [安装 acme.sh](#安装-acmesh)
  - [验证域名并生成证书](#验证域名并生成证书)
    - [HTTP文件验证：](#http文件验证)
    - [DNS-API验证](#dns-api验证)
    - [部署nginx](#部署nginx)
  - [失效](#失效)


查看使用的配置文件路径：`/usr/local/opt/nginx/bin/nginx -t`


腾讯lighthouse-Centos 7:
nginx路径：`/usr/local/lighthouse/softwares/btpanel/server/nginx/conf/nginx.conf`
     `include /www/server/panel/vhost/nginx/*.conf;`



# CDN
## 动态与静态内容
### 静态
静态资源CDN加速已经基本上全覆盖。
### 动态
动态加速的对象是动态生成的网页，动态加速一般是对针对内容（如数据库信息等）在用户与- 源站之间建立高速通道，通过路由优化、TCP加速等技术手段对动态内容进行加速，降低节点到源站之间的时延，从而大大降低了用户访问动态网页的延迟。
使用到的就是CDN的快速传输的能力。其实也就是`DSA(Dynamic Site Acceleration)`。

传统的DSA有:

- TCP 优化：设计算法来处理网络拥堵和包丢失，加快这些情况下的数据从cdn的恢复以及一些常见的TCP瓶颈
- Route optimization：就是优化从源到用户端的请求的线路，以及可靠性，就是不断的测量计算得到更快更可靠的路线
- Connection management：就是边缘和源之间，包括CDN之前的线路，采用长连接，而不是每一个请求一个连接
- On-the-fly compression：就是数据在刚刚离开源的时候就进行压缩，可以缩短在整个网络之中的流通时间
- SSL offload：加速或者说减少一些安全监测，减少原服务器执行这种计算密集型的压力
- Pre-fetching：有的服务可以解析HTML文件，并将原始服务器预取缓存对象嵌入到文件中

### 事件驱动
很多CDN把它定义为动态的(这部分内容很难被缓存)。它实际上是静态的，只是更新时间不可预期，没法提前决定他的生命周期。


## CDN概念

回源访问：访问源站。
边缘访问：访问CDN缓存。
NodeCache后台可以看到每次访问都存在两种访问。

国外免费CDN：Cloudflare、gcorelabs（验证信用卡/Paypal）。
国内免费：七牛云、又拍云。

[这就是CDN回源原理和CDN多级缓存](https://cloud.tencent.com/developer/article/1439913)

### CDN常见多级缓存
CDN的全称是**Content Delivery Network**，即内容分发网络。其基本思路是尽可能避开互联网上有可能影响数据传输速度和稳定性的瓶颈和环节，使内容传输的更快、更稳定。通过在网络各处放置节点服务器所构成的在现有的互联网基础之上的一层智能虚拟网络，CDN系统能够实时地根据网络流量和各节点的连接、负载状况以及到用户的距离和响应时间等综合信息将用户的请求重新导向离用户最近的服务节点上。其目的是使用户可就近取得所需内容，解决 Internet网络拥挤的状况，提高用户访问网站的响应速度。

### CDN工作方法
客户端浏览器先检查是否有本地缓存是否过期，如果过期，则向CDN边缘节点发起请求，CDN边缘节点会检测用户请求数据的缓存是否过期，如果没有过期，则直接响应用户请求，此时一个完成http请求结束；如果数据已经过期，那么CDN还需要向源站发出回源请求（back to the source request）,来拉取最新的数据。

### CDN层级划分
- **边缘层**：直接面向用户，负责给用户提供内容服务的的Cache设备都部署在整个 CDN网络的边缘位置。
- **中心层**：负责全局的管理和控制，同时也保存了最多的内容Cache。在边缘层设备未能命中Cache时，需要向中心层设备请求；而中心层未能命中时，则需要向源站请求。不同的CDN系统设计存在差异，中心层可能具备用户服务的能力，也可能只会向下一层提供服务。
- 如果CDN系统比较庞大，边缘层向中心层请求内容太多，会造成中心层负载压力太大。此时，需要在中心层和边缘层之间部署一个**区域层**，负责一个区域的管理和控制，也可以提供一些内容Cache供边缘层访问。

### CDN缓存
浏览器本地缓存失效后，浏览器会向CDN边缘节点发起请求。类似浏览器缓存，CDN边缘节点也存在着一套缓存机制。

**CDN缓存的缺点**
CDN的分流作用不仅减少了用户的访问延时，也减少的源站的负载。但其缺点也很明显：当网站更新时，如果CDN节点上数据没有及时更新，即便用户再浏览器使用Ctrl +F5的方式使浏览器端的缓存失效，也会因为CDN边缘节点没有同步最新数据而导致用户访问异常。

#### CDN缓存策略

- CDN边缘节点缓存策略因服务商不同而不同，但一般都会遵循http标准协议，通过http响应头中的`Cache-control: max-age`的字段来设置CDN边缘节点数据缓存时间。
- 当客户端向CDN节点请求数据时，CDN节点会判断缓存数据是否`过期`，若缓存数据并没有过期，则直接将缓存数据返回给客户端；否则，CDN节点就会向源站发出回源请求，从源站拉取最新数据，更新本地缓存，并将最新数据返回给客户端。
- CDN服务商一般会提供基于文件后缀、目录多个维度来`指定CDN缓存时间`，为用户提供更精细化的缓存管理。
CDN缓存时间会对“回源率”产生直接的影响。若CDN缓存时间较短，CDN边缘节点上的数据会经常失效，导致频繁回源，增加了源站的负载，同时也增大的访问延时；若CDN缓存时间太长，会带来数据更新时间慢的问题。开发者需要增对特定的业务，来做特定的数据缓存时间管理。
#### CDN缓存刷新
CDN边缘节点对开发者是透明的，相比于浏览器Ctrl+F5的强制刷新来使浏览器本地缓存失效，开发者可以通过CDN服务商提供的“刷新缓存”接口来达到清理CDN边缘节点缓存的目的。这样开发者在更新数据后，可以使用“刷新缓存”功能来强制CDN节点上的数据缓存过期，保证客户端在访问时，拉取到最新的数据。


## NodeCache CDN
[NodeCache](https://console.nodecache.com/)赠送流量包：大陆-15天/100G；北美,亚太-30天/500G。邮箱直接注册，不需要验证手机。


**更改回源HOST**

1. 按照`IP:port`配置CDN后,可使用域名访问非标端口。访问`80`端口时显示未备案。
2. 再将`IP:80`的回源HOST改为`IP:port`后可直接访问；地址栏会直接跳转为`IP`地址。
3. 将`IP:80`的回源HOST改为`非本服务器IP:port`后，可访问，地址栏为域名。

查看Nginx access.log:
- 未配置回源HOST为`IP:port`时，访问非标端口可看到域名（如上一节所示）；无访问`80`端口的记录。
- 配置回源HOST为`本服务器IP:port`后，`80`端口的log中显示为`本机IP:port`。
- 配置回源HOST为`非本服务器IP:port`后，`80`端口的log中显示为`本机IP:port`，未出现配置中的IP。



# Nginx多端口和反代
## LNMP
一键安装包。安装的[软件目录](https://lnmp.org/faq/lnmp-software-list.html)在`/usr/local/`下。

Nginx主配置(默认虚拟主机)文件路径：`/usr/local/nginx/conf/nginx.conf`
添加的虚拟主机配置文件路径：`/usr/local/nginx/conf/vhost/域名.conf`。被主配置include。单文件或多文件配置均可。

## Ngnix
重启失败时用`lnmp restart`
[Nginx配置多端口多域名访问](https://cloud.tencent.com/developer/article/1561901)
[如何使用Nginx为Linux实例绑定多个域名](https://help.aliyun.com/knowledge_detail/41467.html)

先在不同端口建立服务，同一从80端口入站，再在nginx中配置按照域名`转发`到不同端口。
需要在CDN中分别配置服务器IP的`回源host`为Nginx中相对应的具体的域名才能访问到对应站点。
[配置回源HOST](https://help.aliyun.com/document_detail/27131.html)



## nginx 配置
[Nginx 最全操作总结](https://mp.weixin.qq.com/s/LmtHTOVOvdcnMBuxv7a9_A)

### 基本结构
```
main        # 全局配置，对全局生效
├── events  # 配置影响 nginx 服务器或与用户的网络连接
├── http    # 配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置
│   ├── upstream # 配置后端服务器具体地址，负载均衡配置不可或缺的部分
│   ├── server   # 配置虚拟主机的相关参数，一个 http 块中可以有多个 server 块
│   ├── server
│   │   ├── location  # server 块可以包含多个 location 块，location 指令用于匹配 uri
│   │   ├── location
│   │   └── ...
│   └── ...
└── ...


```

### 配置文件的语法规则

1. 配置文件由指令与指令块构成
2. 每条指令以 “;” 分号结尾，指令与参数间以空格符号分隔
3. 指令块以 {} 大括号将多条指令组织在一起
4. include 语句允许组合多个配置文件以提升可维护性
5. 通过 # 符号添加注释，提高可读性
6. 通过 $ 符号使用变量
7. 部分指令的参数支持正则表达式，例如常用的 location 指令

## 配置多个server
主配置文件中`nginx.conf`。
端口不可重复

```C++
//  每个端口可一个server。
server
    {
        listen 90;
        #listen [::]:80 default_server ipv6only=on;
        server_name _;
        index index.html index.htm index.php bing.php;
        root  /home/wwwroot/bing/bing;
        include enable-php.conf;
        access_log  /home/wwwlogs/bing-access.log;
    }
server
    {
        listen 91;
        server_name _;
        index index.html index.htm index.php NASA.php;
        root  /home/wwwroot/bing/nasa;
        include enable-php.conf;
        access_log  /home/wwwlogs/nasa-access.log;
    }
```

## 反代网站
https://www.cnblogs.com/fanzhidongyzby/p/5194895.html
辅助配置文件中`domainname.conf`。

```C
//应用配置，这个应用不是运行在80端口上的。并且可以是另外一个服务器
//使用属性proxy_pass设置
server {
    listen       888;
    server_name  blog.***.com;
    location / {
            proxy_pass http://127.0.0.1:2368;
    }
    access_log  /alidata/log/nginx/access/blog.log;
}
```

## 同端口多域名

```C
server
    {
    listen   80;                            #监听端口设为 80。
    server_name  msn.server111.com;         #绑定您的域名。
    index index.htm index.html index.php;   #指定默认文件。
    root /home/www/msn.server110.com;      #指定网站根目录。
    include location.conf;             #当您需要调用其他配置文件时才粘贴此项，如无需要，请删除此项。
    }
```

重复使用同一个listen端口时，不能加其他参数如`default_server\reuseport`。

**php探针失效**

复制多个default目录使用时，部分p.php探针会失效。
表现为：访问探针会直接下载`p.php`。



# 反向代理其它网站

https://doubibackup.com/l-en8vwt-3.html

可添加网站登录认证。

另外，大量VPS的ip已被scholar.google.com[拉黑](https://blog.plusls.com/linux/nginx/configure-nginx-google-mirror/)，可配置upstream来绕过。

当你要镜像的网站不开放 HTTP或者强制HTTPS 的时候，你就需要加上 SSL 来转成 HTTPS 了。

## caddy实现

```
echo ":888 {
gzip
proxy / http://www.google.com
}" > /usr/local/caddy/Caddyfile
```

## Google镜像

[nginx代理搭建google镜像站](https://www.chromeba.net/nginx%E5%8F%8D%E5%90%91%E4%BB%A3%E7%90%86%E6%90%AD%E5%BB%BAgoogle%E9%95%9C%E5%83%8F%E7%BD%91%E7%AB%99.html)
[参考2](https://stdrc.cc/post/2018/10/23/reverse-proxy-of-google/)

新增nginx配置文件（以g.sudo.gq域名为例）

```
vi /etc/local/nginx/conf/vhost/g.sudo.gq.conf

```

```
server {
    listen       80;
    server_name  g.sudo.gq;#这里改成你自己的域名

    charset utf-8;

    location / {
        proxy_redirect off;
        proxy_cookie_domain google.com <google.domain>;
        proxy_pass https://www.google.com;
        proxy_connect_timeout 60s;
        proxy_read_timeout 5400s;
        proxy_send_timeout 5400s;

        proxy_set_header Host "www.google.com";
        proxy_set_header User-Agent $http_user_agent;
        proxy_set_header Referer https://www.google.com;
        proxy_set_header Accept-Encoding "";
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto https;
        proxy_set_header Accept-Language "zh-CN";
        proxy_set_header Cookie "PREF=ID=047808f19f6de346:U=0f62f33dd8549d11:FF=2:LD=en-US:NW=1:TM=1325338577:LM=1332142444:GM=1:SG=2:S=rE0SyJh2W1IQ-Maw";
        }
    }

```

重新加载nginx配置文件
```
nginx -s reload

```

大功告成.

如果你想进一步了解怎么在google镜像站中插入自己的广告，请阅读：nginx字符串替换模块`ngx_http_substitutions_filter_module`介绍

## ngx_http_google_filter_module
一键安装的项目：[ngx_http_google_filter_module](https://github.com/cuber/ngx_http_google_filter_module) is a filter module which makes google mirror much easier to deploy

[使用 Nginx 反向代理 Google 和 Wikipedia](https://stdrc.cc/post/2018/10/23/reverse-proxy-of-google/)
需要 Nginx 安装有 ngx_http_substitutions_filter_module 模块，因此如果之前安装的 Nginx 在编译时没有添加这个模块，是需要重新编译的。


# Nginx Log分析


Nginx (engine x) 作为一个高性能的 HTTP 和反向代理服务，也是一个 IMAP/POP3/SMTP 服务

[Nginx 日志配置详解](https://segmentfault.com/a/1190000013377493)

## Nginx日志配置
日志到了一定大小就会被压缩改名.
Nginx 预定义了名为[combined](https://nginx.org/en/docs/http/ngx_http_log_module.html#log_format)日志格式：

```
log_format combined '$remote_addr - $remote_user [$time_local] '
                    '"$request" $status $body_bytes_sent '
                    '"$http_referer" "$http_user_agent"';

```

各字段含义如下：

字段 | 含义
---|---
remote_addr | 客户端地址
remote_user | 客户端用户名
time_local | 服务器时间
request | 请求内容，包括方法名、地址和http协议
http_host | 用户请求时使用的http地址
status | 返回的http状态码
request_length | 请求大小
body_bytes_sent | 返回的大小
http_referer | 来源页
http_user_agent | 客户端名称
request_time | 整体请求延时
upstream_response_time | 上游服务的处理延时


## log_format 自定义日志格式

语法
```
access_log path [format [buffer=size] [gzip[=level]] [flush=time] [if=condition]]; # 设置访问日志
access_log off; # 关闭访问日志

```
- path 指定日志的存放位置。
- format 指定日志的格式。默认使用预定义的combined。
- buffer 用来指定日志写入时的缓存大小。默认是 64k。
- gzip 日志写入前先进行压缩。压缩率可以指定，从 1 到 9 数值越大压缩比越高，同时压缩的速度也越慢。默认是 1。
- flush 设置缓存的有效时间。如果超过 flush 指定的时间，缓存中的内容将被清空。
- if 条件判断。如果指定的条件计算为 0 或空字符串，那么该请求不会写入日志。
- 另外，还有一个特殊的值 off。如果指定了该值，当前作用域下的所有的请求日志都被关闭。


## 访问日志实例

```
access_log /var/logs/nginx-access.log buffer=32k gzip flush=1m

```
该例子指定日志的写入路径为/var/logs/nginx-access.log，日志格式使用默认的combined，指定日志的缓存大小为 32k，日志写入前启用 gzip 进行压缩，压缩比使用默认值 1，缓存数据有效时间为 1 分钟。


```
http {
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                                        '"$status" $body_bytes_sent "$http_referer" '
                                        '"$http_user_agent" "$http_x_forwarded_for" '
                                        '"$gzip_ratio" $request_time $bytes_sent $request_length';
    log_format srcache_log '$remote_addr - $remote_user [$time_local] "$request" '
                                '"$status" $body_bytes_sent $request_time $bytes_sent $request_length '
                                '[$upstream_response_time] [$srcache_fetch_status] [$srcache_store_status] [$srcache_expire]';
    open_log_file_cache max=1000 inactive=60s;
    server {
        server_name ~^(www\.)?(.+)$;
        access_log logs/$2-access.log main;
        error_log logs/$2-error.log;

        location /srcache {
            access_log logs/access-srcache.log srcache_log;
        }
    }
}
```

## 常用命令

1. 根据访问IP统计UV
```
awk '{print $1}'  access.log|sort | uniq -c |wc -l
```
2. 统计访问URL统计PV
```
awk '{print $7}' access.log|wc -l

```
3. 访问最频繁的URL
```
awk '{print $7}' access.log|sort | uniq -c |sort -n -k 1 -r|more

```
4. 访问最频繁的IP
```
awk '{print $1}' access.log|sort | uniq -c |sort -n -k 1 -r|more

awk '{print $1}' access.log|sort | uniq -c |sort -n -k 1 -r|head -n 100

```

5. sed根据时间段查看
  使用 sed -n 匹配开始 和 结束 精确度稍微低点，一般匹配到小时，若分钟或者秒，就可能匹配不到
```
cat  access.log| sed -n '/14\/Mar\/2015:21/,/14\/Mar\/2015:22/p'|more

```

6. awk根据时间段查看
 awk 查询更全一些，因为提取日期时间进行比较。
 start_time 和 stop_time 自己手动或者自动生成, 格式 （13/Aug/2019:16:47:14 ）
```
tac access.log | awk -v st="$start_time" -v et="$stop_time" '{t=substr($4,RSTART+2,21);if(t>=st && t<=et) {print $0}}'

统计结果某时间段的访问IP数量排行 

cat access.log | awk -v st="$start_time" -v et="$stop_time" '{t=substr($4,RSTART+2,21);if(t>=st && t<=et) {print $1}}'|sort |uniq -c

```

7. 查询某个IP的详细访问情况,按访问频率排序
```
grep '127.0.01' access.log |awk '{print $7}'|sort |uniq -c |sort -rn |head -n 100

```

## error_log
错误日志error_log，一共有6个等级，分别是info, notice, warn, error, crit, alert(emerg)， 默认的输出等级是error
```
 error_log /var/log/nginx/error.log error;

```

# 泛域名证书wildcardssh
使用泛域名后
1. 部分其它SSL证书的网站会被定向到使用泛域名SSL的网站。
2. 其它A记录解析到本站的域名会被显式重定向。


Let's Encrypt: https://letsencrypt.org , 是一个免费的、自动化的、开放的证书颁发机构。
可使用acme.sh或certbot来安装，并自动续期。 60 天以后会自动续期。


## acme.sh
https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E

## 安装 acme.sh
```
curl  https://get.acme.sh | sh

```
普通用户和root用户都可以安装使用，安装脚本其实是进行了如下操作:
1）会把 acme.sh 安装到你所执行命令用户的用户目录下： `~/.acme.sh/` 
2）会创建 bash 的 alias，方便你的使用：`alias > acme.sh=~/.acme.sh/acme.sh`
3）会自动创建 crontob 脚本，每天零点自动检测和更新所有的证书。
安装过程不会污染已有的系统任何功能和文件，所有后续的修改都将限制在安装目录中：`~/.acme.sh/`

## 验证域名并生成证书
acme.sh 实现了 acme 协议支持的所有验证协议，通常一般有几种方式验证域名：**HTTP文件验证** 和 **DNS验证** 等、。

### HTTP文件验证：
```
acme.sh --issue -d qq.com  -w /home/webroot

```
通过http所访问到的本地目录上面这段过程将会在 `/home/webroot` 创建一个 .well-known 的文件夹，同时 Let’s Encrypt 将会通过你要注册的域名去访问那个文件来确定权限，它可能会去访问 http://qq.com/.well-known/ 这个路径，验证成功会自动清理。

### DNS-API验证
泛域名只支持此方式。
‌通过DNS服务器提供 key 与 secret 实现自动验证

‌托管在**Cloudflare**的域名
进入https://dash.cloudflare.com/profile/api-tokens，复制`Global API Key`

```
export CF_Key="3pe***"
export CF_Email="***@mail.com"

acme.sh --issue -d qq.com -d *.qq.com --dns dns_cf
```

在**腾讯云**解析的域名
请前往 https://www.dnspod.cn/console/user/security 控制台中申请子账号 API Token 并执行命令：

```
export DP_Id="6*ID*0"
export DP_Key="aa445e***TOKEN***5fe26e"
acme.sh --issue -d qq.com -d *.qq.com --dns dns_dp

```
注意：对于通配符证书需要加` -d 域名 -d *.域名` 两个参数DP_Id 和 DP_Key 将会保存在 `~/.acme.sh/account.conf`
执行上述命令后，证书文件将会自动申请被存放在` ~/.acme.sh/` 对应的域名文件夹中，如：`~/.acme.sh/qq.com` 。后续 acme.sh 将会自动更新该文件夹内的证书。

### 部署nginx


1) 拷贝证书
**Nginx版**：
```
acme.sh --installcert -d example.com \
--key-file       /usr/local/nginx/key.pem  \
--fullchain-file /usr/local/nginx/cert.pem \
--reloadcmd     "service nginx force-reload"

```
**Apache** :
```
acme.sh --install-cert -d example.com \
--cert-file      /path/to/certfile/in/apache/cert.pem  \
--key-file       /path/to/keyfile/in/apache/key.pem  \
--fullchain-file /path/to/fullchain/certfile/apache/fullchain.pem \
--reloadcmd     "service apache2 force-reload"

```
注意：通过该命令可将 `/.acme.sh/qq.com` 内的证书copy到指定位置/etc/nginx 为nginx服务器实际的地址(可修改为自己服务器对应的地址)service nginx force-reload 为nginx重启命令(可修改为自己服务器对应的命令)，force-reload 会重载证书后续 acme.sh 签发了新证书后就自动完成该拷贝过程。

2) 配置nginx

Nginx-Conf‌
```
‌vim /etc/nginx/conf.d/qq.com.conf：
```

```
# http(80) -> https(443/ssl)
server {
    listen 80;
    server_name qq.com;
    rewrite ^(.*)$ https://$host$request_uri;
}
# qq.com
server {
    listen 443;
    server_name qq.com;
    include ssl/qq.com.ssl.conf;

    location / {
        # todo
    }
}
```
SSL-Conf‌
```
‌vim /etc/nginx/ssl/qq.com.ssl.conf：

ssl on;
ssl_certificate ssl/qq.com.cer;
ssl_certificate_key ssl/qq.com.key;

```
## 失效
2020.09.30泛域名证书失效，访问网站显示不安全。
临时切换为一年期证书。

经排查，cert、key文件均已自动申请并正确安装。目标目录下的证书文件修改时间为8.17（证书申请时间为6.17）。

切换回泛域名证书，网站变为https。

新问题：网访问速度很慢，样式显示不全（？）。使用这两种证书均有此问题。可能与网络环境/限制有关。

