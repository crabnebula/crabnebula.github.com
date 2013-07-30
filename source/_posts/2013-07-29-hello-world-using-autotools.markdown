---
layout: post
title: "hello world using autotools"
date: 2013-07-29 21:21
comments: true
categories: 
---

GNU Autotools断断续续学习了几次，最近一次才找到一 些感觉，觉得算是入了门。
就从hello world自动生成Makefile开始说起, 参考AutoMake官方手册。

### hello项目文件
hello.c  源文件
	#include <stdio.h>
	#include <config.h>

	int main(int argc, char *argv[])
	{
		puts("hello, autotools");
		puts("this is " PACKAGE_STRING ".");
		return 0;
	}

Makefile.am Automake 配置指令文件
	noinst_PROGRAMS=hello
	hello_SOURCES=hello.c

configure.ac Autoconf 配置指令文件
	AC_PREREQ([2.69])
	AC_INIT([hello], [2.0], [email])
	AM_INIT_AUTOMAKE([-Wall -Werror foreign])
	AC_CONFIG_SRCDIR([hello.c])

	dnl check for programs
	AC_PROG_CC

	AC_CONFIG_HEADERS([config.h])
	AC_CONFIG_FILES([
		Makefile
	])
	AC_OUTPUT

autogen.sh configure和Makefile自动生成脚本
	#!/bin/bash
	test -n "$srcdir" || $srcdir=`dirname "$0"`
	test -n "$srcdir" || $srcdir=.
	(
		cd "$srcdir" &&
		autoreconf --force -v --install
	) || exit
	test -n "$NOCONFIGURE" || "$srcdir/configure" "$@"

### configure.ac解析  
configure.ac使用M4宏指令，被Autoconf和Automake读取，Autoconf读取以AC\_开始的宏
Automake读取以AM\_开头的宏  
AC\_PREREQ 设置Autoconf所需最小版本  
AC\_INIT 设置软件包名字，版本和bug报告地址, 在config.h生成宏定义中  
AM\_INIT\_AUTOMAKE 初始化Automake选项，-Wall -Werror与编译器编译选项相同，但与编译器无关。它们告诉
Automake在执行时打开警告并将警告当作错误处理  
AC\_CONFIG\_SRCDIR 检查源文件是否存在  
AC\_PROG\_CC 检查编译器，设置CC变量  
AC\_CONFIG\_HEADERS 生成配置头文件  
AC\_CONFIG\_FILES 生成的Makefile列表，添加子目录时，要添加相应目标Makefile文件  
AC\_OUTPUT 执行文件生成操作

### Automake配置文件Makefile.am解析
noinst\_PROGRAMS 定义不必安装的目标程序列表  
bin\_PROGRAMS 定义安装的bin目录下的目标程序列表  
hello\_SOURCES 定义生成hello目标程序所需的源文件
