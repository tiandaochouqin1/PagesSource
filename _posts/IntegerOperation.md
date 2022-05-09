---
title: IntegerOperation
tags:
  - [整型]
  - [C语言]
categories: [C语言]
date: 2021-02-24 20:57:31
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->
- [整型计算](#整型计算)
- [溢出计算](#溢出计算)
  - [usigned](#usigned)
  - [其它位数的值](#其它位数的值)



<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
</head>


# 整型计算

https://www.zhihu.com/question/51493338
所有的数都是按照补码存放的，按照补码加减法规则运算。

无符号数A-B，可以和补码表示的有符号数一样，转化成，A+（B的补码），即A+(~B+1 )。

$A+(B_补) = A+(~B+1) = A + (2^n-1-B+1) = A-B+2^n$,然后结果会对$2^n$取模(也就是截断),得到$A-B = A+(B_补)$。


无符号数只是C语言抽象出来的一个概念。机器码中没有区分无符号数和有符号数，只是看寄存器的标志位SF（Sign Flag）来决定它的符号，加、减法运算都是统一由加法器来实现的。区别在于对最后的结果的解释方式不同。

<br/>

>公式左边对应加/减法：右边对应机器运算

$$[A+B]_补=[A]_补 + [B]_补$$
$$[A-B]_补=[A]_补 + [-B]_补$$



# 溢出计算
## usigned

>unsigned int 溢出即对2^n取模。

$(A+k) - A = k$：无符号与有符号整型（这里整数位数要大于int类型，小于int运算时有整型提升），在加法溢出的情况下仍然成立。

https://www.onlinegdb.com/2bv60dpUo 
https://godbolt.org/z/da37be

```
#include <stdio.h>

int main()
{
    unsigned long long uBefore ;
    unsigned long long uIncrement;
    unsigned long long uAfter ;
    unsigned long long uResult;

    uBefore = 0xffffffffffffffff; //48位
    uIncrement = 0xffff;
    uAfter = uBefore + uIncrement;
    // uAfter = 0xfe;
    uResult = uAfter - uBefore ;

    printf("uBefore=0x%llx; uIncrement=0x%llx; uAfter=0x%llx; uResult=0x%llx\n", uBefore, uIncrement, uAfter, uResult);
    if(uIncrement == uResult)
    {
        printf("unsigned OK!!!\n\n");
    }

    long long Before ;
    long long Increment;
    long long After ;
    long long Result;

    Before = 0x7fffffffffffffff; //正数最大值
    Increment = 0xffff;
    After = Before + Increment;
    // After = 0xfe;
    Result = After - Before ;

    printf("Before=0x%llx; Increment=0x%llx; After=0x%llx; Result=0x%llx\n", Before, Increment, After, Result);
    if(Increment == Result)
    {
        printf("signed OK!!!\n\n");
    }

    return 0;
}
```

## 其它位数的值
$(A+k) - A$(A+k可能溢出)：在实际工程中，A的上限可能为40bit(0x000000ffffffff)，而代码中存放该值的变量为64bit。
此时等式形式为：$((A+k) - A) & 0x000000ffffffff = k$