---
title: Bash
tags:
  - [Linux]
categories: [Linux]
date: 2020-04-28 11:00:14
---
<font face="微软雅黑"></font>
<center> </center>

<!-- more -->

- [参考教程](#参考教程)
- [Shell 简介](#shell-简介)
  - [Shell脚本](#shell脚本)
  - [shell语法](#shell语法)
    - [命令替换](#命令替换)
    - [变量替换](#变量替换)
- [相关概念](#相关概念)
  - [文件描述符](#文件描述符)
  - [重定向](#重定向)
    - [重定向udp](#重定向udp)
  - [管道](#管道)
  - [环境变量](#环境变量)
  - [history历史命令](#history历史命令)
  - [bash历史命令](#bash历史命令)
    - [history时间戳](#history时间戳)


# 参考教程
1. [Quick Reference](https://quickref.me/)

2. [ShellGuide](https://github.com/qinjx/30min_guides/blob/master/shell.md)
3. [Learn bash in Y minutes](https://learnxinyminutes.com/docs/bash/)
4. [pure-bash-bible](https://github.com/dylanaraps/pure-bash-bible)
5. [Bash 脚本教程](https://wangdoc.com/bash/)

[重定向输入输出](https://segmentfault.com/a/1190000022100439)

# Shell 简介

shell是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。Ken Thompson的sh是第一种Unix Shell，Windows Explorer是一个典型的图形界面Shell。
Bourne Again Shell（/bin/bash） 最常用。

它不仅是一个功能强大的命令行接口,也是一个脚本语言解释器。我们将会看到， 大多数能够在命令行中完成的任务也能够用脚本来实现，同样地，大多数能用脚本实现的操作也能够在命令行中完成。

```
#!/bin/bash
cd ~
mkdir shell_tut
cd shell_tut

for ((i=0; i<10; i++)); do
    touch test_$i.txt
done

```
## Shell脚本

1. 作为可执行程序

    chmod +x test.sh
    ./test.sh

2. 作为解释器参数
这种运行方式是，直接运行解释器，其参数就是shell脚本的文件名，如：

    /bin/sh test.sh
    /bin/php test.php

这种方式运行的脚本，不需要在第一行指定解释器信息，写了也没用。

    常见的解释型语言都是可以用作脚本编程的，如：Perl、Tcl、Python、PHP、Ruby。
编译型语言，只要有解释器，也可以用作脚本编程，如C shell是内置的（/bin/csh），Java有第三方解释器Jshell，Ada有收费的解释器AdaScript。
如下是一个PHP Shell Script示例（假设文件名叫test.php）：

    #!/usr/bin/php
    <?php
    for ($i=0; $i < 10; $i++)
            echo $i . "\n";

执行：

    /usr/bin/php test.php
或者：

    chmod +x test.php
    ./test.php

## shell语法
### 命令替换
```
$( ) 与` ` (反引号) 都是用来做命令替换用(commandsubstitution)

` ` 基本上可用在全部的 unix shell 中使用，
```

### 变量替换

```

$var与${var} 用于变量替换。
 ${ } 会精确的界定变量名称的范围
```

# 相关概念
## 文件描述符
表述指向文件的引用。ANSI C中为 使用FILE结构体的指针。
与shell输入输出、文件读、文件写等绑定。



命令 | 说明
---|---
command > file | 将输出重定向到 file。
command < file | 将输入重定向到 file。
command >> file | 将输出以追加的方式重定向到 file。
n > file | 将文件描述符为 n 的文件重定向到 file。
n >> file | 将文件描述符为 n 的文件以追加的方式重定向到 file。
n >& m | 将输出文件 m 和 n 合并。
n <& m | 将输入文件 m 和 n 合并。
<< tag | 将开始标记 tag 和结束标记 tag 之间的内容作为输入。

0 是标准输入（STDIN），1 是标准输出（STDOUT），2 是标准错误输出（STDERR）。
这里的 2 和 > 之间不可以有空格，2> 是一体的时候才表示错误输出。

## 重定向
在执行一个命令之前，可以使用重定向操作符对该命令的输入、输出进行重定向。

重定向可以修改文件描述符对应的文件，从而在读写同一个文件描述符时，可以读写到其他文件。

重定向输入会把指定的文件描述符关联到以读模式打开的文件。那么读取指定文件描述符，就会读取文件内容。

重定向输出会把指定的文件描述符关联到以写模式打开的文件。那么写入内容到指定文件描述符，就会写入文件。

在重定向之前，指定的文件描述符对应一个已经打开的文件。
重定向之后，该文件描述符会对应另一个文件、或者对应另一个文件描述符。而另一个文件描述符也是对应另一个已经打开的文件。

当下面文件名用于重定向时，bash 会进行特殊处理：

```
/dev/fd/fd：如果 fd 是一个已经打开的文件描述符整数值，则 /dev/fd/fd 文件会复制到文件描述符 fd
/dev/stdin：复制到文件描述符 0，也可以写为 /dev/fd/0。文件描述符 0 一般对应标准输入
/dev/stdout：复制到文件描述符 1，也可以写为 /dev/fd/1。文件描述符 1 一般对应标准输入
/dev/stderr：复制到文件描述符 2，也可以写为 /dev/fd/2。文件描述符 2 一般对应标准错误输出
```

### 重定向udp
https://www.gnu.org/software/bash/manual/html_node/Redirections.html


**/dev/udp/host/port**
If host is a valid hostname or Internet address, and port is an integer port number or service name, Bash attempts to open the corresponding UDP socket.


## 管道

1. 通过ps命令获取对应程序的pid
`ps|grep dogcom|grep -v grep|awk 'print $1'`


2. 把获取的pid作为其他命令的输入
这里执行一个名字为test的C程序，需要把pid作为输入参数。
**方法1:**

```
./test `ps|grep dogcom|grep -v grep|awk 'print $1'`
```
**符号：\` \`**
名称：反引号，上分隔符
作用：反引号括起来的字符串被shell解释为命令行，在执行时，shell首先执行该命令行，并以它的标准输出结果取代整个反引号（包括两个反引号）部分

**方法2：**
        ps|grep dogcom|grep -v grep|awk 'print $1' | xargs ./test
**命令：xargs**
xargs是给命令传递参数的一个过滤器，也是组合多个命令的一个工具。它把一个数据流分割为一些足够小的块，以方便过滤器和命令进行处理。通常情况下，xargs从管道或者stdin中读取数据，但是它也能够从文件的输出中读取数据。



## 环境变量

export ：查看变量
echo $HISTCONTROL

1. 临时设置，该会话有效：export HISTCONTROL=ignorespace

2. 永久设置：/etc/profile  或 .bashrc中加入export HISTCONTROL=ignorespace ，然后执行source


## history历史命令

echo > .bash_history //清除保存的用户操作历史记录

history -cw //清除所有历史

不记录命令：
```
export HISTIGNORE='pwd:exit:fg:bg:top:clear:history:ls:uptime:df'
export HISTCONTROL=ignorespace

```

执行history中的命令：

```
$ history | tail -4
  179  rm -r temp/
  180  mkdir temp
  181  touch temp/test
  182  touch temp/test
  183  history | tail -5
$ !179:p
rm -r temp
$ !180
touch temp/test
```

## bash历史命令
https://www.gnu.org/savannah-checkouts/gnu/bash/manual/bash.html#History-Interaction

```
!n  # history中第n个命令

!ping  # 上次ping命令（带原参数）
!vim

!!    # 上次命令
!:0    # 上次命令本身
!*    #  上次所有参数

!^    # 上次第一个参数
!$    # 上次最后一个参数
!:n    # 上次第n个参数
!:1-2   # 参数切片

ls !cp:2 # 可与取历史命令组合使用

```



### history时间戳

```
~/.bashrc 或者 ~/.bash_profile
增加
export HISTTIMEFORMAT="%F %T  "

```
`.bashrc`可用于定义命令别名，等。
