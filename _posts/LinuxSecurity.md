---
title: LinuxSecurity
tags:
  - [Linux]
categories: [Linux]
date: 2020-06-16 21:31:10
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->


# 安全设置
* 使用ssh_key（最安全）
* 禁用root
* 复杂用户名和密码（数字字母混合）
* 大数字的端口

## SSH基本设置
`/etc/ssh/sshd_config`文件（Centos7）:
```
Port 6666 #随意修改一个端口
PermitRootLogin no #禁止root登录
RSAAuthentication yes #RSA认证
PubkeyAuthentication yes #开启公钥验证
AuthorizedKeysFile .ssh/authorized_keys #验证文件路径
PasswordAuthentication no #禁止密码认证
PermitEmptyPasswords no #禁止空密码
UsePAM no #禁用PAM


/etc/init.d/ssh restart # 重启ssh服务
```

## 安全设置

```
# Logging
#SyslogFacility AUTH
LogLevel INFO

# Authentication:

LoginGraceTime 20
#PermitRootLogin prohibit-password
PermitRootLogin yes
#StrictModes yes
MaxAuthTries 3
MaxSessions 5

```

## 使用denyhosts
`yum install denyhosts`

[DenyHosts](http://denyhosts.sourceforge.net/index.html)是针对SSH服务器的一个基于日志的入侵预防安全工具，是用Python编写的。其通过监测身份验证登录日志中失败的登录尝试，屏蔽这些登录者的IP地址，从而预防对SSH服务器的暴力破解。

**手动安装**`2.6`版本（`2.10版本，配置不一样，没搞成`）。

```
解压并安装
安装服务器系统：CentOS 7.2
# 官网下载包安装
[root@tmp ~]# tar zxvf DenyHosts-2.6.tar.gz
[root@tmp ~]# cd DenyHosts-2.6
[root@tmp ~]# DenyHosts-2.6]# python setup.py install
配置
# 进入配置目录
[root@tmp ~]# cd /usr/share/denyhosts/
# 创建配置文件
[root@tmp denyhosts]# cp denyhosts.cfg-dist denyhosts.cfg 
# 修改服务文件名称
[root@tmp denyhosts]# cp daemon-control-dist daemon-control
# 提高安全级别，修改权限
[root@tmp denyhosts]# chown root daemon-control
[root@tmp denyhosts]# chmod 700 daemon-control
[root@tmp denyhosts]# vi denyhosts.cfg
SECURE_LOG = /var/log/secure   #ssh日志文件
# format is: i[dhwmy]
# Where i is an integer (eg. 7)
# m = minutes
# h = hours
# d = days
# w = weeks
# y = years
#
# never purge:
PURGE_DENY = 50m               #过多久后清除已阻止IP
HOSTS_DENY = /etc/hosts.deny   #将阻止IP写入到hosts.deny
BLOCK_SERVICE = sshd           #阻止服务名
PURGE_THRESHOLD =              #定义了某一IP最多被解封多少次。某IP暴力破解SSH密码被阻止/解封达到了PURGE_THRESHOLD次，则会被永久禁止；
DENY_THRESHOLD_INVALID = 1     #允许无效用户登录失败的次数
DENY_THRESHOLD_VALID = 10      #允许普通用户登录失败的次数
DENY_THRESHOLD_ROOT = 5        #允许root登录失败的次数
WORK_DIR = /usr/local/share/denyhosts/data #将deny的host或ip纪录到Work_dir中
DENY_THRESHOLD_RESTRICTED = 1 #设定 deny host 写入到该资料夹
LOCK_FILE = /var/lock/subsys/denyhosts #将DenyHOts启动的pid纪录到LOCK_FILE中，已确保服务正确启动，防止同时启动多个服务。
HOSTNAME_LOOKUP=NO            #是否做域名反解
ADMIN_EMAIL =                 #设置管理员邮件地址
DAEMON_LOG = /var/log/denyhosts #DenyHosts日志位置
配置服务及启动
```

创建启动服务连接
```
[root@tmp ~]# ln -s /usr/share/denyhosts/daemon-control /etc/init.d/denyhosts
# 添加启动项
[root@tmp ~]# chkconfig denyhosts on
# 查看启动像是否成功
[root@tmp ~]# chkconfig --list|grep denyhosts
# 启动denyhosts
[root@tmp ~]# service denyhosts start  # 或者 systemctl start denyhosts
# 查看后台进程
[root@tmp ~]# ps -ef|grep denyhosts
root      3098  3865  0 17:35 pts/1    00:00:00 grep --color=auto denyhosts
root     21121     1  0 Feb28 ?        00:00:01 python /usr/bin/denyhosts.py --daemon --config=/usr/share/denyhosts/denyhosts.cfg
```
**黑名单与白名单**：
```
vi /etc/hosts.allow 
vim /etc/hosts.deny

```

## fail2ban
https://blog.csdn.net/weixin_43507959/article/details/102763985


apt install fail2ban 

systemctl enable fail2ban

vi /etc/fail2ban/jail.local 新增配置

```
[DEFAULT]
# fail2ban忽略的IP
ignoreip = 127.0.0.1/8

# 客户端被禁止时长(秒)
bantime = 86400

# 客户端主机被禁止允许失败的次数
maxretry = 5

# 查找失败次数的时长（秒）
findtime = 3600

#日志修改检测机制（gamin、polling和auto这三种）
backend = polling

[ssh-iptables]
enabled = true
filter = sshd
action = iptables[name=SSH, port=ssh, protocol=tcp]
sendmail-whois[name=SSH, dest=my@gmail.com, sender=fail2ban@email.com]

```

然后： fail2ban-client reload

常用命令

```
tail -f -n 10 /var/log/fail2ban.log

sudo iptables --list -n

检验fail2ban状态（会显示出当前活动的监狱列表）

sudo fail2ban-client status

检验一个特定监狱的状态（例如ssh-iptables)

sudo fail2ban-client status ssh-iptables


解锁特定的IP地址

sudo fail2ban-client set ssh-iptables unbanip 192.168.1.8

```

# 查看与伪造记录
## 查看
lastb

cat /var/log/secure

统计有多少IP访问：
```
grep "Failed password for invalid" /var/log/secure | awk '{print $13}' | sort | uniq -c | sort -nr | more
```

统计有多少用户名尝试登录(root用户名统计方式不一样，此处未做统计):
```
grep "Failed password for invalid" /var/log/secure | awk '{print $11}' | sort | uniq -c | sort -nr | more
```

## 删除与伪造登陆记录

[渗透测试TIPS之删除、伪造Linux系统登录日志](https://www.freebuf.com/articles/system/141474.html)

Linux中涉及到登录的二进制日志文件有

```
/var/run/utmp

/var/log/wtmp

/var/log/btmp

/var/log/lastlog

```

其中 utmp 对应w 和 who命令； wtmp 对应last命令；btmp对应lastb命令；lastlog 对应lastlog命令

经查Linux man 手册，

```
/var/run/utmp
/var/log/wtmp
/var/log/btmp

```

的二进制格式都是一样的， 我们姑且称之为xtmp 格式

而/var/log/lastlog 文件的格式与之不同，需单独分析，下面我们先分析xtmp的文件格式吧，这里以utmp 格式为例



https://owasp.org/www-project-top-ten/


# Top 10 Web Application Security Risks

1. Injection.
    Injection flaws, such as SQL, NoSQL, OS, and LDAP injection, occur when untrusted data is sent to an interpreter as part of a command or query. The attacker's hostile data can trick the interpreter into executing unintended commands or accessing data without proper authorization.
2. Broken Authentication. 
   Application functions related to authentication and session management are often implemented incorrectly, allowing attackers to compromise passwords, keys, or session tokens, or to exploit other implementation flaws to assume other users’ identities temporarily or permanently.
3. Sensitive Data Exposure. 
   Many web applications and APIs do not properly protect sensitive data, such as financial, healthcare, and PII. Attackers may steal or modify such weakly protected data to conduct credit card fraud, identity theft, or other crimes. Sensitive data may be compromised without extra protection, such as encryption at rest or in transit, and requires special precautions when exchanged with the browser.
4. XML External Entities (XXE). 
   Many older or poorly configured XML processors evaluate external entity references within XML documents. External entities can be used to disclose internal files using the file URI handler, internal file shares, internal port scanning, remote code execution, and denial of service attacks.
5. Broken Access Control. 
   Restrictions on what authenticated users are allowed to do are often not properly enforced. Attackers can exploit these flaws to access unauthorized functionality and/or data, such as access other users’ accounts, view sensitive files, modify other users’ data, change access rights, etc.
6. Security Misconfiguration.
    Security misconfiguration is the most commonly seen issue. This is commonly a result of insecure default configurations, incomplete or ad hoc configurations, open cloud storage, misconfigured HTTP headers, and verbose error messages containing sensitive information. Not only must all operating systems, frameworks, libraries, and applications be securely configured, but they must be patched/upgraded in a timely fashion.
7. Cross-Site Scripting XSS.
    XSS flaws occur whenever an application includes untrusted data in a new web page without proper validation or escaping, or updates an existing web page with user-supplied data using a browser API that can create HTML or JavaScript. XSS allows attackers to execute scripts in the victim’s browser which can hijack user sessions, deface web sites, or redirect the user to malicious sites.
8. Insecure Deserialization.
    Insecure deserialization often leads to remote code execution. Even if deserialization flaws do not result in remote code execution, they can be used to perform attacks, including replay attacks, injection attacks, and privilege escalation attacks.
9.  Using Components with Known Vulnerabilities.
     Components, such as libraries, frameworks, and other software modules, run with the same privileges as the application. If a vulnerable component is exploited, such an attack can facilitate serious data loss or server takeover. Applications and APIs using components with known vulnerabilities may undermine application defenses and enable various attacks and impacts.
10. Insufficient Logging & Monitoring. 
    Insufficient logging and monitoring, coupled with missing or ineffective integration with incident response, allows attackers to further attack systems, maintain persistence, pivot to more systems, and tamper, extract, or destroy data. Most breach studies show time to detect a breach is over 200 days, typically detected by external parties rather than internal processes or monitoring.

[owasp top 10](../files/how-akamai-augments-your-security-practice-to-mitigate-the-owasp-top-10-risks.pdf)

# HTTP Methods
https://www.cnblogs.com/machao/p/5788425.html
https://segmentfault.com/a/1190000013182974