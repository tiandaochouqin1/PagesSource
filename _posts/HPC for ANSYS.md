---
title: 面向ANSYS仿真的HPC选择
date: 2019-09-4 12:30:11
tags: 其它
categories: 其它
---
<font face="微软雅黑"> </font>
<center>ANSYS有限元计算的高性能计算机</center>

<!-- more -->

<!-- TOC -->

- [总结](#总结)
- [双路 EPYC](#双路-epyc)
    - [EPYC7551](#epyc7551)
    - [ANSYS 的设置](#ansys-的设置)
    - [为什么 EPYC 是最快的](#为什么-epyc-是最快的)
    - [详细比较](#详细比较)
    - [其它说明](#其它说明)

<!-- /TOC -->

[转自](https://www.zhihu.com/question/23122104/answer/717905333)
[EPYC超频组建全能工作站](https://www.chiphell.com/thread-1997874-1-1.html)
[EPYC 7371测评](https://www.servethehome.com/amd-epyc-7371-review-now-the-fastest-16-core-cpu/)
# 总结
首先是结论，『最强』的硬件配置是（按照最强->强排序）：

1. 双路 AMD EPYC 7551 + 16 条 2Rx4 内存 + NVIDIA Tesla 显卡（可有可无）。不搭载显卡的价格大约为 5 万元。几乎所有测试用例中都是最快的，在部分测试用例中，速度甚至达到了整机价格 10 万元以上的 4 路 Intel Xeon Platinum（至强铂金）的 5 倍、整机价格 10 万元以上的 2 路 Intel Xeon Platinum（至强铂金）+ 2 路 NVIDIA Tesla V100（NVIDIA 目前真旗舰显卡）的 4 倍。因为 EPYC 是目前内存带宽最大、多核通信效率最高的 CPU。答主最终购买的配置。
2. 双路 Intel Xeon E5（至强 E5）+ NVIDIA Tesla 显卡（可有可无）。Haswell 架构及之后的 Intel CPU，ANSYS 的性能都很差，因为 Intel 大幅修改了 CPU 的多核通信架构（使用了内存一致模型），因此前几代、老版本的 E5 反而求解速度快。（实测，至少 Xeon E5-2696v4 在 1 分钟求解时间左右的小模型上能有 EPYC 的速度，新一些的 Intel CPU 可能还没这个速度，Xeon E5-2696v4 属于 Broadwell 架构，算是“新”的 CPU，按理不该有这个速度，答主至今未搞懂原因）
3. AMD Ryzen（桌面级 CPU）。Ryzen 2700X 的整机价格大约为 5000 元，此外有 Ryzen 2990WX（AMD 目前桌面真旗舰 CPU），整机价格大约为 2.5 万元。中小模型（10～100 万网格）第三强的配置（最强仍是 EPYC）。

# 双路 EPYC

- 不同 CPU 之间的差异极大。CPU 的性能指标并不是只有主频和核心数量，更有内存通道数量、浮点运算速度/精度、多核同步指令延迟...这些指标可能构成影响速度的主要原因。
- CPU 的核心并不是越多越好。有些应用最佳状态是 16 核并行，使用 32 核的机器甚至会降低运算速度。
- 内存并不是越大越好。有些应用没有对内存访问速度做优化，使用太大的内存会增加多核通信的延迟，导致运算速度的降低。另外，64 GB 及以上的 RECC 内存是低电压内存，速度可能不如 32 GB 及以下的内存。（多路 CPU 主板只能使用 RECC 内存）
- 显卡可能没有意义。有些应用对 GPU 运算没有很好的优化，使用 GPU 运算无法提升速度。
- 并不是越贵越好。有些十分昂贵的硬件方案，例如，多块 SSD 组 RAID，虽然连续读写的速度能有成倍的提升，但随机读写的速度会有成倍的下降，对小文件访问为主的应用而言，RAID 甚至会成为性能瓶颈。

## EPYC7551
- 7601 是现在 EPYC 的旗舰 CPU，7601 比 7551 的主频高约 10%（但价格贵一倍）。不过，7551 的“体质”更好，超频的频率更高（可以使用 ASUS ZenStatus 软件一键超频）。都超频的情况下，7551 比 7601 的频率还高 0.1 GHz 左右，并且更稳定（两者超频时的最高频率都在 3.6 GHz 左右）。
- 实测，NVIDIA Tesla 显卡只对复杂的大模型有明显的加速，模型不复杂的情况下（例如，求解时间小于 5 分钟的模型），GPU 甚至会增加求解时间。
- 内存大小并不重要，但是内存的数量很重要，少于 8 根内存条会明显降低求解速度，因为内存带宽对 ANSYS 求解时间的影响是第二大的。最好使用 2Rx4 的内存，内存带宽利用率更高、实际内存带宽更大。

## ANSYS 的设置
也非常重要（按照重要性最高->高排序）：

1. [Direct 求解器比 Iterate 求解器更快](https://www.sharcnet.ca/Software/Ansys/17.0/en-us/help/ans_bas/Hlp_G_BAS3_2.html)，但只有使用 EPYC 时非常明显，这是 EPYC 稳坐第一的主要原因之一。
2. [将一对接触设置为多对接触，能提升多核运算效率。](https://support.ansys.com/staticassets/ANSYS/Conference/Confidence/Boston/Downloads/hpc-best-practices-for-fea.pdf)
3. [in-core 比 out-of-core 模式快，内存足够大时自动开启 in-core。](https://studentcommunity.ansys.com/thread/specifying-memory-mode-out-of-core-or-in-core/)

## 为什么 EPYC 是最快的
仅考虑纯 CPU 计算，影响求解时间的主要因素有（按照影响最大->大排序）：

1. **多核通信效率**。Intel 在 Haswell 架构后几乎全面使用了内存一致模型，在降低多核编程难度的同时，大大提高了多核通信的延迟。AMD 的 CPU 架构更“古老”，反而造成了 Ryzen（AMD最新一代 CPU 架构，EPYC 使用类似的架构）在 ANSYS 求解速度上的极大优势。
2. **内存带宽**。ANSYS 在求解时，需要极其频繁地访问内存，CPU 的运算速度（通常不超过 3 GHz）一般高于内存访问速度（通常不超过 2666 MHz），因此瓶颈是内存访问速度。EPYC 最大的优势是 8 个内存通道，而 Intel Xeon E3/E5/E7（至强 E3/E5/E7）只有 4 个内存通道，Intel Scalable（至强金牌/铂金）只有 6 个内存通道（最新的 9 系有 12 个内存通道，但是买不到、价格极其昂贵、架构相比 EPYC 也没有优势）。部分 Intel Xeon E5（至强 E5）的多核通信效率高于 AMD Ryzen（即 EPYC），但是内存通道数量太少、内存带宽太低，导致实测性能不如 EPYC。
3. **CPU 核心数**。Intel Xeon Platinum（至强铂金）目前最高只有 28 核，而 EPYC 最高是 32 核（例如 7551）。
4. **CPU 主频**。Intel Xeon Platinum（至强铂金）的 28 核 CPU 能达到 4 GHz 左右的最高主频，而 EPYC 的 32 核 CPU 只能达到 3.6 GHz 左右的最高主频。但是，CPU 主频高并没有太大用处，内存访问速度才是关键。
Intel Xeon（至强）的浮点计算能力非常好，但是浮点计算能力/计算速度对 ANSYS 的求解速度影响并不大，对求解准确度可能有一点点的影响，但理论上也不大。

ANSYS 在使用 Direct 求解器及开启 in-core 模式的情况下，能最大化内存带宽的优势。因此，双路 EPYC、总计 16 个内存通道的配置能极大地提升求解速度。
因此，双路 EPYC 依靠高内存带宽、高多核通信效率，成为了 ANSYS 最强 CPU。
## 详细比较

1. 多核通信效率
衡量多核通信效率，最简单的指标是 LOCK CMPXCHG 指令的执行速度（按照最快->快排序、数值为单条指令平均耗时、单位为一个 CPU 指令周期）：

        AMD Ryzen（14 纳米，即 EPYC）：15
        Intel Sandy Bridge（32 纳米）：22
        Intel Ivy Bridge（22 纳米）：22
        Intel Haswell（22 纳米，部分至强 E 系列）：87
        Intel Broadwell（14 纳米，部分至强 E 系列）：87
        Intel Skylake（14 纳米，多数至强金牌/铂金系列）：87
[数据来源](https://www.agner.org/optimize/instruction_tables.pdf)
LOCK CMPXCHG 指令非常常用，互斥量、临界区等多核通信基本数据结构，都是用它实现的。
2. 为什么要 16 条内存？
『CPU 总通道数 = 内存数』才能最大化内存带宽，双路 EPYC 总共 16 个内存通道，因此需要 16 条内存。此外：
内存的“R 数”（Rank 数）也有一定影响，2R 比 1R 的速度快一点。
内存的 Bank 数，例如，2Rx4 比 2Rx8 的 Bank 更多，Bank 多的会快一点。
内存足够大时，ANSYS 才会开启 in-core 模式。
Rank 数和 Bank 数大概有 10% 的影响。（数据不准确，未进行全面测试）
部分测试用例中，8 条内存能达到 16 条内存 90% 的求解速度，但少于 8 条时求解速度会严重降低。
**实测成绩**
测试用例为：

        ANSYS Mechanical Workbench
        10 万网格
        3 对接触（均为 Bonded）
        汽车轮毂、轮胎与地面接触的受力模型
实测成绩（按照最快->快排序、均插满内存、均未超频）：

        双路 EPYC：10+ 秒
        双路 Intel Xeon E5-2696v4：30+ 秒（某些情况下可以达到 10+ 秒，原因不明，具体做法尚不明确）
        AMD Ryzen 1600X（家用 CPU）：30+ 秒
        双路 Intel Xeon Gold 6148 + 双路 NVIDIA Tesla V100：50+ 秒
        四路 Intel Xeon Platinum 8176：60+ 秒
        双路 Intel Xeon Gold 6148：110+ 秒
        单路 Intel Xeon E5-2682v4 + 单路 NVIDIA Tesla V100：110+ 秒
        单路 Intel Xeon E5-2682v4：120+ 秒
        单路 Intel Platinum 8163：130+ 秒
        Thinkpad P50（具体配置未知）：140+ 秒
        Thinkpad X1 Carbon 2018（CPU 为 i7-8650U、内存为 16 GB）：150+ 秒
        说明：
因时间、精力限制，没有在所有机型上都测试中型、大型模型，因此只有小型模型（测试用例）的测试结果，但是中型模型、大型模型的实测成绩和小模型几乎一致。
部分实测成绩数据因个人疏忽没有记录，记忆可能有细微差错，但基本不影响结论及成绩排名。

郑老板的数据更全，感兴趣的朋友去找他要。
该测试用例 16 核并行时速度最快，网格数量提升至 100 万（求解时间 5 分钟左右）时，全核并行速度最快。
关闭超线程有助于提升求解速度，影响应该大于 10%。
部分测试机器来自于阿里云及百度云，成绩低于真实水平，但基本不影响结论。

## 其它说明
最常见的 ANSYS 的盗版版本没有 HPC License 的限制，换而言之，可以支持任意数量的 CPU 核心及显卡。
ANSYS 官网几乎所有关于高性能计算的说明，都建议使用双路 CPU 的配置。
[测试用例下载](https://pan.baidu.com/s/1-AnUGDYkhBUzdBsLgUm10w)
提取码 gwff
