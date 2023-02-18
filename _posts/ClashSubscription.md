---
title: Clash自定义订阅和Provider
tags:
  - 服务器
  - 手机
  - 网络
categories: [网络]
date: 2020-03-29 17:49:28
---
<font face="微软雅黑"> </font>
<center>subconverter订阅、配置详解、provider功能、全局代理tap</center>

<!-- more -->
- [地址](#地址)
  - [其它项目地址](#其它项目地址)
- [规则与subconverter](#规则与subconverter)
  - [搭建好的api](#搭建好的api)
  - [简易用法](#简易用法)
  - [我的配置](#我的配置)
  - [Provider](#provider)
- [部署与使用](#部署与使用)
  - [规则更新](#规则更新)
  - [本地使用](#本地使用)
  - [服务器部署](#服务器部署)
    - [Heroku容器部署](#heroku容器部署)
  - [在服务器中配置规则](#在服务器中配置规则)
  - [更新subconverter和新版clash](#更新subconverter和新版clash)
    - [升级步骤](#升级步骤)
- [使用Provider](#使用provider)
  - [筛选节点](#筛选节点)
  - [进阶用法](#进阶用法)
- [解锁服务](#解锁服务)
  - [Netflix DNS解锁](#netflix-dns解锁)
    - [Netflix扩展解锁](#netflix扩展解锁)
  - [解锁网易云](#解锁网易云)
    - [规则](#规则)
  - [Supervisor](#supervisor)
  - [Youtube 去广告](#youtube-去广告)
- [全局配置](#全局配置)
  - [用Proxifer省心](#用proxifer省心)
  - [UWP](#uwp)
  - [Tap](#tap)
  - [混合配置（mixin）](#混合配置mixin)
- [目前存在的问题](#目前存在的问题)
- [ClashShell](#clashshell)
- [docker clash](#docker-clash)
  
--- 

- 在`节点更改频繁`和需要`在线规则更新`时使用在线订阅。否则可使用`Provider`或`手动配置`。
- Clash还是有未修复`bug`，如tap的使用，使用遇到问题不要纠结。等更新修复。

--- 

# Clash订阅
##  地址
[Clash github](https://github.com/Dreamacro/clash)
[Clash官方说明书](https://docs.cfw.lbyczf.com/)

[各种代理软件教程合集](https://wiki.kache.moe/)
### 其它项目地址

[Subconvert](https://github.com/tindy2013/subconverter)
运行 subconverter 主程序后，按照 调用说明 的对应内容替换即可得到一份使用神机规则的配置文件。
[URL ENCODE](https://www.urlencoder.org/)
[Provider 说明](https://www.notion.so/Clash-Proxy-Provider-ff8d1955f6234ad3a779fecd3b3ea007)

[Clash proxy-provider 搭配 subconverter 使用小记](https://10101.io/2020/02/12/use-clash-proxy-provider-with-subconverter#Provider+API%E4%BD%BF%E7%94%A8%E5%AE%9E%E4%BE%8B)
[订阅转换API](https://limbopro.xyz/archives/subconverter.html)
[SS/SSR/V2Ray订阅类型转换教程 | 万能API](https://merlinblog.xyz/wiki/api.html)

## 规则与subconverter


subconverter使用的主要规则-[神机规则](https://github.com/ConnersHua/Profiles/tree/master)
项目中包含以下规则的本地文件：
`ACL4SSR  ConnersHua  HKMTMedia.list  lhie1  LocalAreaNetwork.list  MSServices.list  NobyDa`

各个规则互有包含，建议只使用一个。
[ACL4SSR](https://github.com/ACL4SSR/ACL4SSR)
[NobyDa](https://github.com/NobyDa/Script/tree/master/Surge)
[ConnersHua](https://github.com/ConnersHua/Profiles/tree/master/Surge/Ruleset)
[NobyDa](https://github.com/NobyDa/Script/tree/master/Surge)
  

### 搭建好的api
使用[项目](https://github.com/tindy2013/heroku-subconverter)在[Heroku](https://dashboard.heroku.com/apps)一键搭建的api（每次原项目更新需要重新部署）:
https://subconverterapi.herokuapp.com/

其它api:
https://fndroid.github.io/clash-config-builder/
https://bnbpro.xyz/sub?target=clash&url=URL_ENCODED_LINKS
带GUI的网站：
https://acl4ssr.netlify.com/
https://api.10101.io/ #使用subconverter
https://sub.dleris.best/  #这个使用的[lhie1](https://github.com/lhie1/Rules)规则屏蔽app广告不行,国内媒体不全


### 简易用法
`http://127.0.0.1:25500/sub?target=%TARGET%&url=%URL%&config=%CONFIG%`

|调用参数|必要性|示例|解释|
|---|---|---|---|
|target|必要|surge&ver=4|指想要生成的配置类型|
|url|必要|https%3A%2F%2Fwww.xxx.com|指机场所提供的订阅链接，需要经过 URLEncode 处理|
|config|可选|https%3A%2F%2Fwww.xxx.com|指远程 pref.ini (包含分组和规则部分)，需要经过 URLEncode 处理，默认加载本地设置文件|


### 我的配置
默认未开启`运营商拦截`和`广告拦截`；部分或者全部已经被包含在`全球拦截`里。

```
[# ~]# cat /home/subconverter/snippets/groups.txt 
🔰 节点选择`select`[]♻️ 自动选择`[]🎯 全球直连`.*
♻️ 自动选择`url-test`.*`http://www.gstatic.com/generate_204`300
;🎥 NETFLIX`select`[]🔰 节点选择`[]♻️ 自动选择`[]🎯 全球直连`.*
;⛔️ 广告拦截`select`[]🛑 全球拦截`[]🎯 全球直连`[]🔰 节点选择
;🚫 运营劫持`select`[]🛑 全球拦截`[]🎯 全球直连`[]🔰 节点选择
🌍 国外媒体`select`[]🔰 节点选择`[]♻️ 自动选择`[]🎯 全球直连`.*
🌏 国内媒体`select`[]🎯 全球直连`(HGC|HKBN|PCCW|HKT|深台|彰化|新北|台|hk|港|tw)`[]🔰 节点选择
Ⓜ️ 微软服务`select`[]🎯 全球直连`[]🔰 节点选择`.*
📲 电报信息`select`[]🔰 节点选择`[]🎯 全球直连`.*
🍎 苹果服务`select`[]🔰 节点选择`[]🎯 全球直连`[]♻️ 自动选择`.*
🎯 全球直连`select`[]DIRECT
🛑 全球拦截`select`[]REJECT`[]DIRECT
🐟 漏网之鱼`select`[]🔰 节点选择`[]🎯 全球直连`[]♻️ 自动选择`.*

[# snippets]# cat rulesets.txt 
🎯 全球直连,rules/LocalAreaNetwork.list
Ⓜ️ 微软服务,rules/MSServices.list
🎯 全球直连,rules/ConnersHua/Surge/Ruleset/Unbreak.list
🛑 全球拦截,rules/NobyDa/Surge/AdRule.list
🛑 全球拦截,rules/ConnersHua/Surge/Ruleset/Hijacking.list
;🎥 NETFLIX,rules/ConnersHua/Surge/Ruleset/Media/Netflix.list
🌍 国外媒体,rules/ConnersHua/Surge/Ruleset/GlobalMedia.list
🌏 国内媒体,rules/lhie1/Surge/Surge 3/Provider/AsianTV.list
📲 电报信息,rules/ConnersHua/Surge/Ruleset/Telegram.list
🔰 节点选择,rules/ConnersHua/Surge/Ruleset/Global.list
🍎 苹果服务,rules/ConnersHua/Surge/Ruleset/Apple.list
🎯 全球直连,rules/ConnersHua/Surge/Ruleset/China.list
🎯 全球直连,rules/NobyDa/Surge/Download.list
🎯 全球直连,[]GEOIP,CN
🐟 漏网之鱼,[]FINAL

[# snippets]# cat rulesets_remote.txt //这个没有用

```

### Provider
使用subconverter需要加上`&list=true`参数，只获取节点。
[Provider 说明](https://www.notion.so/Clash-Proxy-Provider-ff8d1955f6234ad3a779fecd3b3ea007)
用于`只更新节点`。
- 切换到规则时立即会更新provider内容。
- http链接中无有效proxies时不会更新/替代本地文件。

```
proxy-provider:
      name: #name of the provider
        type: #type of the provider, it can be a HTTP or a File
        path: #where is the file, ./ relative to clash home
        url: #only available when type is HTTP, where to download a file. You don't need to create a new file in local space.
        interval: #auto-update interval,only available when type is HTTP
        health-check: #health check opinion start at here
          enable:
          url: 
          interval:
```
Here is an example: 
```
proxy-provider:
  hk:
    type: http
    path: ./hk.yaml
    url: http://remote.lancelinked.icu/files/hk.yaml
    interval: 3600
    health-check:
      enable: true
      url: http://www.gstatic.com/generate_204
      interval: 300
  us:
    type: file
    path: /home/lance/.clash/provider/us.yaml
    health-check:
      enable: true
      url: http://www.gstatic.com/generate_204
      interval: 300
```

Here is an example of `hk.yaml`
```
proxies:
- name: "ss1"
  type: ss
  server: server
  port: 443
  cipher: chacha20-ietf-poly1305
  password: "password"
  # udp: true

```

同时使用`proxy`和`use`时，provider无法检查健康状态。
If you want to use Provider, you should place Provider name in use instead of proxies.  Certainly, you can combine use Provider and Proxies in Proxy Group, but provider can’t check health any more in this group. Here is a part example of config.yaml .

```
Proxy Group: 
  - name: Proxy
    type: select
    use:
      - hk
      - us

  - name: Domestic
    type: select
    use:
      - hk
      - us
    proxies:
      - DIRECT
```

## 部署与使用

### 规则更新
一些文件需要更新
1. `pref.ini`
2. `groups.txt`
3. `rulesets.txt`
4. `rename_node.txt`

### 本地使用
下载subconverter的Windows版[release](https://github.com/tindy2013/subconverter/releases)；运行；此时本地可调用`api`为`127.0.0.1:25500`。

### 服务器部署

```
wget https://github.com/tindy2013/subconverter/releases/download/v0.4.4/subconverter_linux64.tar.gz
tar -xvf subconverter_linux64.tar.gz 
# 直接执行 ./subconverter 即可运行，默认监听 0.0.0.0:25500
# 采用 pm2 来管理后台运行和开启自启
# 安装 pm2
wget -qO- https://getpm2.com/install.sh | bash
# 启动 subconverter 
pm2 start subconverter
# 生成开机启动脚本
pm2 startup
# 保存现有list
pm2 save

```

如需使用域名访问，需要：

```
#  修改 pref.ini 中监听地址
vim pref.ini
修改 server 字段如下
[server]
;Address to bind on for Web Server
listen=127.0.0.1

# nginx中配置反代
# 可配置https

```

#### Heroku容器部署
快速部署到 Heroku： 
https://github.com/tindy2013/heroku-subconverter
http://subconverterapi.herokuapp.com/

使用方法：
1. 打开仓库 选择 Use this template 生成新的仓库并保存到自己的 GitHub 中
2. 修改 base 文件夹中的 pref 配置文件 (如更改 access_token 以及将 managed_config_prefix 修改为 Heroku 分配的域名)
3. 访问 https://heroku.com/deploy?template=自己的仓库地址 输入 App name 点击 Deploy app 即可部署

### 在服务器中配置规则

按照需求配置好后可直接更新订阅使用。同时`更新节点和规则`。
关键配置文件见github仓库。

由于clash规则多来源于surge，故配置中也常用`surge`字样，有些可以忽略。

* 使用自定义规则；
* 自定义分组；

关于 subconverter 主程序目录中 `pref.ini` 文件的解释

1. `pref.ini`：尽量不更改。
    `update_ruleset_on_request`
根据请求执行规则集更新，设置为 true 时打开，默认为 false
2. `groups.txt`
实现各种分组功能。
按照关键词分组。
可区别多个订阅链接。
`此处筛选发生在rename_node之后`
3. `rulesets.txt`
加一条 `节点选择,自己的list地址`；在线list可能无法获取。
4. `rename_node.txt` 重命名
节点重命名
`rename_node=台湾@台`
`vi /home/subconverter/snippets/rename_node.txt`
@符合前面为正则表达式，注意`[]`等特殊符号需要加`\`。@后面为文本字符。

### 更新subconverter和新版clash
更新clash后提示需要使用新的profile（安卓2.1.5、windows 0.11.7），故将更新converter`0.5.1`to`0.6.3`。

使用Beyond Compare比较两个版本的文件结构及内容：
1. 文件结构基本未改变；
2. 只有`pref.ini`有内容的增减和改变，实践证明可以不关心这些改变；
3. 其它关键自定义配置文件及其格式、语法未有变化。

#### 升级步骤
1. `pref.ini`：更改默认的25500端口。
2. `groups.txt`、`rulesets.txt`、`rename_node.txt`：`./snippsets`文件夹下，覆盖。
3. `MyRules.list` :复制到与snipsets同文件夹。
4. `pm2 delete subconverter&& pm2 start subconverter&&pm2 save`:删除老版，启动并保存新版。

## 使用Provider
只更新`节点`。
注意部分参数需要进行[URL ENCODE](https://www.urlencoder.org/)处理。
[调用说明](https://github.com/tindy2013/subconverter/blob/master/README-cn.md#%E8%B0%83%E7%94%A8%E8%AF%B4%E6%98%8E)


### 筛选节点
需要用到的参数有：
`include筛选发生在rename_node之前`

```
target=clash    # 转换结果为 clash 配置
url=https://nfnf.xyz/link/abcdefg?mu=4    # 机场订阅链接
include=(TW|台湾|台灣)    
    # 英文括号可不Encode;include发生在rename_node之前；只匹配台湾节点
list=true    # 生成 provider 链接

# 将 url 和 include 内容均 urlencode：
url=https%3a%2f%2fnfnf.xyz%2flink%2fabcdefg%3fmu%3d4
include=(TW%7c%e5%8f%b0%e6%b9%be%7c%e5%8f%b0%e7%81%a3)

# 将参数拼接起来得到新的订阅链接——只包含台湾节点的 provider 链接
https://api.10101.io/sub?target=clash&url=https%3a%2f%2fnfnf.xyz%2flink%2fabcdefg%3fmu%3d4&include=(TW%7c%e5%8f%b0%e6%b9%be%7c%e5%8f%b0%e7%81%a3)&list=true

# 同理可得到只包含美国节点的 provider 链接
https://api.10101.io/sub?target=clash&url=https%3a%2f%2fnfnf.xyz%2flink%2fabcdefg%3fmu%3d4&include=(US%7c%e7%be%8e%e5%9b%bd)&list=true

```

配置对应的***.yml文件，非config.yaml:

```

# proxy-provider
proxy-provider:
  TW:
    type: http
    path: ./tw.yaml
    url: https://api.10101.io/sub?target=clash&url=https%3a%2f%2fnfnf.xyz%2flink%2fabcdefg%3fmu%3d4&include=(TW%7c%e5%8f%b0%e6%b9%be%7c%e5%8f%b0%e7%81%a3)&list=true
    interval: 3600
    health-check:
        enable: false
        url: http://www.gstatic.com/generate_204
        interval: 300
  US:
    type: http
    path: ./us.yaml
    url: https://api.10101.io/sub?target=clash&url=https%3a%2f%2fnfnf.xyz%2flink%2fabcdefg%3fmu%3d4&include=(US%7c%e7%be%8e%e5%9b%bd)&list=true
    interval: 3600
    health-check:
        enable: false
        url: http://www.gstatic.com/generate_204
        interval: 300

# 在 Proxy Group 中 🎬 HKMTMedia 组和 💰️PayPal 中引入 provider
Proxy Group:
  - name: 🎬 HKMTMedia
    type: select
    proxies:
      - DIRECT
    use:
        - TW    # provider
  - name: 💰️PayPal
    type: select
    use:    
      - US    # provider
    proxies:    # 以下为自建节点
      - 🇺🇸BWG-DC6
      - 🇺🇸Spartan
      - DIRECT
```         

### 进阶用法

|调用参数|必要性|示例|解释|
|---|----|----|----|
|target|必要|surge&ver=4|指想要生成的配置类型，详见上方 支持类型 中的参数|
|url|可选|https%3A%2F%2Fwww.xxx.com|指机场所提供的订阅链接，需要经过 URLEncode 处理，可选的前提是在 default_url 中进行指定。也可以使用 data URI|
|config|可选|https%3A%2F%2Fwww.xxx.com|指远程 pref.ini (包含分组和规则部分)，需要经过 URLEncode 处理，可查看 示例仓库 寻找灵感，默认加载本地设置文件|
|upload|可选|true / false|用于将生成的订阅文件上传至 Gist，需要填写gistconf.ini，默认为 false (即不上传)|
|upload_path|可选|MySS.yaml|用于将生成的订阅文件上传至 Gist 后的名称，需要经过 URLEncode 处理|
|emoji|可选|true / false|用于在节点名称前加入 Emoji，默认为 true|
|group|可选|MySS|用于设置该订阅的组名，多用于 SSD/SSR|
|tfo|可选|true / false|用于开启该订阅链接的 TCP Fast Open，默认为 false|
|udp|可选|true / false|用于开启该订阅链接的 UDP，默认为 false|
|scv|可选|true / false|用于关闭 TLS 节点的证书检查，默认为 false|
|list|可选|true / false|用于输出 Surge Node List 或者 Clash Proxy Provider 或者 Quantumult (X) 的节点订阅 或者 解码后的 SIP002|
|sort|可选|true / false|用于对输出的节点或策略组进行再次排序，默认为 false|
|include|可选|详见下文中 include_remarks|指仅保留匹配到的节点，支持正则匹配，需要经过 URLEncode 处理，会覆盖配置文件里的设置|
|exclude|可选|详见下文中 exclude_remarks|指排除匹配到的节点，支持正则匹配，需要经过 URLEncode 处理，会覆盖配置文件里的设置|
|filename|可选|MySS|指定该链接生成的配置文件的文件名，可以在 Clash For Windows 等支持文件名的软件中显示出来|
|append_type|可选|true / false|用于在节点名称前插入节点类型，如 [SS],[SSR] 等|
|append_info|可选|true / false|用于输出包含流量或到期信息的节点, 默认为 true，设置为 false 则取消输出|
|expand|可选|true / false|用于在 API 端处理或转换 Surge, QuantumultX 的规则列表，即不将规则全文置入配置文件中，默认为 false，设置为 true 则将规则全文写进配置文件|
|dev_id|可选|92DSAFA|用于设置 QuantumultX 的远程设备 ID, 以在某些版本上开启远程脚本|
|interval|可选|43200|用于设置托管配置更新间隔，确定配置将更新多长时间，单位为秒|
|strict|可选|true / false|如果设置为 true，则 Surge 将在上述间隔后要求强制更新|

```

## 解锁服务
### Netflix DNS解锁
[netflix-proxy](https://github.com/ab77/netflix-proxy)
[利用Netflix-proxy配置解锁NetFlix](https://zhujiget.com/2420.html)
需解锁的机器1的DNS（设置为机器2的ip）由可访问NF的机器2解析。
V2ray中可配置不同域名使用不同DNS。

一键脚本（Ubuntu可用）：
```
 apt-get update\
   && apt-get -y install vim dnsutils curl sudo\
   && curl -fsSL https://get.docker.com/ | sh || apt-get -y install docker.io\
   && mkdir -p ~/netflix-proxy\
   && cd ~/netflix-proxy\
   && curl -fsSL https://github.com/ab77/netflix-proxy/archive/latest.tar.gz | gunzip - | tar x --strip-components=1\
   && ./build.sh 
```
### Netflix扩展解锁 
[Cookies 扩展](https://nikkie.xyz/2020/03/10/NF/)
[chrome 扩展](https://tecknity.com/free-netflix-account-cookies/)
下载并安装扩展`Account pool`，扩展中点击接收并继续。
[网站底部](https://tecknity.com/free-netflix-account-cookies/%EF%BC%89)提供cookies链接，进入显示Cookies的页面后在扩展中点击使用。

### 解锁网易云
`搭建的为http代理，需要手动加到clash的yml文件中。`？
`手机app许清除缓存，且要等一会才会解析出来`

[解锁网易云灰色歌曲](https://merlinblog.xyz/wiki/neteasemusic.html)

`国内VPS搭建代理-运行-Clash分流`
[UnblockNeteaseMusic](https://github.com/nondanee/UnblockNeteaseMusic)
一款可以让网易云曲库里的灰色歌曲能够正常播放的神器。`Node.js`运行。

* 使用 QQ / 虾米 / 百度 / 酷狗 / 酷我 / 咪咕 / JOOX 音源替换变灰歌曲链接 (默认仅启用一、五、六)
* 为请求增加 X-Real-IP 参数解锁海外限制，支持指定网易云服务器 IP，支持设置上游 HTTP / HTTPS 代理
* 完整的流量代理功能 (HTTP / HTTPS)，可直接作为系统代理 (同时支持 PAC)

[衍生项目](https://github.com/nondanee/UnblockNeteaseMusic/issues/233)


#### 规则
`rules/ACL4SSR/Clash/Ruleset/NetEaseMusic.list`
```
DOMAIN-SUFFIX,interface.music.163.com,网易云音乐
DOMAIN-SUFFIX,interface1.music.163.com,网易云音乐
DOMAIN-SUFFIX,interface2.music.163.com,网易云音乐
DOMAIN-SUFFIX,interface3.music.163.com,网易云音乐
DOMAIN-SUFFIX,interface4.music.163.com,网易云音乐
DOMAIN-SUFFIX,interface5.music.163.com,网易云音乐
DOMAIN-SUFFIX,apm.music.163.com,网易云音乐
DOMAIN-SUFFIX,apm3.music.163.com,网易云音乐
DOMAIN-SUFFIX,man.netease.com,网易云音乐
DOMAIN-SUFFIX,api.iplay.163.com,网易云音乐
DOMAIN-SUFFIX,ac.dun.163yun.com,网易云音乐
DOMAIN-SUFFIX,mr.da.netease.com,网易云音乐
DOMAIN-SUFFIX,crash.163.com,网易云音乐
DOMAIN-SUFFIX,imap.163.com,网易云音乐
DOMAIN-SUFFIX,music.126.net,网易云音乐
DOMAIN-SUFFIX,music.163.com,网易云音乐
DOMAIN-KEYWORD,netease,网易云音乐
IP-CIDR,59.111.181.60/32,网易云音乐
IP-CIDR,223.252.199.66/32,网易云音乐
IP-CIDR,223.252.199.67/32,网易云音乐
IP-CIDR,59.111.160.195/32,网易云音乐
IP-CIDR,59.111.160.197/32,网易云音乐
IP-CIDR,59.111.181.35/32,网易云音乐
IP-CIDR,59.111.181.38/32,网易云音乐
IP-CIDR,39.105.63.80/32,网易云音乐
IP-CIDR,47.100.127.239/32,网易云音乐
IP-CIDR,118.24.63.156/32,网易云音乐
IP-CIDR,193.112.159.225/32,网易云音乐
IP-CIDR,59.111.181.155/32,网易云音乐
IP-CIDR,115.236.118.33/32,网易云音乐
IP-CIDR,59.111.128.0/17,网易云音乐
IP-CIDR,115.236.112.0/20,网易云音乐
IP-CIDR,223.252.192.0/19,网易云音乐
IP-CIDR,101.71.154.241/32,网易云音乐
```

### Supervisor

supervisor是一个Python开发的通用的进程管理程序，可以管理和监控Linux上面的进程，能将一个普通的命令行进程变为后台daemon，并监控进程状态，异常退出时能自动重启。

配置supervisor并在后台运行
`nano /etc/supervisord.d/netease.ini`
写入以下配置：
```
[supervisord]
nodaemon=false

[program:netease]
user=root
directory=/root/UnblockNeteaseMusic
command=/usr/bin/node app.js -p 8848
autostart=true
autorestart=true
```


**启动项目**

systemctl start supervisord
systemctl enable supervisord

### Youtube 去广告
广告阻止不仅于使用 [Rule] 规则，有的广告需要 [URL Rewrite] 和 [MITM]
会造成以下问题

* 网页版可能无法正常播放
* YouTube Premium 用户无法正常播放
* Quantumult 遇到片头广告时可能会卡黑屏
所以默认并没有启用，如果仍需启用需在「HTTPS 解密(MitM)」的「主机名」列表中添加：
`*.googlevideo.com`


## 全局配置

[浅谈在代理环境中的DNS解析行为](https://blog.skk.moe/post/what-happend-to-dns-in-proxy/)

### 用Proxifer省心
并且设置默认规则为走代理。这样才能代理`Xshell`。

### UWP

勾选`EXAMPT ALL`

默认的情况下，UWP应用会被禁止使用回环地址（127.0.0.1）的代理，而CFW和ss-win均是本地代理，都是通过回环地址提供代理的。
通过ss-win的处理方式可以知道，PAC分流会让系统放行直连流量，所以大部分不需要代理的UWP应用都可以在ss-win的全局PAC分流下使用。而CFW除了内置的几个cfw-bypass字段以外，所有流量都需要处理，这就使得几乎所有的`UWP在开启系统代理时都不可用`。

### Tap

```
Fndroid on Mar 6 • 
请不要使用管理员权限启动Clash for Windows  //???
重新Install TAP Device
```

[Clash 教程](https://merlinblog.xyz/wiki/cfw.html)
[TAP_开发者的文档](https://docs.cfw.lbyczf.com/contents/tap.html)

https://github.com/Fndroid/clash_for_windows_pkg/issues/466
对于不遵循系统代理的软件，TAP模式可以接管其流量并交由CFW处理

nslookup的NameServer为127.0.0.1；ip为按照顺序显示的伪IP。

0.9.0及以后版本
1. 安装TAP网卡
点击General中TAP Device将会安装TAP网卡，此网卡用于接管系统流量，安装完成可在系统网络连接中看到名为cfw-tap的网卡
2. 启动TAP模式
使用的Profile中包含fake-ip设置：
```

dns:
   enable: true
   enhanced-mode: fake-ip # 或 redir-host
   listen: 0.0.0.1:53
   nameserver:
      - 223.5.5.5
experimental:
  interface-name: WLAN # WLAN 为物理网卡名。 不填会无法连接tap

```
此版本可以使用interface-name属性避免回环，所以可以不使用fake-ip模式，并且支持了UDP及IP类请求。
0.9.6移除interface-name属性，改为自动检测。
***
以非管理员身份运行Clash并正确配置网卡名称后可实现全局代理。

### 混合配置（mixin）
版本要求:`0.9.5`版本更新后，支持向所有配置文件中注入`公共属性设置`

例如：在配置文件中统一添加dns字段，操作如下：
在 `config.yaml`中添加以下字段:




```

cfw-profile-mixin: 
  experimental:
    interface-name: WLAN  
  dns:
    enable: true
    enhanced-mode:  fake-ip 
    #enhanced-mode: redir-host 
    listen: 0.0.0.0:53
    nameserver:
      - 223.5.5.5
      - 233.6.6.6
      - 8.8.8.8
      - 114.114.114.114
  fake-ip-filter:
      - 'dns.msftncsi.com'
      - 'www.msftncsi.com'
      - 'www.msftconnecttest.com' 
      - 'd.docs.live.net' 

```



在启动或切换配置时，上面内容将会替换到原有配置文件中进行覆盖。
配置文件内容不会被修改，混合行为只会发生在`内存`中。
可以通过`任务栏图标`菜单开关这个行为。

## 目前存在的问题
从[项目issue](https://github.com/fndroid/clash_for_windows_pkg/issues/)来看，目前`tap的问题挺多`。
1. 同时开启系统代理和tap会导致uwp应用网络连接失败。
2. 节点全部timeout，但能够使用。
3. 管理员模式下：重新安装后才可使用（tap显示为网络n）。切换配置或重启软件会[开启 DHCP](https://github.com/Fndroid/clash_for_windows_pkg/issues/667)，导致问题。目前issue未解决....

4. 使用中出现过多次经direct规则的网站访问速度非常慢或无法访问，其它被代理网站正常。
5. 打开connection界面时卡死也可能是因为tap配置错误造成了回环。
6. tap模式工作后无法同步系统时间。
7. 有时需要重启浏览器才有网。



# 常用工具
## ClashShell

https://github.com/juewuy/ShellClash

安装,安装面板，访问面板配置代理规则。公网up访问需配置。

路由器也可以用：[在路由器上安装及使用ShellClash的教程](https://juewuy.github.io/post/clash-for-miwifi-an-zhuang-ji-shi-yong-jiao-cheng/)

## docker clash
在docker中跑clash服务。

https://fx.tmioe.com/820.html

## 机场使用历史

youqu-支持ipv6，hn地区可用率低

mosu: 2020.5 - 2022.9跑路，年付60-180都用过

kcssr: 2022.10-2023.2,一般，季付36

泡芙云、cylink：使用过，价格贵，体验还可以

### 2023记录
1. https://duangks.com/ 比较良心
2. https://www.duyaoss.com/ 都很贵


xfss: 2023.2-, 延迟带宽优，10元120G。https://XFTLD.ORG https://XFLTD.TOP

类似不限时机场：[比奇堡](https://duangks.com/archives/95/)、魔戒

优质低流量机场：[花云](https://duangks.com/archives/76/)、[YTOO](https://duangks.com/archives/86/Z)、

大流量老机场：CordCloud(看起来专业)
