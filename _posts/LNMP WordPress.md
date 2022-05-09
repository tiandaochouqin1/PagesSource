---
title: LNMP+Wordpress
date: 2019-12-05 19:26:40
tags: 
- [Linux]
- [WordPress]
- [LNMP]

categories: [Web]
---
<font face="微软雅黑"> </font>
<center>MySQL数据库的常用操作</center>

<!-- more -->

<!-- TOC -->

- [一键安装LNMP](#一键安装lnmp)
- [安装WordPress](#安装wordpress)
  - [配置 WordPress 数据库](#配置-wordpress-数据库)
  - [安装和配置 WordPress](#安装和配置-wordpress)
- [Wordpress不加载样式](#wordpress不加载样式)
  - [问题描述](#问题描述)
  - [解决](#解决)
- [开启SSL](#开启ssl)
- [Mysql重置密码](#mysql重置密码)
  - [Mysql常用操作](#mysql常用操作)
- [Wordpress插件](#wordpress插件)
  - [常用](#常用)
  - [wordpress无法安装插件](#wordpress无法安装插件)
  - [修改了本站地址无法打开网站](#修改了本站地址无法打开网站)
    - [php方法](#php方法)
    - [数据库更改](#数据库更改)
  - [本站地址带端口时无法frp](#本站地址带端口时无法frp)
  - [多域名访问且不会跳转主域名](#多域名访问且不会跳转主域名)
  - [无法在线更新](#无法在线更新)

<!-- /TOC -->

# 一键安装LNMP
使用[LNMP无人值守安装工具](https://lnmp.org/faq/v1-5-auto-install.html)安装lnmp1.6。
mysql密码为设置的密码。
1. 安装screen;
2. 执行：`screen -S lnmp` 创建screen会话。
3. 执行lnmp安装命令：`wget http://soft.vpser.net/lnmp/lnmp1.6.tar.gz -cO lnmp1.6.tar.gz && tar zxf lnmp1.6.tar.gz && cd lnmp1.6 && LNMP_Auto="y" DBSelect="2" DB_Root_Password="lnmp.org" InstallInnodb="y" PHPSelect="5" SelectMalloc="1" ./install.sh lnmp`

如果网络掉线，可以重新连接SSH，再执行 `screen -r lnmp` 就会看到你的lnmp安装进程。
有时候screen异常退出可能会提示状态为Attached，可以执行：`screen -D -r lnmp` 进行恢复。
有时候可能会创建了多个同名的screen会话，可以执行：`screen -ls` 查看对应会话的session id，然后使用`screen -D -r sessionid` 进行恢复。
# 安装WordPress

## 配置 WordPress 数据库
注意：根据 MariaDB 版本，设置用户身份验证方式有一定区别，具体步骤请参见 MariaDB 官网。

执行以下命令，进入 MariaDB。
        `mysql`
执行以下命令，创建 MariaDB 数据库。例如 “wordpress”。
       `CREATE DATABASE wordpress;` //**注意命令后的“；”**
执行以下命令，创建一个新用户。例如 “user”，登录密码为 123456。
        `CREATE USER 'user'@'localhost' IDENTIFIED BY '123456';`
执行以下命令，赋予用户对 “wordpress” 数据库的全部权限。
        `GRANT ALL PRIVILEGES ON wordpress.* TO 'user'@'localhost' IDENTIFIED BY '123456';`
执行以下命令，使所有配置生效。
        `FLUSH PRIVILEGES;`
执行以下命令，退出 MariaDB。
        `\q`

## 安装和配置 WordPress
LNMP一键脚本安装的目录在`/home/wwwroot/default/`；替换一下教程中的`/usr/share/nginx/html/`。

**安装WordPress**
WordPress 可从 WordPress 官方网站下载 WordPress 最新中文版本并安装，本教程采用 WordPress 中文版本。

执行以下命令，删除网站根目录下用于测试 PHP-Nginx 配置的index.php文件。
`rm -rf /usr/share/nginx/html/index.php`
依次执行以下命令，进入/usr/share/nginx/html/目录，并下载与解压 WordPress。

        cd /usr/share/nginx/html
        wget https://cn.wordpress.org/wordpress-5.0.4-zh_CN.tar.gz
        tar zxvf wordpress-5.0.4-zh_CN.tar.gz

**修改 WordPress 配置文件**
依次执行以下命令，进入 WordPress 安装目录，将wp-config-sample.php文件复制到wp-config.php文件中，并将原先的示例配置文件保留作为备份。

        cd /usr/share/nginx/html/wordpress
        cp wp-config-sample.php wp-config.php
执行以下命令，打开并编辑新创建的配置文件。

`vim wp-config.php`
按 “i” 切换至编辑模式，找到文件中 MySQL 的部分，并将相关配置信息修改为 配置 WordPress 数据库 中的内容。

        // ** MySQL settings - You can get this info from your web host ** //
        /** The name of the database for WordPress */
        define('DB_NAME', 'wordpress');

        /** MySQL database username */
        define('DB_USER', 'user');

        /** MySQL database password */
        define('DB_PASSWORD', '123456');

        /** MySQL hostname */
        define('DB_HOST', 'localhost');

修改完成后，按“Esc”，输入“:wq”，保存文件返回。

**验证 WordPress 安装**
在浏览器地址栏输入云服务器实例的公网 IP 加上 wordpress 文件夹，例如：
`http://192.xxx.xxx.xx /wordpress`
转至 WordPress 安装页，开始配置 WordPress。
根据 WordPress 安装向导提示输入以下安装信息，单击【安装 WordPress】，完成安装。

| 所需信息 | 说明 |
| --- | --- |
|站点标题|WordPress 网站名称。|
|用户名|WordPress 管理员名称。出于安全考虑，建议设置一个不同于 admin 的名称。因为与默认用户名称 admin 相比，该名称更难破解。|
|密码|可以使用默认强密码或者自定义密码。请勿重复使用现有密码，并确保将密码保存在安全的位置。|
|您的电子邮件|用于接收通知的电子邮件地址。|

现在可以用登录 WordPress 博客，并开始发布博客文章了。

# Wordpress不加载样式
## 问题描述
在digitalocean创建Snapshot后恢复到另外一台机器后，WordPress网站不能正常打开。
`http://ip` 可以正常进入。
`http://ip/wordpress` 加载速度慢，且不加载样式。

## 解决

1. 重装WordPress
未解决。

2. 更换数据库

仅进行此步骤即可解决问题。
将清除所有的网站数据，并进入重新安装WordPress的流程。

删除原来使用的数据库，新建新数据库并应用。
重新安装设置WordPress。

# 开启SSL
按照[腾讯云教程](https://cloud.tencent.com/document/product/400/35244)设置SSL。
发现访问网站首页显示https不安全，其它部分页面为安全。
原因主要是wordpress中图片或其它资源使用`http`未更改过来

[解决步骤](https://www.wpxiaobai.cn/how-to-fix-the-common-ssl-issue-in-wordpress/)：
1. 将`www.domain.com` 解析到`domain.com`，安装的证书为`domain.com`。
2. wp后台更改网站地址和wordpress地址为`https`;
3. Chrome调试模式查看有未正确加载的资源。发现背景图片未加载，同样在后台可更改为`https`;
4. 有一个主题的`custom.css`未正确加载，在目录新建一个相应的空文件。之后所有资源均正常获取，但仍显示不安全。
5. `更新主题`。直接删除原有主题文件，上传新主题。解决问题。



# Mysql重置密码

1. 跳过MySQL的密码认证过程，方法如下：
`vim /etc/my.cnf(注：windows下修改的是C:\ProgramData\MySQL\MySQL Server 8.0\my.ini)`
在[mysqld]后面任意一行添加“skip-grant-tables”用来跳过密码验证的过程，如下图所示：

2. 接下来我们需要重启MySQL：
`/etc/init.d/mysql restart`(有些用户可能需要使用/etc/init.d/mysqld restart)

3. 重启之后输入`mysql`即可进入mysql。
4. 接下来就是用sql来修改root的密码

        mysql> use mysql;
        mysql> update user set password=password("你的新密码") where user="root";
        mysql> flush privileges;
        mysql> quit
到这里root账户就已经重置成新的密码了。
5. 编辑`my.cnf`,注释掉刚才添加的内容，然后重启MySQL。

## Mysql常用操作

您可以通过键入以下内容快速检查可用的数据库：

`SHOW DATABASES;`
您的屏幕应该看起来像这样：

        mysql> SHOW DATABASES;
        +--------------------+
        | Database           |
        +--------------------+
        | information_schema |
        | mysql              |
        | performance_schema |
        | test               |
        +--------------------+
        4 rows in set (0.01 sec)
创建数据库非常简单：

 `CREATE DATABASE database name;`
在这种情况下，例如，我们将调用我们的数据库“事件”。

        mysql> SHOW DATABASES;
        +--------------------+
        | Database           |
        +--------------------+
        | information_schema |
        | events             |
        | mysql              |
        | performance_schema |
        | test               |
        +--------------------+
        5 rows in set (0.00 sec)
在MySQL中，最常用于删除对象的短语是Drop。 您将使用此命令删除MySQL数据库：

 `DROP DATABASE database name;`
如何访问MySQL数据库
一旦我们有一个新的数据库，我们可以开始填充信息。

第一步是在较大的数据库中创建一个新表。

让我们打开我们要使用的数据库：

 `USE events;`
以同样的方式检查可用的数据库，您还可以查看数据库包含的表的概述。

 `SHOW tables;`

# Wordpress插件
## 常用
Easy Updates Manager：管理更新。
UpdraftPlu：备份/恢复，支持云盘。
Better Search Replace：在WordPress数据库上运行搜索/替换。
Elementor Page Builder：可视化编辑wp界面。

## wordpress无法安装插件
安装时会提示登录ftp，但是仍然失败。
```
安装失败： 502 Bad Gateway 502 Bad Gateway nginx <!-- a padding to disable MSIE and Chrome friendly error page --> 
```
出现无法创建目录的确是权限的问题，但是不是目录读写的权限，而是用户组的问题。
想要下载插件的用户组为 web 用户组，用户名组名为 www（装 lnmp 环境的就是 www，可以在 ngnix.conf 中第一行查看），而此时 wordpress 用户组为 root，这样就不能创建目录了。ftp用户组也没有权限。
**解决方法：**
输入 ls -l wordpress 可以看到用户和用户组都是 root。
```
chown -R www:www my_wp_blog
```

其它方法，[下载](https://wordpress.org/plugins/)，手动上传插件。


## 修改了本站地址无法打开网站

### php方法
找到所使用主题的 `function.php`，在任意空白处加上下面两行代码：

```
update_option('siteurl','http://domain');    

update_option('home','http://domain');
```
重置后删除，避免以后无法更改。

### 数据库更改
进入主机cPanel管理页面，进入数据库栏内的“`phpMyAdmin` (MySQL 数据库管理程序)”。
进入数据库后，点击网站数据库“wp_options”表，编辑“option_name”的siteurl字段和home字段，修改所对应的 “option_value”值为正确的网址即可。如下图所示：

## 本站地址带端口时无法frp
通过frp远程(ip:port)访问时，访问管理界面时会被重定向为`ip:port`。除了首页，其它均会被重定向，无法访问。



## 多域名访问且不会跳转主域名
适合多服务器和多CDN接入
1，使用宝塔面板打开网站根目录

2，编辑wp-config.php

3，在数据库信息上面填写代码

如果使用任意域名访问
```
define('WP_SITEURL', 'http://' . $_SERVER['HTTP_HOST']);
define('WP_HOME', 'http://' . $_SERVER['HTTP_HOST']);

```
如果使用限定域名访问
```
$domain = array("www.qq.com", "www.qq.com", "www.qq.com");
if(in_array($_SERVER['HTTP_HOST'], $domain)){
define('WP_SITEURL', 'http://' . $_SERVER['HTTP_HOST']);
define('WP_HOME', 'http://' . $_SERVER['HTTP_HOST']);
}

```

## 无法在线更新
需要填写ftp信息。
原因：没有网站根目录的读写权限。