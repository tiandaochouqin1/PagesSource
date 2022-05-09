---
title: VPC
date: 2019-11-11 20:05:55
tags: VPS
categories: VPS
---
<font face="微软雅黑"> </font>
<center>云服务器</center>

<!-- more -->
# 腾讯实验室的教程
[腾讯实验室](https://cloud.tencent.com/developer/labs)有各种优质教程。
[阿里云开放实验室](https://edu.aliyun.com/lab/courses)
[阿里云开发者社区](https://developer.aliyun.com/)

[腾讯静态网站托管](https://cloud.tencent.com/document/product/1210/43365)

- [Centos 搭建宝塔 Linux 控制面板、搭建 Wordpress](https://cloud.tencent.com/developer/labs/lab/10425)
- [基于 typecho 搭建个人博客系统](https://cloud.tencent.com/developer/labs/lab/10445)
- [搭建 Docker 环境](https://cloud.tencent.com/developer/labs/lab/10054)
- [基于 CentOS 搭建 Hexo 个人博客](https://cloud.tencent.com/developer/labs/lab/10379)
- [搭建 Git 服务器](https://cloud.tencent.com/developer/labs/lab/10045)
- [CentOS 搭建 Aria2 及 AriaNg 实现多种文件的离线下载](https://cloud.tencent.com/developer/labs/lab/10428)


# 登录失败解决方法
**环境：**腾讯云 VPC S4，CentOS 7.5 64

**问题：**
加载秘钥后解绑秘钥，发现无法登录。
使用控制台标准登录方式时，提示：实例鉴权失败，请确认实例已启用密码鉴权并且账号密码正确。
使用SSH登录时，提示：Access Denied。

**解决方式：**

1. 修改sshd_config文件，未解决问题：
    vim /etc/ssh/sshd_config 修改 sshd_config文件中 PasswordAuthentication yes。
    然后重启服务： service sshd restart

2. 重置密码：解决问题。


# 云存储oss

`使用VPS搭建私有云，对接对象存储。`
## 腾讯COS
对象存储（Object Storage Service，简称OSS）

搭建后下载流量走VPS？国内VPS带宽太小，且COS下行流量太贵。
解决方法：
1. 想办法设置nextcloud 对外直接提供 oss 的公网地址,可解决服务器带宽问题。
2. FRP P2P模式，可能解决服务器流量与带宽问题；
3. 必要时使用内网VPS下载大量数据，nextcloud用于预览与管理。
4. 选择按流量计费的国内VPS。

阿里云的OSS与腾讯云价格及定价策略均相近。阿里云**教程更多**。

基本满足存储个人相册和少量私密文件备份的要求。
若产生大量下行流量，可选择CFS服务，或使用网盘。
**价格计算器：**
标准存储：20G空间+2G外网下行=4元/月。

## 对象存储

[腾讯云对象存储 COS](https://cloud.tencent.com/document/product/436/6222)
存储类型对比

|对比项|标准存储（多AZ）|标准存储|低频存储|归档存储|
|---|---|---|---|---|
|服务可用性|99.99%|99.95%|99.9%|99.9%|
|响应|毫秒级|毫秒级|毫秒级|需提前申请恢复|
|对象最小计量大小|128KB|按对象实际大小计算|64KB|64KB|
|最短计费时间|不限制|不限制|30天|90天|
|支持地域|广州地域|全部地域|全部地域|只适用于公有云地域|
|**存储费用**|较高|标准|较低|极低|
|数据取回费用|不收取|不收取|较低|较高|
|**读写请求费用**|极低|极低|较低|极低（数据需恢复到标准存储）|

计费方式分为按量计费和资源包付费两种
[计费项](https://cloud.tencent.com/document/product/436/6239)包括：存储容量费用、请求费用、数据取回费用、流量费用和管理功能费用

|存储类型 | 存储容量费用（元/GB/月） | 读/写请求费用（元/万次）  | 数据取回费用（元/GB） | 外网下行流量 | CDN 回源流量 | 跨地域复制流量|
|---|---|---|---|---|---|---|
|标准存储 | 0.099 | 0.01 |  0 | 0.5 | 0.15 | 0.5|
|**低频存储** | 0.08 | 0.05 | 0.02 | 0.5 | 0.15  | 0.5|
|归档存储 | 0.03 | 0.01 | 0.06-0.2 | 0.5（需恢复才适用） | 不适用 | 不适|

数据取回量：标准存储无此费用。
https://cloud.tencent.com/document/product/436/30747
**外网下行流量：**外网下行流量是数据通过互联网从 COS 传输到客户端产生的流量。直接通过对象链接下载对象或通过静态网站访问节点浏览对象产生的流量为外网下行流量，对应费用为外网下行流量费用。
**搭配CDN加速才划算**
开启 CDN 加速后为什么会产生外网下行流量？
开启 CDN 加速后，如果您仍使用 COS 的源站域名（格式如<BucketName-APPID>.cos.<region>.myqcloud.com）访问 COS 上的文件，则依然会产生外网下行流量费用。COS **建议您使用 CDN 加速域名访问文件**，这样将仅产生 CDN 回源流量
[内网流量免费](https://cloud.tencent.com/document/product/436/31315#.E5.86.85.E7.BD.91.E4.B8.8E.E5.A4.96.E7.BD.91.E8.AE.BF.E9.97.AE)：相同地域内腾讯云产品访问，将会自动使用内网连接，产生的内网流量不计费。

## 文件存储 CFS
只收取存储费用，`0.35元/GB/月`起可用。
文件存储适用场景
1. 高性能计算HPC
大量高性能计算节点可以基于文件存储，并发进行数据处理
2. 视频/图像处理
文件存储提供支持NFS、SMB协议延迟、高吞吐的存储服务，适用于视频/图像处理场景
3. 内容管理及Web服务
适用于容器、网站/APP、在线发行、存档等各种应用的数据存储

## 使用

COSBrowser APP:
简单的交互轻松实现对 COS 资源的查看、传输和管理。桌面端和移动端两种。快捷上传下载，`预览`。
[Hexo+NexT+腾讯对象存储COS实现相册](http://blog.fiftykg.com/hexo/hexo%E7%9B%B8%E5%86%8C%E5%8A%9F%E8%83%BD%E5%AE%9E%E7%8E%B0.html)

# 静态网站
## 部署
[腾讯云静态网站托管](https://cloud.tencent.com/document/product/1210/43365)
部署HUGO等其它静态网站同理。

1. 安装`hexo`和`tcb`：`npm i -g @cloudbase/cli hexo-cli`。
2. 初始化hexo项目：`hexo init`。
3. 腾讯云->开通云开发环境->开通静态网站托管.
4. 使用 CLI 部署 Hexo: `github pages工程目录下，public目录`
  ```
  tcb login //认证腾讯云账号
  cd public
  tcb hosting:deploy ./ -e EnvID  //指定环境ID
  ```
5. 自定义域名及ssl证书。

## 价格
### 按量计费方式
网站托管 

资源细项 | 售价（元）
------|------
容量（GB/天） | 0.0043
流量（GB） | 0.21

目前25M，一次访问约2-5M。
```
容量：0.1G*365（天）*0.0043=0.15695 元
流量：100（倍）*0.1G*0.21=2.1 元
```

### 流量包和容量包

资源包 | 资源细项 | 资源包量 | 价格（元） | 有效期（月）
----|------|------|-------|-------
网站托管流量包 | 网站托管流量 | 100GB | 18.6 | 3
网站托管容量包 | 网站托管存储容量 | 50GB | 18 | 3

## 其它
每次部署似乎会全量上传文件，而不是像像Github Pages那样只上传更改。