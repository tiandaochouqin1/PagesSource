---
title: 分区合并与数据恢复
tags:
  - Windows
  - 系统
categories: Windows
date: 2020-04-08 23:53:53
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->

# 问题
需要扩展系统分区C，空闲空间N与C之间存在一个现有的分区B。

**基本原理:**
先合并N与B得到B+；然后调整B+空间，在其头部分出空闲空间N+；将空间N+与C合并。

**软件：**
1. DiskGenuis
2. 傲梅分区助手（强制推广公众号）
   
# 过程
1. DiskGenuis备份分区表，备份分区B为镜像，备份到其它硬盘，检查分区表错误。
2. 分区助手检查分区错误，或使用`chkdsk /scan`；
3. 分区过程需要重启进入PE。
4. DiskGenuis 5.1.0 英文破解版重启进入PE后只显示软件页面没有实质操作，手动重启；5.1.1版（所谓完美破解版）重启进入PE后立即自动重启也无操作。应该是破解未完全。4.7.2版（所谓完美破解版）未测试。
5. 使用傲梅分区助手；顺利分区并进入系统；
6. 问题：磁盘盘符被打乱（C-磁盘1；D-磁盘2；E-磁盘3），D和E互换。
   导致的问题有：
   D盘安装了大量软件，相关`路径错误`。
   部分`自启动软件 `被禁止：power-key、Clash
   部分被禁止的软件开启了自启动。
   开始菜单`最近使用`软件顺序被打乱。
7. 立即使用系统自带的磁盘管理将盘符交换回来，根据提示重启。
   重启后再出现自启动软件被禁止。


# 数据恢复简介
https://www.r-studio.com/Data_Recovery_Guide.shtml
https://www.r-studio.com/file-recovery-basics.html

[R-Studio网络技术员版](https://masuit.com/1538/r%20studio)。最强大的数据恢复软件之一。
抛弃Easy Recovery等。

**数据恢复解决方案**:
* 支持对NTFS、ReFS、FAT/exFAT、Ext2FS/Ext3/Ext4、UFS、HFS等分区文件系统恢复数据
* 支持对已损坏或删除的分区、加密文件、数据流进行数据恢复
* 支持硬盘分区创建镜像文件.rdr、RAID磁盘阵列
* 最大的特色在于可以自动识别 RAID 参数修复损坏的磁盘阵列
* R-Studio Network具有链接到远程计算机网络磁盘恢复数据、S.M.A.R.T. 属性监视、文本/十六进制编辑、大量参数设置等功能。


**典型的数据恢复案例**：

* 恢复意外删除的文件
* 从无法正常运行的计算机中恢复文件
* 从SD卡恢复照片和视频
* 重新格式化或全新安装操作系统后，从文件系统损坏的磁盘中恢复文件
* RAID重建和恢复
* 从Windows存储空间，Apple RAID，Linux LVM / LVM2和NAS设备恢复文件
* 从虚拟机，各种文件容器格式和加密磁盘恢复文件
* 网络数据恢复

**数据丢失时：**
1. 不要进行任何操作，包括打开软件和开关机；如果是系统盘；则仅关机。
2. 克隆磁盘，使用镜像来进行恢复操作。
3. 不要购买任何数据恢复软件，除非您确定它可以在特定情况下恢复文件。。请注意，即使程序显示了正确的文件和文件夹结构，也不能保证可以成功恢复文件。只有成功预览的文件才能成功恢复。
4. 当计算机要删除文件时，它不会立即销毁其数据。相反，它将对有关文件和文件夹的信息进行一些更改，以表明该文件已被删除。所有操作系统都将实际文件数据保持不变，直到有必要为另一个文件分配该磁盘空间。
5. 如果磁盘上的数据被覆盖，那么旧数据就消失了。没有程序或商用数据恢复方法可以恢复它。

**磁盘损坏时：**
硬件故障的磁盘上`不应尝试数据恢复`。如果您怀疑磁盘可能受到了物理损坏，则最好的做法是将案例转给具有适当经验和设备的数据恢复专业人员。任何篡改物理损坏的磁盘的尝试都可能导致进一步的数据丢失，从而使后续的数据恢复尝试变得徒劳。
## 恢复原理

如上所述，磁盘中存储文件数据的部分还包含有关文件和文件夹的信息的备份副本。磁盘的此部分可能还包含一些其他信息，这些信息涉及散布在整个磁盘上的文件和文件夹结构。

对于尚未覆盖的文件，有两种文件恢复方法。所有数据恢复软件都使用这些技术中的一种或两种。

### 文件信息恢复
**通过分析有关文件和文件夹的信息来恢复文件**
这是文件恢复程序尝试执行的第一种方法。这是因为它可以恢复具有其原始名称，路径，日期/时间戳及其数据（如果成功）的文件。
文件恢复软件首先尝试读取和处理有关文件和文件夹的信息的第一份副本。在某些情况下（例如意外删除文件），这是`完整恢复`文件所需要采取的唯一步骤。

如果有关文件和文件夹的信息的第一份副本受到严重破坏，则软件会在磁盘上扫描有关文件和文件夹的信息的第二份副本。它还尝试收集有关磁盘数据部分上的文件夹和文件结构的其他信息。然后，它处理所有这些信息以重建原始文件夹和文件结构。
如果磁盘上的文件系统没有受到严重损坏，通常可以恢复整个文件和文件夹结构。
如果磁盘上的文件系统受到严重损坏，则此恢复方法无法重新创建整个文件夹结构。然后，恢复的文件将出现在“孤立”文件夹中。

### 原始文件恢复
**使用搜索已知文件类型的文件恢复（原始文件恢复）**
如果第一种方法无法产生令人满意的结果，则执行原始文件搜索。第二种数据恢复方法可以比第一种方法更成功地恢复文件数据，但是它不能重建原始文件名，日期/时间戳或磁盘的整个文件夹和文件结构。
搜索已知文件类型或原始文件恢复的方法是，通过分析磁盘上的“`文件签名`”内容。文件签名是表示文件开头或结尾的常见模式。几乎每种文件类型都有至少一个文件签名。例如，所有png（便携式网络图形）文件类型的文件都以“‰PNG”字符串开头，而许多MP3文件都以“ ID3”字符串开头。这样的文件签名可用于识别磁盘上的一条数据属于某个文件类型，因此可以恢复。
某些文件类型根本没有立即可识别的文件签名

### 自定义文件签名
R-Studio之类的高级文件恢复程序允许用户手动指定非常复杂和高级的文件签名集，包括自定义文件签名。