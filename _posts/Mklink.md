---
title: 软链接与硬链接
tags:
  - Windows
  - Linux
  
categories: Windows
date: 2020-04-05 16:59:56
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->

[Windows 中的 mklink 命令](https://liam.page/2018/12/10/mklink-in-Windows/)
[Windows下mklink使用, 硬链接, 软链接和快捷方式的区别](https://www.cnblogs.com/heqichang/archive/2012/04/26/2471774.html)

# mklink简介
GUI工具：
[FolderMove](https://foldermove.com/)
[Link Shell Extension](https://schinagl.priv.at/nt/hardlinkshellext/hardlinkshellext.html)

`mklink [[/d] | [/h] | [/j]] <Link> <Target>`
mklink 命令需要使用`管理员`权限，在 `cmd.exe` 中运行。

```
创建符号链接。

MKLINK [[/D] | [/H] | [/J]] Link Target

        /D      创建目录符号链接。默认为文件符号链接。
        /H      创建硬链接而非符号链接。
        /J      创建目录联接。
        Link    指定新的符号链接名称。
        Target  指定新链接引用的路径(相对或绝对)。
```

# 参数

|使用方式|适用于|快捷方式小箭头|
|---|---|---|
|不带参数|文件|有|
|/D|文件夹|有|
|/J|文件夹|有|
|/H|文件|无|

`从上到下的行为从越来越像快捷方式到越来越像两个独立的文件夹。`

目录联接在创建时会自动引用目标目录的绝对路径，而符号链接允许相对路径的引用。

## 注意

1. 建立文件或目录链接限于 NTFS 文件系统，符号（软）链接的建立可以跨文件系统；
2. 硬链接只能用于文件，不能用于目录，符号（软）链接可以为目录建立链接；
3. 硬链接只能建立同一分区内的文件指向；
4. 硬链接不允许对空文件建立链接，符号（软）链接可以。

## 区别

1. 符号链接（Symbolic link）
   * 执行命令 mklink link_name target_name
   * 创建链接后的图标和快捷方式很像
   * 在系统中不占用空间
   * 在文件系统中不是一个单独的文件
   * 在操作系统层解析（！？）
   * 如果源文件被删除了，链接就没用了
   * 移除源文件不会影响符号链接
   * 移除链接文件也不会影响源文件

2. 硬链接（Hard link）
   * 执行命令 mklink /H link_name target_name
   * 在系统中占用的空间与源文件相同，但在系统中引用的是相同的对象（不是拷贝）
   * 在操作系统层解析（！？）
   * 图标和创建快捷方式的图标不同
   * 移除源文件不会影响硬链接
   * 移除硬链接不会影响源文件
   * 如果源文件被删除，它的内容依然通过硬链接存在
   * 硬链接文件的任何更改都会影响到源文件

3. 快捷方式（Shortcut）
   * 在选择的源文件上鼠标右键，通过下拉菜单创建
   * 快捷方式在系统中跟源文件是完全分离的
   * 只有那些懂得快捷方式的程序知道它们
   * 如果源文件删除，链接就没用了
   * 移除源文件不会移除快捷方式
   * 移除快捷方式不会影响到源文件

# 用途

## OneDrive自定义同步文件夹
符号链接：
```
mklink /D C:\Users\Administrator\OneDrive\Test C:\Users\Administrator\Downloads\Music
为 C:\Users\Administrator\OneDrive\Test <<===>> C:\Users\Administrator\Downloads\Music 创建的符号链接
```

硬链接：在系统中占用的空间与源文件相同，但在系统中引用的是相同的对象。两个文件地位等同，改变一个则另一个跟随变化。
```
mklink /H C:\Windows\System32\config\systemprofile\.config\rclone\rclone.conf C:\Users\Administrator\.config\rclone\rclone.conf

```
## 转移系统中的用户设置文件

命令如下：
1. 复制User目录到D盘：　robocopy “C:\Users” “D:\Users” /E /COPYALL /XJ
2. 强制删除User目录：　rmdir “C:\Users” /S /Q
3. 创建C盘下的User的软件链接，链接到D盘User目录：mklink /J “C:\Users” “D:\Users”

重装系统后只需重复第二条和第三条命令及可

## C盘软件迁移
用法同上。
C盘软件安装文件夹移动到其它盘，然后在原来位置创建


# Linux命令ln
`无参数`：硬链接，文件。
`-s`:软链接，文件或文件夹。