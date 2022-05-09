---
title: 知识积累-2021
tags:
  - [编程]
categories: [编程]
date: 2021-08-30 00:45:47
---

<center> </center>

<!-- more -->


- [union结构体](#union结构体)
- [sparse file](#sparse-file)
- [磁盘性能fio](#磁盘性能fio)

# union结构体

```
#include <stdio.h>
#include <stdlib.h>

int main()
{
    union{
        int a[2];
        long  b;
        char c[4];
    }s;
    s.a[0] = 0x39;
    s.a[1] = 0x38;
    printf("%lx\n",s.b);
    printf("%c\n",s.c[0]);
    return 0;
}
```

打印结果为
```
0x3800000039 # 注意数位数！ 0x0000003800000039
9     # ascii 码
```

gdb调试
```
b main
si 2

x/10xw 0x7fffffffe3d8

0x7fffffffe3d8: 0x00000039      0x00000038      0x00000000      0x00000000
0x7fffffffe3e8: 0xf7a2f505      0x00007fff      0x00000000      0x00000020
0x7fffffffe3f8: 0xffffe4c8      0x00007fff


```

# sparse file
稀疏文件：文件内容大量为空时，文件大小保存在元数据中，只有真实的/非空数据才会被写入磁盘，文件需要的存储空间在被使用时才会被分配。

缺点：造成磁盘碎片；文件系统的剩余空间失真

1. truncate -s 5M <filename>
 
2. stat可查看实际占用的blocks数量

3. gnu cp默认支持sparse文件。--sparse=WHEN 会将已写入磁盘的长零串写为稀疏文件。

# 磁盘性能fio

```
fio \
--name=fio-test \
--filename=test.data \
--size=5G \
--bs=4k \
--rw=randwrite \
--ioengine=libaio \
--direct=1 \
--iodepth=32 \
--time_based \
--runtime=600 \
--group_reporting
```