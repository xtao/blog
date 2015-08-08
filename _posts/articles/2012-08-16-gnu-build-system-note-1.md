---
layout: post
title: GNU build system note 1
description: "GNU 构建系统笔记"
modified: 2014-11-14
category: articles
tags: [GNU, Make]
comments: true
share: true
---

## 基本流程

1. 编辑 configure.ac 文件，初始化项目
1. 编辑 Makefile.am 以及各个需要 Makefile 的子目录中的 Makefile.am
1. 运行 autoscan，自动检测依赖，生成 configure.scan，作为参考，把需要的内容放到 configure.ac 中
1. 运行 autoreconf -i 更新文件
1. ./configure
1. make

## 包含的基本工具

1. Autoconf
1. Automake
1. Libtool

configure.ac:

~~~
AC_INIT([incep], [1.0.1.0], [xutao881001@gmail.com])
AC_CONFIG_AUX_DIR([build-aux])
AM_INIT_AUTOMAKE([foreign -Wall -Werror -Wportability])
AC_PROG_CC
AC_REVISION([$Revision: 590 $])

AM_PATH_PYTHON([2.4])
AC_CONFIG_FILES([Makefile log/Makefile])
# Call hand’s ./configure script recursively.
AC_CONFIG_SUBDIRS([wolf])
AC_OUTPUT
~~~

Makefile.am:

~~~
# Build the library in the hand subdirectory first.
SUBDIRS = wolf log
~~~
