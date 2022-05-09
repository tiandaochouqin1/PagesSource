---
title: Cloudflare项目部署和CDN
tags:
  - 网络
  - CDN
categories: 网络
date: 2020-04-16 10:44:29
---
<font face="微软雅黑"> </font>
<center>部署js反向代理google/github，Parterner自选CDN节点</center>

<!-- more -->


- [CF Worker部署](#cf-worker部署)
- [Google](#google)
  - [Workers-proxy](#workers-proxy)
- [WikiPedia](#wikipedia)
  - [CF 代理1](#cf-代理1)
  - [CF 代理2](#cf-代理2)
- [Cloudflare parterner自定义CDN IP](#cloudflare-parterner自定义cdn-ip)
  - [添加](#添加)
  - [自定义CDN节点IP](#自定义cdn节点ip)
  - [ip选择](#ip选择)
  - [三网智能解析](#三网智能解析)
- [免费容器或云托管](#免费容器或云托管)

CloudFlare CDN/CF workers等基本被薅废了。在国内，DNS解析不如其他的智能解析服务好用。
通过 Workers 在边缘运行 JavaScript、Rust、C 和 C++ 等,构建无服务器应用程序。
# CF Worker部署
首页：https://workers.cloudflare.com
注册，登陆，Start building，取一个子域名，Create a Worker。
复制 index.js 到左侧代码框，Save and deploy。

绑定域名：workers->添加路由： `example.domain.com/*`。`*`是必须的。



# Google
## Workers-proxy
[Workers-Proxy](https://github.com/Siujoeng-Lau/Workers-Proxy)。不限于Google。


CF使用人数过多，页面显示流量异常，**无法访问**
改为以下：
```
const upstream_mobile = 'www.google.com.au'
const upstream = 'www.google.com.au'
const blocked_region = [ 'SY', 'PK', 'CU'] //去掉 CN
```

# WikiPedia

## CF 代理1
```
addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

async function handleRequest(request) {
  try {
    var u = (new URL(request.url))
    if (u.protocol !== 'https:') {
      u.protocol = 'https:'
      return (new Response('', { status: 301, headers: { 'Location': u.href } }))
    }
    u.searchParams.append('variant', 'us-en')
    if (u.pathname === '/') { return (new Response('', { status: 302, headers: { 'Location': '/wiki/' } })) }
    var ua = (new Headers(request.headers)).get('User-Agent')
    var ual = (ua || '').toLowerCase()
    var d = await fetch(`https://en${(ual.indexOf('mobile') !== -1 || ual.indexOf('android') !== -1 || ual.indexOf('like mac os x') !== -1) ? '.m' : ''}.wik${'i'}pedia.org${u.pathname}${u.search}`)
    return (new Response(d.body, { status: d.status, headers: d.headers }))
  } catch (e) {
    return (new Response(String(e), { status: 500 }))
  }
}

```

## CF 代理2

```
// 你想镜像的站点
const upstream = 'zh.wikipedia.org'
// 针对移动端适配站点，没有就和保持和上面一致
const upstream_mobile = 'zh.wikipedia.org'
// 禁止某些地区访问
const blocked_region = []
// 禁止自访问
const blocked_ip_address = ['0.0.0.0', '127.0.0.1']
// 你想镜像的站点
const replace_dict = {
    '$upstream': '$custom_domain',
    '//zh.wikipedia.org': ''
}
// 剩下的就不用管了
addEventListener('fetch', event => {
    event.respondWith(fetchAndApply(event.request));
})
async function fetchAndApply(request) {
    const region = request.headers.get('cf-ipcountry').toUpperCase();
    const ip_address = request.headers.get('cf-connecting-ip');
    const user_agent = request.headers.get('user-agent');
    let response = null;
    let url = new URL(request.url);
    let url_host = url.host;
    if (url.protocol == 'http:') {
        url.protocol = 'https:'
        response = Response.redirect(url.href);
        return response;
    }
    if (await device_status(user_agent)) {
        upstream_domain = upstream
    } else {
        upstream_domain = upstream_mobile
    }
    url.host = upstream_domain;
    if (blocked_region.includes(region)) {
        response = new Response('Access denied: WorkersProxy is not available in your region yet.', {
            status: 403
        });
    } else if(blocked_ip_address.includes(ip_address)){
        response = new Response('Access denied: Your IP address is blocked by WorkersProxy.', {
            status: 403
        });
    } else{
        let method = request.method;
        let request_headers = request.headers;
        let new_request_headers = new Headers(request_headers);
        new_request_headers.set('Host', upstream_domain);
        new_request_headers.set('Referer', url.href);
        let original_response = await fetch(url.href, {
            method: method,
            headers: new_request_headers
        })
        let original_response_clone = original_response.clone();
        let original_text = null;
        let response_headers = original_response.headers;
        let new_response_headers = new Headers(response_headers);
        let status = original_response.status;
        new_response_headers.set('access-control-allow-origin', '*');
        new_response_headers.set('access-control-allow-credentials', true);
        new_response_headers.delete('content-security-policy');
        new_response_headers.delete('content-security-policy-report-only');
        new_response_headers.delete('clear-site-data');
        const content_type = new_response_headers.get('content-type');
        if (content_type.includes('text/html') && content_type.includes('UTF-8')) {
            original_text = await replace_response_text(original_response_clone, upstream_domain, url_host);
        } else {
            original_text = original_response_clone.body
        }
        response = new Response(original_text, {
            status,
            headers: new_response_headers
        })
    }
    return response;
}
async function replace_response_text(response, upstream_domain, host_name) {
    let text = await response.text()
    var i, j;
    for (i in replace_dict) {
        j = replace_dict[i]
        if (i == '$upstream') {
            i = upstream_domain
        } else if (i == '$custom_domain') {
            i = host_name
        }
        if (j == '$upstream') {
            j = upstream_domain
        } else if (j == '$custom_domain') {
            j = host_name
        }
        let re = new RegExp(i, 'g')
        text = text.replace(re, j);
    }
    return text;
}
async function device_status (user_agent_info) {
    var agents = ["Android", "iPhone", "SymbianOS", "Windows Phone", "iPad", "iPod"];
    var flag = true;
    for (var v = 0; v < agents.length; v++) {
        if (user_agent_info.indexOf(agents[v]) > 0) {
            flag = false;
            break;
        }
    }
    return flag;
}
```




# Cloudflare parterner自定义CDN IP
[CloudFlare免费CDN加速自定义节点](https://wzfou.com/cloudflare-ip/)


CloudFlare自定义CDN节点IP可以在一定程度上解决免费套餐线路拥堵的问题

域名-> CF CDN ip/domain ->你的IP

在gcp tw服务器使用后并未有加速效果。

## 添加
利用CloudFlare Partner API开发的CloudFlare CDN接入平台平台：
https://cdn.wzfou.com/（来自挖站否）
https://cdn.bnxb.com/
https://cdn.kevsrv.com/
https://cf.tlo.xyz/

1. 添加你的域名。
2. 然后，添加A记录，该记录为你源站的IP地址。
3. 添加完成后，会生成CNAME、Anycast IPv4和NS设置三种方式。
4. 一般来说我们选择CNAME或者是Anycast IPv4来接入。
5. 现在到你的域名DNS管理处，添加CNAME记录，这个CNAME记录就是刚刚生成的记录值。

**检测生效**
按照上面的方法，请先耐心等待DNS解析生效，进入到CloudFlare Partner 接入平台，点击“安全”。
当看到SSL证书生成成功时，CloudFlare CDN接入成功了。

## 自定义CDN节点IP
记得删除原来的CNAME或者A记录
`1.1.1.1/1.0.0.1`是CloudFlare联合APNIC推出的公共DNS解析服务,也可当作CDN的ip。
其它可用ip见教程。
## ip选择
CloudFlare的百度云合作ip
```

162.159.208.4-162.159.208.103
162.159.209.4-162.159.209.103
162.159.210.4-162.159.210.103
162.159.211.4-162.159.211.103

```
网友收集的CloudFlare国内线路友好IP：
```

108.162.236.1/24 联通 走美国
172.64.32.1/24 移动 走香港
104.16.160.1/24 电信 走美国洛杉矶
———
172.64.0.0/24 电信 美国旧金山
104.20.157.0/24 联通 走日本
104.28.14.0/24 移动 走新加坡

```
网友关于各线路推荐列表：

```
电信：推荐走圣何塞，例：104.16.160.* 或者上面的百度云合作 ip。
移动：推荐走移动香港，例：172.64.32.* 141.101.115.* 或者 104.23.240.0-104.23.243.254。
联通：没发布什么好线路，可走圣何塞。例：104.16.160.* 或者 104.23.240.0-104.23.243.254。也可以试一下走亚特兰大 108.162.236.* 。

```
## 三网智能解析

CloudFlare自定义CDN节点IP，移动、电信和联通会出现不同的访问情况，我们可以利用DNS的智能解析服务，将移动、联通、电信用户解析到不同的IP地址上。

自定义CloudFlare的节点IP，有可能被CloudFlare封掉，解决的办法也很简单，利用DNS智能解析，将国外访问按照正常的CloudFlare给的CNAME记录解析，而国内我们则按照自定义IP来解决。

# 免费容器或云托管
https://github.com/ripienaar/free-for-dev

1. github action
2. https://fast.io 
   利用cloudflare的CDN，可直链Google Drive、OneDrive、Github、MediaFire等
3. https://netlify.com
   从GIT仓库或文件夹创建网页。托管github pages
   [用Gitlab+Netlify+Forestry免费搭建免费博客网站](https://bincode.cc/%e7%94%a8gitlabnetlifyforestry%e5%85%8d%e8%b4%b9%e6%90%ad%e5%bb%ba%e5%85%8d%e8%b4%b9%e5%8d%9a%e5%ae%a2%e7%bd%91%e7%ab%99/)
4. https://heroku.com
   各种云服务，如服务器，数据库，监控，计算等等。550 h/month和30 mins空闲宕机（收到请求则激活）的限制。
   部署[subconverter](https://github.com/tindy2013/heroku-subconverter)、[ss](https://github.com/onplus/shadowsocks-heroku)、[v2ray](https://github.com/bclswl0827/v2ray-heroku)
5. https://cloud.okteto.com/
   Kubernetes。与heroku类似，较新，限制多。
6. https://kubesail.com/
   类似heroku。Kubernetes。
7. [IBM Cloud Lite](https://cloud.ibm.com/)
   在线ssh
8. https://www.goorm.io/
   在线ssh
9. https://vercel.com/dashboard
   部署GithubPages。
10. railway.app 容器，按使用量计费，配置强大，够用。[搭建网页版vscode](https://justyy.com/archives/45744)部署 [vscode-server](https://github.com/cdr/deploy-code-server)。被墙。
