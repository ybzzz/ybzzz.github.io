---
title: C++ vtable Error
date: 2019-09-02 17:13:04
categories:
- c++
tags:
- c++
---

虚基类继承的时候出现error：`undefined reference to 'vtable for xxx'` 以及 `undefined reference to 'typeinfo for xxx'`

解决方法：
1. 对于纯虚函数需要加上函数体 `{}`，即 `virtual void example();` 要改为 `virtual void example() {}`
2. 含有虚函数的类的析构函数要声明为virtual。

为什么要加函数体？
>暂时不明