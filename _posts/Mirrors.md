---
title: Google
tags:
  - [Google]
categories: [网络]
date: 2020-08-22 18:14:19
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->
- [镜像](#镜像)
  - [搭建Google镜像](#搭建google镜像)
  - [ngx_http_google_filter_module](#ngx_http_google_filter_module)
  - [在线代理](#在线代理)
  - [zmirror](#zmirror)
  - [nginx反代](#nginx反代)
  - [替代搜索引擎](#替代搜索引擎)
    - [whoogle-search](#whoogle-search)
    - [searx](#searx)
- [WIKIPEDIA](#wikipedia)
  - [中文](#中文)
- [Github](#github)
  - [镜像github加速](#镜像github加速)
  - [github1s](#github1s)
- [Bing Wallpaper](#bing-wallpaper)
  - [github+stackoverflow](#githubstackoverflow)
  - [stackoverflow](#stackoverflow)
- [计时提醒](#计时提醒)
- [短网址](#短网址)


https://mirror.js.org/
# 镜像

https://github.com/hmsjy2017/Google-Mirrors 或 https://mirror.js.org/

https://search.iwiki.uk/
http://www.google.cn.ua/
https://www.library.ac.cn/
https://g22.i-research.edu.eu.org/

http://scholar.hedasudi.com/






http://www.9312.net/

http://www.4243.net/

https://ac.scmor.com/


http://sci.xueshuwu.cn/




[真GOOGLE镜像](https://elgoog.im/)...

## 搭建Google镜像

## ngx_http_google_filter_module

[ngx_http_google_filter_module](https://github.com/cuber/ngx_http_google_filter_module)：待学习使用。

## 在线代理

[workers-proxy](https://github.com/xiaoyang-liu-cs/workers-proxy)，不太好用，加载速度慢。

[jsproxy](https://github.com/EtherDream/jsproxy)(其中的CloudFlare Worker版本无法使用，google显示流量异常)


## zmirror
https://github.com/aploium/zmirror


## nginx反代
[nginx代理搭建google镜像站](https://www.chromeba.net/nginx%E5%8F%8D%E5%90%91%E4%BB%A3%E7%90%86%E6%90%AD%E5%BB%BAgoogle%E9%95%9C%E5%83%8F%E7%BD%91%E7%AB%99.html)

[使用 Nginx 反向代理 Google 和 Wikipedia](https://stdrc.cc/post/2018/10/23/reverse-proxy-of-google/)

需要带cookies，否则会转到人机验证。
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
```
nginx -s reload

```

## 替代搜索引擎
1. https://searx.space/
2. https://google61.herokuapp.com/
3. [Start page](https://www.startpage.com/)

### whoogle-search
[Privacy-respecting metasearch engine ](https://github.com/benbusby/whoogle-search)

heroku部署。账户项目[每月使用时间为550h](https://devcenter.heroku.com/articles/free-dyno-hours)（添加信用卡可增加450h），在闲置30分钟后会暂时关闭，需要10-15s才能重新上线。
[避免停机](https://github.com/benbusby/whoogle-search#prevent-downtime-heroku-only)。
在自己的设备上crontab添加：
```
*/15 9-23 * * * curl https://<your heroku app name>.herokuapp.com > /home/<username>/whoogle-refresh
```
### searx
https://github.com/asciimoo/searx

# WIKIPEDIA
[帮助:如何访问维基百科](https://cnwiki.webxp.ml/wiki/Help:%E5%A6%82%E4%BD%95%E8%AE%BF%E9%97%AE%E7%BB%B4%E5%9F%BA%E7%99%BE%E7%A7%91#%E7%BD%91%E9%A1%B5%E4%BB%A3%E7%90%86)

## 中文

https://www.wikipedia.ryancray.com	https://zh.wikipedia.ryancray.com
https://wiki.sxisa.org	https://zh.wiki.sxisa.org
https://www.iwiki.ml	https://zh.iwiki.ml
https://www.wikipedia.iwiki.eu.org	https://zh.wikipedia.iwiki.eu.org
https://www.jinzhao.wiki	https://zh.jinzhao.wiki
https://www.wikiredia.com	桌面版：https://zh.wikiredia.com



# Github

1. 油猴脚本 [Github 增强 - 高速下载](https://github.com/XIU2/UserScript)
2. https://github.com/521xueweihan/GitHub520


## 镜像github加速
- FastGithub油猴脚本 镜像网站+下载
https://github.com/RC1844/FastGithub ：实为包含其它多个镜像github项目API的**集合**。

- FastGit 镜像网站+下载

  https://hub.fastgit.org/


- 镜像网站+  download 文件。
基于[jsproxy](https://github.com/EtherDream/jsproxy)或[FastGithub](https://github.com/RC1844/FastGithub)使用**CloudFlareWorks代理**搭建的 https://github.webxp.ml

- 镜像下载链接生成
[gh-proxy](https://github.com/hunshcn/gh-proxy)：项目参考了jsproxy。示例https://gh.api.99988866.xyz/
elease、archive使用cf加速，文件会跳转至JsDelivr

[github host](https://github.com/521xueweihan/GitHub520)

## github1s
浏览器vscode中浏览仓库。


# Bing Wallpaper
https://github.com/tiandaochouqin1/bing-wallpaper

[Github Actions 自动抓取每日必应壁纸](https://www.wdbyte.com/2021/03/bing-wallpaper-github-action/)


1. 获取api。bing.com和cn.bing.com（参考文章使用）不同。
   F12监控网络，查找带图片文件名的response，找到对应的请求。

2. Java Fastjson 从response解析出url。
3. url加入到repo的readme.md中。
4. github action设置pull，修改，并push。此处使用


## github+stackoverflow
https://gitclone.com/

## stackoverflow
替换googlecdn以访问stackoverflow
https://github.com/justjavac/ReplaceGoogleCDN


# 计时提醒
Iris Pro：屏幕颜色调整、计时器等。
[fadetop](http://www.fadetop.com/)
[workrave](https://workrave.org/download/)
[Wise remainder](https://www.wisecleaner.com/wise-reminder.html)


# 短网址

利用零宽度字符来生成表面上看起来一样的短网址。 https://zws.im/

[零宽度字符](https://juejin.cn/post/6844903669192720391)

零宽度字符：不可打印，用于调整打印格式，如控制换行、连字等。

# hyperbeam

https://hyperbeam.com/app/