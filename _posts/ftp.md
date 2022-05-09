---
title: ftp
tags:
  - [网络]
categories: [网络]
date: 2020-05-08 16:54:52
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->



# FTP原理

用户通过客户机程序向服务器程序发出命令，服务器程序执行用户所发出的命令，并将执行的结果返回到客户机。
客户机程序代表用户接收到这个文件，将其存放在用户目录中。
FTP 会话属于复合 TCP 连接，要用到**两个TCP连接**，一个是**命令链路**，用来在FTP客户端与服务器之间传递命令；另一个是**数据链路**，用来上传或下载数据。
## ftp sftp scp 比较

FTP（File Transfer Protocol）是文件传输协议，使用 TCP 来传输。FTP 是一个 C/S（Client/Server）架构的网络服务。

SFTP（Secure File Transfer Protocol）：安全文件传送协议。可以为传输文件提供一种安全的加密方法。**SFTP 为 SSH 的一部份**。

SCP（Secure Copy）：SCP 就是 Secure copy，是用来进行远程文件复制的，并且整个复制过程是加密的。数据传输使用 ssh，并且和使用和 ssh 相同的认证方式，提供相同的安全保证。
Windows可使用开源软件[winscp](https://winscp.net/)客户端。


`建议使用sftp`。

方法| 协议| 特点
---------|----------|---------
FTP | TCP | 明文传输
SFTP | SSH，即不需要外安装ftp服务 | 可靠性高，可断点续传
SCP | SSH | 需要指定详细目录，不可断点续传


## 主动模式和被动模式

### 主动模式（Standard，PORT）

主动模式的工作顺序：
- Client 先和 Server 通过 21 端口建立连接；
- Client 向 Server 发送指令，指令中包含了 Client 要通过 N 号端口来传输什么数据；
- Server 打开自己的 20 端口，去 主动连接 Client 的 N 号端口来传输数据。
缺点：
- 主动 FTP 对 FTP 服务器的管理有利，但 对客户端的管理不利。
- 因为 FTP 服务器企图与客户端的高位随机端口建立连接，而这个端口很有可能被客户端的防火墙阻塞掉。
为了解决服务器发起到客户的连接的问题，人们开发了一种不同的 FTP 连接方式。当客户端通知服务器它处于被动模式时才启用。

### 被动模式（Passive，PASV）
被动模式的工作顺序：
- 建立连接的方式和主动模式相同；
- 建立连接后，与主动方式不同，Client 不会提交 PORT 命令并允许 Server 来回连它的数据端口，而是提交 PASV 命令。
- Server 会开启一个任意的非特权端口（P > 1024），并发送 PORT P 命令给 Client。
- Client 发起从本地端口到 Server 的端口 P 的连接用来传送数据。
缺点：
- 被动 FTP 对 FTP 服务器的管理不利，但对客户端的管理有利。
- 因为客户端要与服务器端建立两个连接，其中一个连到一个大于 1024 的随机端口，而这个端口很有可能被服务器端的防火墙阻塞掉。

选择用PASV方式还是PORT方式登录FTP服务器，选择权在FTP客户端，而不是在FTP服务器。



## ftp/sftp命令

`get/put [-r] src_file dst_file`
- 连接
```
ftp >
  open ip

```

- 下载文件
```
get filename   //下载此文件到本地当前目录。 可以!dir查看
mget *.xls //可以使用通配符上传多个文件

```
- 文件夹命令
```
get -r filename
```


- 上传文件
```
put filename
mput  *.log//可以使用通配符

向vsftp服务器上传文件报“550 Permission denied”
解决：修改服务器/etc/vsftpd.conf
 write_enable=YES

```

- 删除文件
```
delete filename
```
- 退出
```
quit
bye
```

- 其它命令
支持大部分文件管理命令。
```
help //查看命令

```

## 用户

1. 匿名用户
 “ftp” 或者 “anonymous”。
  这类用户是指在 FTP 服务器中没有指定帐户，但是其仍然可以进行匿名访问某些公开的资源。
  为什么要用匿名用户？
  - 默认情况下，FTP 服务器会把建立的所有帐户都归属为真实用户。
  - 但是，这往往不符合企业安全的需要。给其他用户所在的空间带来一定的安全隐患

2. 本地（真实）用户
是 FTP 服务器本机的系统用户账号。
当这类用户登录 FTP 服务器的时候，其默认的主目录就是其帐号命名的目录。
当开放本地账户的时候，我们往往会做 chroot（禁锢家目录）来保证安全。

3. 虚拟用户
账号信息存放在独立的文件或者数据库内。
不是本地账号，不能登陆操作系统，安全性比较高。
虚拟用户的特点：
- 只能访问服务器为其提供的 FTP 服务，而不能访问系统的其它资源。
- 所以，如果想让用户对 FTP 服务器站内具有写权限（可以上传数据到服务器），但又不允许访问系统其它资源，可以使用虚拟用户来提高系统的安全性。
- 创建虚拟用户需要使用 pam。


# ftp 软件
## vsftpd

```
sudo apt install vsftpd
sudo vi /etc/vsftpd.conf 
service restart vsftpd

```

vsftpd：主配置文件。
ftpusers: 指定哪些用户不能访问 FTP 服务器, 这里的用户包括 root 在内的一些重要用户。

### 创建 FTP 连接用户
ftp专用用户，设置默认路径。

```
useradd ftpusr  //创建用户ftpuser

usermod -s /sbin/nologin ftpuser  /设置用户只能ftp不能登入

passwd ftpusr   /设置用户密码


usermod -d /mnt ftpuser   /修改用户的家目录，但是仍能够切换到其它目录

```


## pure-ftpd
由于ftp协议用到两个TCP连接，即需要至少使用两个不同端口。默认认证端口为21。
更换默认的21端口后，访问地址为`ip:端口`

pureftp使用的数据传输端口为20000-30000中的随机端口。
`PassivePortRange      20000 30000`

PureFtpd配置文件：`/usr/local/pureftpd/etc/pure-ftpd.conf`
PureFtpd MySQL配置文件：`/usr/local/pureftpd/pureftpd-mysql.conf`


### 更改密码
登录phpmyadmin，打开pureftpd 数据库ftp 的  admin 表 ，点击Administrator用户名前面的编辑，在password后面的函数中选择md5，值哪一项输入你要设置的密码。点执行即可。

## pureftpd和vsftpd比较
Pure-FTPD 和 VSFTPD 都是很优秀的的FTP建站软件，其区别主要在于两者的`可扩展性`。


1. VSFTPD  体积小巧，配置比较简单，安全和性能比较出众，但在数据库支持和扩展性方面不佳。

2. pure-ftpd 是一款功能强大、性能稳定、安全可靠、使用简单等优点，并能够进行web管理的ftp server软件。
 
pure-ftpd 的配置文件，相对简单，支持多种数据库，还可以对每用户独立的上传下载流量进行限制，对IP地址进行限制、对用户目录文件数及大小单独限制等。
它与 LDAP、TLS、PostgreSQL、MySQL等工具结合，可实现更多扩展应用，表现出强大的功能。

比如，Pure-FTPD 与数据库结合后，可以实现如下功能：
- 用php编写前台页面管理用户，让用户自己修改密码。这个可以应用于虚拟主机管理界面中；
- 将用户数据与论坛或网站的用户数据表整合，可以在论坛或网站中实现 FTP 功能；
- 将用户数据与邮件服务的用户数据表整合，可以实现邮件系统中的网盘功能；

