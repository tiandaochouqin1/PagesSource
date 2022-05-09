---
title: JavaScript
tags:
  - [Javascript]
  - [油猴]
categories: [JavaScript]
date: 2020-10-18 00:08:31
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->


[JavaScript教程](https://www.liaoxuefeng.com/wiki/1022910821149312)
[X分钟速成Y](https://learnxinyminutes.com/)
https://learnxinyminutes.com/docs/zh-cn/javascript-cn/

- [学习JS](#学习js)
- [网址重定向油猴脚本](#网址重定向油猴脚本)
  - [location 方法](#location-方法)
  - [location各字段](#location各字段)
  - [获取url参数](#获取url参数)
  - [百度直链获取脚本](#百度直链获取脚本)
- [其它](#其它)

# 学习JS
[JavaScript 初体验](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/First_steps/A_first_splash):按照此教程[写一遍](../files/number-guessing-game-start.html)，约2小时。

- Dom Tree ：包含了所有的HTMl标签，包括display：none  ，JS动态添加的元素等。
- Render Tree：Dom Tree 和CSSOM结合后构建呈现Render Tree。Render Tree 能识别样式，每个node都有自己的style，且不包含隐藏的节点（比如display : none的节点）。

[JavaScript 的性能优化：加载和执行](https://developer.ibm.com/zh/articles/1308-caiys-jsload/)
当浏览器遇到body标签才算真正开始加载并渲染DOM。
js下载和执行时，DOM的加载和渲染同时被**阻塞**（由于JavaScript有可能会更改DOM Tree和Render Tree，因此同时被阻塞）。


# 网址重定向油猴脚本
```
// ==UserScript==
// @name         网址重定向
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  try to take over the world!
// @author       LFY
// 此处添加需要处理的网址
// @match        https://github.com/*
// @grant        none
// ==/UserScript==

(function() {
    'use strict';
    alert(window.location.host+window.location.hostname)
    // window.location.replace("https://github.webexp.me"+window.location.pathname);
    window.location.assign("https://github.webexp.me"+window.location.pathname);
    //对每个网站分别设置重定向
    if(window.location.host=='github.com'){
        alert("if!")
    }
    // Your code here...
})();
```
## location 方法

1. Location.assign()
该方法使浏览器加载并展示URL所指定的文档

2. Location.reload()
该方法用于重新加载当前页面，可以接受一个Boolean类型的参数，参数为true,强制从服务器重新获取，为false时从缓存中读取。默认值为false

3. Location.replace()
提供一个URL，使页面跳转到相应的URL，与location.assign()的区别是，location.replace()跳转后的页面不会保存在浏览器历史中，即无法通过返回按钮返回到该页面。

4. Location.toString()
获取当前页面的完整URL，相当于location.href

## location各字段
https://developer.mozilla.org/en-US/docs/Web/API/Location
https://www.w3schools.com/jsref/obj_location.asp

[用JS获取地址栏参数的方法](https://www.cnblogs.com/fishtreeyu/archive/2011/02/27/1966178.html)

完整的URL由这几个部分构成：
```
scheme://host:port/pathname?query#fragment 
```
示例：
```
http://www.maidq.com/index.html?ver=1.0&id=6#imhere
```
1. href 整个URL字符串
2. scheme:通信协议，如`http:`
3. host:hostname+port
4. hostname:主机名或IP地址
5. port:端口号
6. pathname:路径,如`/fisker/post/0703/window.location.html`
7. search:查询
用于给动态网页（如使用CGI、ISAPI、PHP/JSP/ASP/ASP.NET等技术制作的网页）传递参数，可有多个参数，用"&"符号隔开，每个参数的名和值用"="符号隔开。`?ver=1.0&id=6`
8. fragment:信息片断
`#`后面的字符串，用于指定网络资源中的片断。例如一个网页中有多个名词解释，可使用fragment直接定位到某一名词解释。(也称为锚点.)
如`#imhere`

## 获取url参数

javascript使用正则获取url中的某个参数：
```
&+url参数名字=值+&
```
```
//获取url中的参数
function getUrlParam(name) {
    var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)"); //构造一个含有目标参数的正则表达式对象
    var r = window.location.search.substr(1).match(reg);  //匹配目标参数
    if (r != null) return unescape(r[2]); return null; //返回参数值
}

```



## 百度直链获取脚本
1. [网盘助手](http://pan.newday.me/)
  https://greasyfork.org/zh-CN/scripts/378301-%E7%BD%91%E7%9B%98%E5%8A%A9%E6%89%8B
  推荐应用ID：`250528（官方）、265486、309847；266719、778750`
  [1] 下载速度慢：脚本只是方便大家生成下载链接，并不能解除下载速度的限制。所谓的提速，是指通过IDM等多线程工具来提高下载速度。遇到限速严重的情况，可以尝试 `更换应用ID` 来解决。
  [2] 下载提示`403`：可以尝试 `**更换应用ID**` 来解决。
  [3] 下载到一半提示要登录或者403：推测`下载链接是会失效`的（推测是6个小时），超过这个时间文件仍未下载好，就会提示这个错误。所以，下载大文件且下载速度慢的时候，建议使用客户端下载。
  [4] `不能下载压缩文件`：如果压缩文件超过一定大小（推测是300M），则提示下载失败，这个是百度的限制，脚本也是没办法。

2. [百度网盘直链下载助手](  https://github.com/syhyz1990/baiduyun)
  功能与原理与上一个类似。
  支持使用JSONRPC协议发送下载链接到Aria。（地址格式： http://localhost）
  实测非会员使用`aria2c不能起到加速效果`，需搭配会员。

**问题：**
直接点击显示403错误。

**解决方法：**
右键：使用IDM下载。

**其它：**

* SVIP会员使用第三方工具可加速。下载一定量后也有被限速风险。
* 使用Pandowload会快速触发账号限速。
* 百度云按照账号限速，每个账号下载了一定量后限速。
* 大量文件可使用远程Windows VPS登录百度云客户端下载（可搭配SVIP），避免占用本地资源。
* [临时共享会员](https://github.com/SunRelease/BaiduSVIP)


# 其它
[Bookmarklet 小书签](https://www.runningcheese.com/bookmarklet)也是使用的JavaScript。
可以参考[BookMarklet.html](../files/CheeseBookmarklet_2020-01-13.html)的一些使用方法。