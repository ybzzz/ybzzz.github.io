---
title: shell脚本学习记录
date: 2019-08-03 22:51:53
categories:
- Software Development
- Programming Language
- Shell Script
tags:
---

学习shell脚本编程。
<!--more-->



### 基本脚本
``` shell
	#!/bin/bash
	date
	who
```
以上为一个简单脚本，此时需要添加执行的权限`chmod u+x test`，然后可以运行`./test`，得到结果：
![easyshell](Shell/firstshell.PNG)

### 基本规则、语法
+ 首行`#!/bin/bash`声明用的shell；
+ 除首行外，使用#注释；
+ echo命令打印文本，注意引号；
+ $加环境变量来使用这些变量（set显示当前环境变量列表）；
+ 想显示$符号，加上\；
+ 等号赋值=号左右不得有空格；
+ 将命令输出赋予变量，可使用``testing= `date` ``（该处是反引号）或者`testing=$(date)`来获取；
+ 重定向> <;
+ 管道 | （命令是同时进行，不是依次，中间没有缓冲区，直接将运行数据给另一个命令）；
+ 使用`expr arg1 + arg2`类似进行数学运算，在命令行中对有些符号要加上转义字符\才能工作；
+ 在shell脚本中，如`var3=$(expr $var2 / $var2)`来进行运算；
+ 也可使用方括号进行数学运算`var4=$[$var3 * ($var2 - $var1)]`，这种方式不需要转义符号。（这两种都只支持整数运算）；
+ 用内置计算器bc进行计算，`scale=4`设置小数位数；
+ 用bc进行复杂计算，可以使用内联重定向进行多行运算，以下是上述几个方式示例：
![几个运算方法示例](Shell/bc.PNG)

+ 使用`echo $?`查看shell所执行的最后一条命令的退出状态码，范围0~255；
