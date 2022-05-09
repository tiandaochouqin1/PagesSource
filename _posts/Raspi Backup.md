---
title: Raspi System Backup
date: 2019-11-28 00:47:59
tags: 路由器
categories: Linux
---
<font face="微软雅黑"> </font>
<center>Raspi 最小备份方法</center>

<!-- more -->

<!-- TOC -->

- [问题描述](#问题描述)
- [备份方法1](#备份方法1)
  - [1 缩小分区后备份](#1-缩小分区后备份)
  - [2 复制完整镜像中的文件](#2-复制完整镜像中的文件)
  - [3 复制内存卡文件到镜像](#3-复制内存卡文件到镜像)
- [备份方法2](#备份方法2)

<!-- /TOC -->
***


# 问题描述

使用树莓派时需要频繁刷写不同的系统。

树莓派备份时为全盘备份，备份镜像的大小与存储卡一样，占用空间过大，且写入速度过慢。故需要一种可以按实际需要大小备份系统的方法。

# 备份方法1

原理：`dd` 命令备份：将已有分区完整备份；不包括未分配空间。
**以下方法备份的镜像用于还原后均需要扩展磁盘空间。**

## 1 缩小分区后备份

[在linux上制作树莓派最小img镜像](https://blog.csdn.net/u013451404/article/details/80552765)
[https://www.jianshu.com/p/7e6b7e3ebb97](https://www.jianshu.com/p/7e6b7e3ebb97)

1. 查看tf 卡实际有效数据占用
        df -h

2. 查看并记录需要缩减分区 start 扇区,这里只有sdb 2 需要缩减
        fdisk -l

3. 调整分区占用大小
        使用命令：
        e2fsck 检查分区信息
        resize2fs 调整分区大小
        sudo resize2fs /dev/sdb2 3G

4. 重建分区，大小为步骤3 调整后的大小
        sudo fdisk /dev/sdb
        d // 使用command d delete sdb2
        n // 重新建立sdb2
        1. 按 n 后选择新建主分区(p)，分区号与之前要一致，写2
        2. 填写分区的start 分区，步骤2记录下来的， end 分区 +3G ， 回车
        3. 关键步骤，询问 原来已经有一个ext4 分区存在啦，是否要删除该分区标志？
        一定要输入： n ，回车
        w // 改变重建分区表
        fdisk -l 分区大小已经改成3G
5. 将压缩的 tf 卡镜像到一个img 文件中
sdb2 的end 扇区是最后一个占用的扇区，为7317503 ，但是由于扇区是从0开始编号的，所以实际整个TF卡上的分区一共占用了7317504个扇区，每个扇区是512字节，那么实际占用(7317504512)/(10241024) = 3573MB
        1. sudo dd if =/dev/sdb of=rpi.img bs=1M count=3573
        2. sudo fdisk -l debian-on-rpi.img

## 2 复制完整镜像中的文件

[Raspberrypi 3 备份还原系统](https://blog.csdn.net/lb1885727/article/details/77574502)

1. 完整备份的到镜像；
2. 挂载镜像，将镜像中的文件复制到一个空的镜像文件中；
3. 替换文件系统的UUID。

## 3 复制内存卡文件到镜像

[制作树莓派最小镜像-img裁剪瘦身](https://blog.csdn.net/talkxin/article/details/50456282)中采用此方法，并使用脚本实现。

脚本经过以下固件版本测试：

        2016-05-27-raspbian-jessie
        2018-06-27-raspbian-stretch-lite

整理后的脚本，编辑sudo vi backup.sh，复制以下内容，sudo chmod 777 backup.sh，然后sudo ./backup.sh就可以在当前脚本目录中生成树莓派的SD卡镜像了。
**脚本接受1个必要参数：存放镜像的目录。**

```C++
#!/bin/bash

set -e

# start
if [ -z $1 ]; then
  echo "Backup directory not set, required."
  exit 1
fi
BACK_UP_DIR=$1
echo

# install
echo "Installing package ..."
apt-get install dosfstools dump parted kpartx -y
echo "Finish."
echo

# create image
echo "Creating image ..."
ROOT=`df -P | grep /dev/root | awk '{print $3}'`
MMCBLK0P1=`df -P | grep /dev/mmcblk0p1 | awk '{print $2}'`
ALL=`echo $ROOT $MMCBLK0P1 |awk '{print int(($1+$2)*1.2)}'`
TIME=`date "+%Y%m%d%H%M%S"`
FILE=$BACK_UP_DIR/backup_$TIME.img
dd if=/dev/zero of=$FILE bs=1K count=$ALL
echo "Finish."
echo

# part
echo "Parting image ..."
P1_START=`fdisk -l /dev/mmcblk0 | grep /dev/mmcblk0p1 | awk '{print $2}'`
P1_END=`fdisk -l /dev/mmcblk0 | grep /dev/mmcblk0p1 | awk '{print $3}'`
P2_START=`fdisk -l /dev/mmcblk0 | grep /dev/mmcblk0p2 | awk '{print $2}'`
parted $FILE --script -- mklabel msdos
parted $FILE --script -- mkpart primary fat32 ${P1_START}s ${P1_END}s
parted $FILE --script -- mkpart primary ext4 ${P2_START}s -1
parted $FILE --script -- quit
echo "Finish."
echo

# mount
echo "Mounting ..."
LOOP_DEVICE=`losetup -f --show $FILE`
kpartx -va $LOOP_DEVICE
PART_BOOT="/dev/dm-0"
PART_ROOT="/dev/dm-1"
echo "Finish."
echo

# format
echo "Formating ..."
mkfs.vfat $PART_BOOT
mkfs.ext4 $PART_ROOT
echo "Finish."
echo

# backup prepare
MOUNT_POINT=/media/backup_$TIME/
if [ ! -d "$MOUNT_POINT" ];then
  mkdir $MOUNT_POINT
fi

# backup /dev/boot
echo "Backing up disk /dev/boot ..."
mount -t vfat $PART_BOOT $MOUNT_POINT
cp -rfp /boot/* $MOUNT_POINT
umount $MOUNT_POINT
echo "Finish."
echo

# backup /dev/root
echo "Backing up disk /dev/root ..."
mount -t ext4 $PART_ROOT $MOUNT_POINT
cd $MOUNT_POINT
dump -h 0 -0uaf - / | sudo restore -rf -
cd
umount $MOUNT_POINT
echo "Finish."
echo

# unmount
echo "Unmounting ..."
kpartx -vd $LOOP_DEVICE
losetup -d $LOOP_DEVICE
rm -fr $MOUNT_POINT
echo "Finish."
echo

# end
echo "Back-up image $FILE is successfully created."
echo
```


# 备份方法2
1. Gpart/fdisk等工具缩减分区为实际使用大小；
2. win32disk imager 备份sd。
3. 镜像恢复到sd卡后需要重新调整分区。

