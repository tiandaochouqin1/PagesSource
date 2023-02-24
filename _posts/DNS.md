---
title: 域名与邮箱
date: 2019-12-04 15:12:37
tags: VPS
categories: VPS
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->

- [CheapName.com](#cheapnamecom)
- [Freenom免费域名](#freenom免费域名)
- [DNS](#dns)
  - [DNS记录类型](#dns记录类型)
  - [A记录、URL转发、CNAME区别](#a记录url转发cname区别)
  - [DNS主机记录](#dns主机记录)
  - [TTL值](#ttl值)
  - [Cloudflare托管](#cloudflare托管)
  - [Cloudflare 301重定向](#cloudflare-301重定向)
- [GithubPages绑定](#githubpages绑定)
- [私有DNS](#私有dns)
- [域名邮箱](#域名邮箱)
  - [临时邮箱](#临时邮箱)
  - [Yandex域名邮箱](#yandex域名邮箱)
    - [其它登录](#其它登录)


# 域名服务商
## 腾讯云dnspod
腾讯云轻量无忧每3个月赠送20无门槛域名续费券。轻量无忧计划 Lighthouse Care的活动页面地址是：https://cloud.tencent.com/act/lighthouse
## CheapName.com

`Domain List->manage->Advance DNS->Host Records`

## Freenom免费域名
[Freenom](https://my.freenom.com/domains.php)唯一一个提供免费顶级域名的商家,包括.ml/.tk/.ga/.cf/.gq后缀。
到期前14天填可续期，每次最长12 month。
下单时ip需和资料中的国家一致，否则出现`技术性错误，域名注册失败`。
[自动续期](https://github.com/luolongfei/freenom)。可定义邮件/telegram bot 通知。
git clone项目，配置账号、邮箱，然后直接运行。
```
00 09 * * * cd /data/wwwroot/freenom/ && php run > freenom_crontab.log 2>&1
```

# DNS
## DNS记录类型
A和CNAME是最常用的两种。

| Record | Point to |
|---|---|
|A|ipv4 address|
|AAAA|ipv6 address|
|ALIAS|domain name(coexist)|
|CNAME|domain name|
|NS|nameservere,将子域名指定其它域名服务器|
|SRV|记录提供特定服务的服务器|
|TXT|通常做SPF记录(反垃圾邮件)|
|URL Redirect (Unmasked Forwarding)|显式跳转，地址栏变化|
|URL Frame (Masked Forwarding)|隐式跳转|
|URL Redirect (301)|301转向，可作用于搜索引擎|
|MX |邮件交换记录 (MX) 会将电子邮件定向到某个域的服务器|
|MXE|将邮件转发给服务器的ip|
|CAA|指定域名允许哪个证书颁发机构（CA）为其颁发证书|
|PTR| PTR记录是A记录的逆向记录，又称做IP反查记录或指针记录，负责将IP反向解析为域名|

## A记录、URL转发、CNAME区别

* A 记录和 CNAME 属于标准的 DNS 记录，而 URL 转发则实际上只是个简单的重定向。因为 CNAME 是基于 ip 的，而 URL 转发是基于网址。
* URL 转发可以转发到某一个目录下，甚至某一个文件上。而 CNAME 是不可以，这就是 URL 转发和 CNAME 的主要区别所在。
* CNAME 可以随意设，但 URL 转发在一些缺少网络自由的国家是被禁止的，因为 URL 转发还分显示和隐式，很容易造成误解。

应用方面：

* A记录——适应于独立主机、有固定IP地址

* CNAME——适应于虚拟主机、变动IP地址主机

* URL转发——适应于更换域名又不想抛弃老用户

## DNS主机记录

| Type | Function |
|---|---|
|www|www.example.com|
|@|example.com|
|* |泛解析，匹配所有*.example.com|
|mail|邮件服务器|
|二级域名|填写abc->abc.example.com|
|手机网站|填写.m->m.example.com|

## TTL值
生存时间（**Time To Live**），表示解析记录在DNS服务器中的缓存时间，TTL的时间长度单位是秒。比如：在访问www.example.com时，如果在DNS服务器的缓存中没有该记录，就会向某个NS服务器发出请求，获得该记录后，该记录会在DNS服务器上保存TTL的时间长度，在TTL有效期内访问www.example.com，DNS服务器会直接缓存中返回刚才的记录。

## Cloudflare托管
将Name Server指向cloudflare提供的的NS。

使用Cloudflare CDN服务和域名服务器。

## Cloudflare 301重定向
CF不支持隐式转发(DNSPOD支持)。可使用通配符变量。
https://support.cloudflare.com/hc/zh-cn/articles/200172286-%E5%A6%82%E4%BD%95%E5%80%9F%E5%8A%A9-Cloudflare-%E6%89%A7%E8%A1%8C-URL-%E8%BD%AC%E5%8F%91%E6%88%96%E9%87%8D%E5%AE%9A%E5%90%91-
https://support.cloudflare.com/hc/zh-cn/articles/218411427#redirects

需要域名解析到有效的ip地址，同时配置URL转发。

**示例：**
修复从根到 www 
```
example.com/*
```
然后，您可以设置以下 URL 以将流量转发到此处：
```
http://www.example.com/$1
```



# GithubPages绑定

1. 在cn.name中绑定。
2. 在GitHub repository->Setting->Custom Domain 中填入域名。

或者使用url隐式转发。

# 私有DNS
[Next DNS](https://my.nextdns.io/)是一个可自定义的私有DMS服务。

1. secure DNS protocols like DNS-over-HTTPS, DNS-over-TLS or even DNScrypt.
2. Web界面管理
3. 自定义广告拦截与隐私。包含常用的各种规则
4. 延时太高，60ms+。


# 域名邮箱
## 临时邮箱
直接cname到
```
mail.0du.win
```

## Yandex域名邮箱
[利用Yandex搭建个人域名邮箱服务](https://www.newlearner.site/2019/08/05/yandex-domain-mail.html)
申请：https://admin.yandex.ru/domains
1. DNS验证OwnerShip;
2. MX配置；
3. DKIM防垃圾邮件；
4. [Add一个新用户](https://connect.yandex.com/portal/admin/departments/1)，新用户[官网登陆](https://mail.yandex.com/lite)并同意协议。

### 其它登录
```
POP3：pop.yandex.com SSL端口：995
SMTP：smtp.yandex.com SSL端口：465
IMAP：imap.yandex.com SSL端口：993
```
