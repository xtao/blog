---
layout: post
title: Little endian and Big endian
description: "关于大端和小端的一些判断方法"
modified: 2014-11-14
category: articles
tags: [C]
comments: true
share: true
---

~~~ c
int checkCPU()
{
　{
　　 union u
　　 {
　　　 int a;
　　　 char b;
　　 } c;
　　 c.a = 1;
　　 return (c.b == 1);
　 }
}
~~~

~~~ bash
$ echo I | tr -d [:space:] | od -to2 | head -n1 | awk '{print $2}' | cut -c6
~~~

~~~ perl
perl -MConfig -e 'print "$Config{byteorder}\n";'
~~~

参考资料

1. http://www.cppblog.com/goolds/archive/2011/11/21/160618.html
1. http://serverfault.com/questions/163487/linux-how-to-tell-if-system-is-big-endian-or-little-endian
1. http://stackoverflow.com/questions/2610849/finding-if-the-system-is-little-endian-or-big-endian-with-perl
