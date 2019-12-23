---
title: GCC 常用参数
date: 2019-12-23 21:08:06
tags: GCC
categories: 嵌入式
---


预处理阶段：（1.宏定义展开  2.头文件包含  3.条件编译）
**gcc hello.c -o hello.i -E**

编译阶段：（将c文件（.c）编译得到汇编文件（.s））
**gcc hello.i -o hello.s -S**

汇编阶段：（将汇编文件（.s）文件汇编得到二进制目标文件）
**gcc hello.s -o hello.o -c**

链接阶段：（链接得到二进制可执行文件）
**gcc hello.o -o hello.out**

可执行文件的执行：
**./hello.out**



- -E ：预编译处理

- -S ：预处理->编译 生成汇编文件

- -c ：预处理->编译->汇编 生成目标文件(但不链接)

- -o ：指定输出的目标名称

- -g ：添加调试信息

- -I ：指定头文件搜索路径

- -L ：指定链接库的路径

- -l ：指定链接库的名称

- -D ：编译时添加宏控制

- -w ：不生成任何警告信息

- -M : 获得目标文件的依赖关系

- -MM : 获得目标文件的依赖关系，不包含系统头文件的依赖关系

- -MT : 获得目标文件的依赖关系，并指定目标名

- -Wall ：生成所有警告信息

- -static ：此选项将禁止使用动态库，所以，编译出来的可执行文件一般都很大，运行时不需要加载动态链接库，就可以运行  

- -share ：此选项将尽量使用动态库，所以生成文件比较小，但是运行的过程中需要加载动态链接库

- -D ：[Click for more](http://blog.csdn.net/maopig/article/details/7230311)
  在Makefile文件里，我们会看到：
  gcc -D MACRONAME=MACRODEF 或者 gcc -D MACRONAME 
  这样就定义了预处理宏，编译的时候可选代码就会被编译进去了。
  对于GCC编译器，有如下选项：[-D macro=string]或者[--define-macro macro=string]
  -D macro=string，等价于在头文件中定义：#define macro string
  如：-D TRUE=true，等价于：#define TRUE true 
  -D macro，等价于：#define macro 1，实际上也达到了定义：#define macro的目的。
  如：-D LINUX，等价于：#define LINUX 1（与#define LINUX作用类似）。
  ​

- -On ：指定优化级别(其中 n 的取值可以为：0、1、2、3 。各个优化级别的区别参考[这里](http://blog.csdn.net/qq_31108501/article/details/51842166))

  在实际的运用过程中，发现对于CPU密集型的程序来说，增加优化级别能够大幅缩短执行时间。比如下面给升级镜像增加OOB区域的数据（制作烧录镜像）。

  ![diff-Ox](https://raw.githubusercontent.com/zhangyaaoo/ImageBed/master/make/diff-Ox.png)

