---
layout: post
title: Gentoo Prefix SYNC Source
description: "新的 Gentoo Prefix 源"
modified: 2015-04-04
category: articles
tags: [Gentoo, Prefix]
comments: true
share: true
---

我是在 MacOSX 装的 Gentoo Prefix，之前好久 `emerge --sync` 因为源有问题，导致我一直是更新失败的。

最近从网上，找到了另外一个源，很好用，我终于又可以更新软件了。

新的:

~~~
SYNC="rsync://rsync.prefix.bitzolder.nl/gentoo-portage-prefix"
~~~

原来的:

~~~
SYNC="rsync://rsync.us.prefix.freens.org/gentoo-portage-prefix"
SYNC="rsync://rsync.eu.prefix.freens.org/gentoo-portage-prefix"
~~~
