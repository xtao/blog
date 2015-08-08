---
layout: post
title: BSD radix porting note 1
description: "BSD 的 Radix 算法小记"
modified: 2014-11-14
category: articles
tags: [BSD,Radix]
comments: true
share: true
---

## 前言

最近需要一个 IP 路由搜索的算法，看了一眼 Linux 的 HASH 和 LC-Trie 算法之后，完全搞不定，后来发现了 BSD 的 Radix，
相比而言，BSD 的算法更简单一些，于是开始分析 BSD 的 Radix，这里是我的部分总结。

看了 FreeBSD，NetBSD，OpenBSD 的 Radix 虽说本质上都是同一个Radix算法，代码也几乎一样，但是当我看到 Apple 的 Radix 时，
发现代码和注释原来可以写得这样优美，于是我决定在 Apple 的 Radix 文件上开始移植。

源文件:

1. radix.c
1. radix.h

古老的函数:

1. bzero – memset

    POSIX 里用 memset 替代

1. bcopy – memcpy, args reverse

    POSIX 里用 memcpy 替代，但是要注意参数是反着的

1. bcmp – memcmp

    POSIX 里用 memcmp 替代

在 GCC 的 string.h 的头文件里上面这三个函数还是存在的

## 步骤

1. panic() & log()

    这两个函数都被我使用空白宏替换了

1. 去除无用的头文件

    换上常用的 stdio.h, stdlib.h, string.h, sys/parm.h, stdint.h

1. 去除无用的mutex锁

    原来这个文件有几个 mutex 锁，用于初始化，我全部删掉了

1. 修改初始化函数

    原来的初始化会涉及 BSD 的内核，所以稍加修改(rn_init())

我移植的代码: [radix_for_linux](https://github.com/xtao/note/tree/master/c/radix_for_linux)
