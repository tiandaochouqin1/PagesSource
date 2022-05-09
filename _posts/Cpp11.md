---
title: C++11新特性
date: 2019-06-01 17:51:25
tags: LeetCode
categories: 编程
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->
<!-- TOC -->

- [基于范围的for](#%e5%9f%ba%e4%ba%8e%e8%8c%83%e5%9b%b4%e7%9a%84for)

<!-- /TOC -->
***

## 基于范围的for

传统方法：

    int arr[10]={1,2,3,4,5,6,7};
    for(int i=1;i<7;i++)
        cout<<arr[i];

    std::vector<int> vec {1,2,3,4,5,6,7};
    for(std::vector<int>::iterator itr=vec.begin();itr!=vec.end();itr++)
        std::cout<<*itr;

C++11引入了C#和Python中的范围for,不需要明确给出容器的开始和结束条件就可以遍历整个容器。

    std::vector<int> vec{1,2,3,4,5,6,6,7};
    for(auto n:vec)
        std::cout<<n;

    for(auto n:arr)
        std::cout<<n;

基于范围的FOR循环的遍历是**只读的遍历**，除非将变量变量的类型声明为引用类型。
将声明的遍历遍历n从auto 声明为auto &。

**基于范围的for对于容器map的遍历：**

    std::map<string,int> map={{"a",1},{"b",2},{"c",3}};
    for(auto &val:map)
        cout<<val.first<<"-<"val.second<<endl;
在遍历容器的时候，auto自动推导的类型是容器的value_type类型，而不是迭代器，而map中的value_type是std::pair，也就是说val的类型是std::pair类型的，因此需要使用val.first,val.second来访问数据。

**如果冒号后面的表达式是一个函数调用时，函数仅会被调用一次**

**不能修改容器**
基于范围的for循环和普通的for循环一样，在遍历的过程中如果修改容器，会造成迭代器失效.

**自定义的类实现基于范围的for**

1. 定义自定义类的迭代器  //这里的迭代器是广义的迭代器，指针也属于该范畴。
2. 该类型拥有begin() 和 end() 成员方法，返回值为迭代器（或者重载全局的begin() 和 end() 函数也可以） 。
3. 自定义迭代器的 != 比较操作 。
4. 自定义迭代器的++ 前置自增操作，显然该操作要是迭代器对象指向该容器的下一个元素 。
5. 自定义迭代器* 解引用操作，显然解引用操作必须容器对应元素的引用，否则引用遍历时将会出错。

***