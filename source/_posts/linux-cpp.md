---
title: linux下cpp编译
date: 2019-08-03 13:18:12
categories:
- c++
tags:
- linux
- c++
- 编译
---


编译和链接分为四个部分：预处理(processing)、编译(compilation)、汇编(assembly)、链接(linking)

<!--more-->


### 预处理
处理源代码中以'#'开始的预编译指令，包括：
+ 展开宏定义，替换`#define`
+ 处理条件预编译,如#if、#ifdef、#elif、#else、#endif，将不需要的代码过滤掉
+ 处理`#include`命令，出入包含的文件
+ 过滤注释中的内容
+ 保留`#program`编译器指令

预编译后得到.i文件，可用以下指令得到：
`g++ -E hello.cpp -o hello.i`


### 编译
对.i文件进行词法分析、语法分析、语义分析以及优化后产生汇编代码文件。
+ 词法分析：将源代码程序分割成一系列记号（关键字、标识符、字面量（数字、字符串等）和特殊记号（加好、等号等））  工具如：lex
+ 语法分析：产生语法树，可判断表达式是否合法（如括号匹配、缺少操作符等）  工具如yacc
+ 语义分析：分析静态语义，如赋值类型错误等

编译得到.s文件（未链接的目标文件，如变量地址等），可用以下指令得到：
`g++ -S hello.i -o hello.s`


### 汇编
将编译阶段生成的.s文件转换为目标文件，如下：
`g++ -C hello.s -o hello.o`

### 链接
把每个源代码模块独立地编译，然后按照要将它们“组装”起来，这个组装模块的过程就是链接。主要进行地址和空间分配、符号决议和重定位等。
包括静态链接和动态链接。

+ 对函数库的链接是放在编译时期完成的是**静态链接**.所有相关的目标文件与牵涉到的函数库被链接合成一个可执行文件。 
程序在运行时，与函数库再无瓜葛，因为所有需要的函数已复制到相关位置。这些函数库被称为静态库，通常文件名为"libxxx.a"形式。
创建静态库示例：
文件：add.h、sub.h、add.cpp、sub.cpp、main.cpp
（1）先编译至汇编阶段：
		g++ -c add.cpp
		g++ -c sub.cpp
（2）然后由.o文件创建静态库：`ar cr libxxx.a sub.o add.o`，可用`ar tv libxxx.a`显示库文件中的.o文件。
（3）在程序中使用静态库，执行`g++ -o main main.cpp -L. -lxxx`。其中-L指定加载库文件的路径，-l指定加载的库文件。

缺点：静态链接文件会很大，每次库升级都要重新编译库文件。多个程序用相同的库会产生多个库的拷贝，很浪费空间。

+ 对一些库函数的链接载入推迟到程序运行时期，就是**动态链接**。
动态链接得到的是.so文件。多个程序可共享同一个库。
（1）得到动态库文件
		g++ -fPIC -o add.o -c add.cpp
		g++ -fPIC -o sub.o -c sub.cpp
		g++ -shared -o libxxx.so add.o sub.o
也可以替换为 `g++ -fPIC -shared -o libxxx.so add.cpp sub.cpp`。
-fPIC:编译为位置独立的代码
-Lpath:表示在path目录中搜索库文件
-Ipath:表示在path目录中搜索头文件
（2）使用动态库文件
可以与使用静态库一样，但是要注意动态库的位置。可以1.将动态库复制到目录`/usr/lib`中 ；2修改环境变量LD_LIBRARY_PATH.

缺点：可移植性太差






















