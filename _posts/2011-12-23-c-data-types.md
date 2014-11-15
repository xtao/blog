---
layout: post
title: C data types
description: "C 数据类型总结"
modified: 2014-11-14
category: articles
tags: [C]
comments: true
share: true
---

最近在写 linux 下面的 c 程序，突然面对众多的变量类型无所是从，因此搜索了一下，总结如下：

首先是涉及到的几个文件:

1. stddef.h
2. stdint.h
3. inttypes.h

## stddef.h

在我的 Gentoo 系统上搜索 stddef.h

~~~
/usr/include/linux/stddef.h
/usr/lib64/bcc/include/stddef.h
/usr/lib64/gcc/x86_64-pc-linux-gnu/4.5.3/include/stddef.h
/usr/share/man/man0p/stddef.h.0p.bz2
/usr/src/linux-3.0.6-gentoo/include/linux/stddef.h
~~~

可以看出在 /usr/include/ 下面并没没有 stddef.h 文件，然后我查看 /usr/include/linux/stddef.h 这个文件

~~~
1 #ifndef _LINUX_STDDEF_H
2 #define _LINUX_STDDEF_H
3
4
5
6 #undef NULL
7 #if defined(__cplusplus)
8 #define NULL 0
9 #else
10 #define NULL ((void *)0)
11 #endif
12
13
14 #include /* newer gcc includes this */
15 #ifndef offsetof
16 #define offsetof(TYPE, MEMBER) ((size_t) &((TYPE *)0)->MEMBER)
17 #endif
18
19 #endif
~~~

按照这个文件所说，stddef.h 这个文件应该由 gcc 提供的。

/usr/lib64/gcc/x86_64-pc-linux-gnu/4.5.3/include/stddef.h 这个文件的内容很多，我就不贴了，之后我 man 了一下 stddef.h

~~~
DESCRIPTION
The header shall define the following macros:

NULL Null pointer constant.

offsetof(type, member-designator)

Integer constant expression of type size_t, the value of which is the offset in bytes to the structure member (member-designator), from the begin‐
ning of its structure (type).

The header shall define the following types:

ptrdiff_t
Signed integer type of the result of subtracting two pointers.

wchar_t
Integer type whose range of values can represent distinct wide-character codes for all members of the largest character set specified among the locales supported by the compilation environment: the null character has the code value 0 and each member of the portable character set has a code value equal to its value when used as the lone character in an integer character constant.

size_t Unsigned integer type of the result of the sizeof operator.

The implementation shall support one or more programming environments in which the widths of ptrdiff_t, size_t, and wchar_t are no greater than the width
of type long. The names of these programming environments can be obtained using the confstr() function or the getconf utility.
~~~

这样就清楚了很多:

1. NULL 一个宏，NULL指针常量
2. size_t sizeof 的返回值，类型是 unsigned integer

    在 32bit 的机器下，是unsigned int(32位系统上是32位无符号整形);在64bit的机器下是unsigned long(64位系统上是64位无符号整形)

3. wchar_t 宽字符类型
4. ptrdiff_t 两个指针的差，类型是 signed interger

stdint.h vs inttypes.h

stdint.h 与 inttypes.h 之间貌似存在着争执，inttypes.h 是 C99 提出的标准.
这两个文件主要就是定义了 int 类型的长度，针对不同机器 int 的长度可能不一致，尤其是在嵌入式开发。

我们放下争执，最主要的就是定义了以下几种类型:

1. int8_t
2. uint8_t
3. int16_t
4. uint16_t
5. int32_t
6. uint32_t
7. int64_t
8. uint64_t

[1] http://en.wikipedia.org/wiki/Stdint.h#stdint.h

[2] http://blog.csdn.net/zhengxinjian_2009/article/details/6729342
