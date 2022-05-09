---
title: Docker and Nextcloud
tags:
  - [云盘]
  - [树莓派]
  - [Docker]
categories: [网络]
date: 2020-06-21 20:50:17
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->

# Docker
树莓派上安装。
[Nexcloud Docker](https://hub.docker.com/_/nextcloud/).可自定义数据存储的目录。
[Docker入门教程](https://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)

## 国内镜像源
```
vi /etc/docker/daemon.json 
```
```
{
 "registry-mirrors": ["https://registry.docker-cn.com",

                               "http://hub-mirror.c.163.com" ,

                               "https://kfwkfulq.mirror.aliyuncs.com"

                           ]
} 
```

```
service docker restart
```

## 容器基本操作
**拉取镜像与创建容器：**
```
docker pull nextcloud
docker run -d -p 8080:80 nextcloud
```

**container操作的是ID**，使用`docker ls -a`查看。也可自定义容器名，操作容器名。
部分命令中的`container`字符可不要。

**启动容器：**
`docker container run  [containerID]`是新建容器，每运行一次，就会新建一个容器。
`docker container start  [containerID]`用来启动已经生成、已经停止运行的容器文件。

```
docker container start  nextcloud 
```

`--name container_name`参数指定名称。
`docker rename old new`:重命名容器。

**停止容器：**
`container stop [containerID]`：发出 SIGTERM 信号，然后过一段时间再发出 SIGKILL 信号。
`docker container kill [containerID]`：发出SIGKILL 信号。
用程序收到 SIGTERM 信号以后，可以自行进行收尾清理工作，但也可以不理会这个信号。如果收到 SIGKILL 信号，就会强行立即终止，那些正在进行中的操作会全部丢失。

`docker container cp [containID]:[/path/to/file] .`:从正在运行的 Docker 容器里面，将**文件拷贝**到当前目录。
`docker container logs [containerID]`：查看容器的输出。
`docker container exec -it [containerID] bash`：进入正在运行的容器。


## 容器开机自启

在运行docker容器时可以加如下参数来保证每次docker服务重启后容器也自动重启：

```
docker run --restart=always
```
如果已经启动了则可以使用如下命令：
```
docker update --restart=always <CONTAINER ID>

```
**--restart** 具体参数值详细信息：
```
no - 容器退出时，不重启容器；
on-failure - 只有在非 0 状态退出时才重新启动容器；
always - 无论退出状态是如何，都重启容器；
```
使用 on - failure 策略时，指定 Docker 将尝试重新启动容器的最大次数。默认情况下，Docker 将尝试永远重新启动容器。
```
docker run --restart=on-failure:10 nextcloud
```


## 更新容器
[自动更新 Docker 镜像与容器](https://www.jianshu.com/p/67ca0b521154)

**容器（container）与程序数据（volume）是相互独立的**。可以通过执行以下操作来随时替换app容器：

```
docker pull mysql
docker stop my-mysql-container
docker rm my-mysql-container
docker run --name=my-mysql-container --restart=always \
  -e MYSQL_ROOT_PASSWORD=mypwd -v /my/data/dir:/var/lib/mysql -d mysql
```

###  Persistent data
The [Nextcloud ](https://hub.docker.com/_/nextcloud/)installation and all data beyond what lives in the database (file uploads, etc) is stored in the unnamed docker volume volume` /var/www/html`. The docker daemon will store that data within the docker directory `/var/lib/docker/volumes/....` That means your data is saved even if the container crashes, is stopped or deleted.

A named Docker volume or a mounted host directory should be used for upgrades and backups. To achieve this you need one volume for your database container and one for Nextcloud.

Nextcloud:

/var/www/html/ folder where all Nextcloud data lives

```
$ docker run -d \
-v nextcloud:/var/www/html \
nextcloud
Database:

```
/var/lib/mysql MySQL / MariaDB Data

/var/lib/postgresql/data PostgreSQL Data

```
$ docker run -d \
-v db:/var/lib/mysql \
mariadb
```
## docker-watchtower自动更新
https://p3terx.com/archives/docker-watchtower.html
本身已被打包为容器镜像
```
docker run -d \
    --name watchtower \
    -v /var/run/docker.sock:/var/run/docker.sock \
    containrrr/watchtower
```
官方给出的默认启动命令在长期使用后会堆积非常多的标签为 none 的旧镜像，命令末尾加入` --cleanup` 选项，这样每次更新都会把旧的镜像清理掉，可以简写为 `-c`。
想更新 `nginx、redis `这两个容器，我们可以把容器名称追加到启动命令的最后面
`--interval, -i - `设置更新检测时间间隔，单位为秒。默认5分钟。

**选择性自动更新**
可自定义更新列表、容器自动更新特征、更新频率

## 数据卷
docker 提供的一种高级的用法。

数据卷：就是一个正常的容器，专门用来提供数据卷供其它容器挂载的。

看示例：

```
docker run -v /home/dock/Downloads:/usr/Downloads  --name dataVol ubuntu64 /bin/bash

```
创建一个普通的容器。用 --name 指定一个名。
再创建一个新的容器，来使用这个数据卷。
```
docker run -it --volumes-from dataVol ubuntu64 /bin/bash
```
--volumes-from 用来指定要从哪个数据卷来挂载数据。

# Nextcloud Docker
## 挂载SMB
插件中启用外部存储。
设置中添加存储页面。提示`“smbclient” 未安装。无法挂载 "SMB / CIFS", "SMB / CIFS 使用 OC 登录信息"。请联系管理员安装。`：
进入dockers
```
docker exec -it nextcloud bash
```
安装nano，更换为[清华软件源](https://mirrors.tuna.tsinghua.edu.cn/help/debian/)。
```
apt install smbclient libsmbclient-dev
pecl install smbclient
docker-php-ext-enable smbclient
```
退出，重启容器。

挂载的目录在容器中，需要进入查看。

## 挂载本机存储
nextcloud挂在本地（容器中）：
进入docker查看当前目录。
`nextcloud->设置->管理->外部存储`，配置中填入本地目录，目录不存在则无法添加。

在容器中挂载本机目录：
```
docker run -it -v /home/dock/Downloads:/usr/Downloads ubuntu64 /bin/bash
```

由于已运行后无法直接增加挂载，故重新部署。
```
docker run -it -d -v /home/pi/nextcloud_local:/home/local_file  -p 8080:80 --name nextcloud --restart=always  nextcloud
```
**上传提示无权限**
需要将`/home/local_file`权限设置为与`/var/lib/docker/volumes/volume——ID/_data/data`一样：
```
chown -R www-data:www-data nextcloud_local/
```

## 不信任的域名访问

```
Access through untrusted domain
Please contact your administrator. If you are an administrator, edit the "trusted_domains" setting in config/config.php like the example in config.sample.php.
```
`/var/lib/html/config/config.php`添加：
```
'trusted_domains' => 
  array (
    0 => '192.168*',
    1 => '***',
  ),
```
## 修改映射端口
先停止container和docker。
```
vi /var/lib/docker/containers/containerID/hostconfig.json 
```

## nextcloud容器数据目录
在宿主机的默认目录为：`/var/lib/docker/container/`。
docker中的目录为：
Nextcloud:`/var/www/html/` folder where all Nextcloud data lives
Database:
`/var/lib/mysql` MySQL/MariaDB Data
`/var/lib/postgresql/data` PostgreSQL Data

## 修改docker目录
查看当前docker挂载目录：
```
docker inspect nextcloud|grep Mount -A 20
```

1. 修改配置文件（测试无效）

```
停止 docker 服务（关键，修改之前必须停止 docker 服务）
`vim /var/lib/docker/containers/container-ID/config.v2.json`
修改配置文件中的目录位置，然后保存退出
启动 docker和container
```
2. 提交现有容器为新镜像，然后指定新参数运行
使用已commit的镜像运行，需要重新开始配置nextcloud。

3. export 容器为镜像，然后 import 为新镜像，指定新参数
import的为容器，运行时必须带[额外的参数](https://blog.csdn.net/qq_37212970/article/details/84379926)。
具体的 command 需要在导出容器的时候通过 docker ps 查看到。看完整的 command 的内容： docker ps  --no-trunc
原有的nextcloud环境变量丢失，无法正常运行。

4. 软链接的方式来移动数据到其它盘，启动Docker时发现存储目录依旧是/var/lib/docker，但是实际上是存储在数据盘的。

docker所在盘容量不够时：
1. 修改 docker daemon 的启动参数 -g, --graph=""
2. 挂载新目录到docker根目录

# Wordpress docker
在树莓派上官方 **mysql** 镜像无法使用，因为树莓派的架构为 arm。
尝试了多个第三方的镜像，不能正常使用。
改用MariaDB。MariaDB是MySQL的开源分支版本。
[MySQL与MariaDB核心特性比较](https://www.cnblogs.com/zhjh256/p/11237120.html)

## MariaDB
[参考1](https://www.cnduf.com/83.html)
[参考2](https://segmentfault.com/a/1190000022340163)
```
docker pull wordpress
docker pull mariadb
```
创建并运行MariaDB容器，把本地数据目录映射到容器中的`/var/lib/mysql`目录，并把docker的3306端口映射到主机地址的3307端口，要注意使用`-e MYSQL_ROOT_PASSWORD`来指定MariaDB root用户初始密码
```
docker run --name mariadb -p 3307:3306 -v /docker/mariadb/var/lib/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=8*** -d mariadb
```

登入MariaDB容器环境,创建数据库和数据库维护用户（也可在安装WP页面设置）

```
docker exec -it mariadb bash
mysql -u root -p

create database wordpress character set utf8 collate utf8_general_ci;
create user wordpress_user identified by 'XXXXXXXXX';
grant all privileges on wordpress.* to wordpress_user;
flush privileges;
```

```
docker run --name wordpress -p 8081:80 --link mariadb:mysql -d wordpress
```
然后进入`http://ip:8081`，安装WordPress。

### docker run --link 的作用
`docker run --link` 可以用来链接 2 个容器，使得源容器（被链接的容器）和接收容器（主动去链接的容器）之间可以互相通信，并且接收容器可以获取源容器的一些数据，如源容器的环境变量。

## Docker-Compose
是 Docker 的一种**编排服务**，定义并运行复杂应用的工具，可以让用户在集群中部署分布式应用。
服务 (service)：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例。
项目 (project)：由一组关联的应用容器组成的一个完整业务单元，在 docker-compose.yml 文件中定义。

[Docker-compose安装WP](https://cloud.tencent.com/developer/article/1592741)


# Docker Whoogle
Self-hosted, ad-free, privacy-respecting Google metasearch engine
```
docker pull benbusby/whoogle-search
docker run --publish 5000:5000 --detach --name whoogle-search benbusby/whoogle-search:latest
```

## Google 已失效
https://hub.docker.com/r/starjason/docker-google-mirror

其实这只是强大的Google Filter Module的一个容器而已啦

```
git clone https://github.com/yifeikong/docker-google-mirror
cd docker-google-mirror
docker build -t google-mirror .
docker run -d -p 80:80 google-mirror
```




# jd

[京东挂机 青龙面板的安装与使用以及互助+Cookie 的获取](https://yoouu.cn/jd-qinglong/)
  
https://github.com/whyour/qinglong

https://github.com/zero205/JD_tencent_scf

1. docker安装qinglong
2. 下载脚本到docker
3. 面板配置cookies
4. 启动脚本


```
docker run -dit \
  --name QL \
  --hostname QL \
  --restart always \
  -p 51057:5700 \
  -v $PWD/QL/config:/ql/config \
  -v $PWD/QL/log:/ql/log \
  -v $PWD/QL/db:/ql/db \
  -v $PWD/QL/scripts:/ql/scripts \
  -v $PWD/QL/jbot:/ql/jbot \
  whyour/qinglong:latest
```