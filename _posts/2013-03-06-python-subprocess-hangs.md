---
layout: post
title: Python subprocess hangs
description: "使用Python的subprocess模块的时候遇到了一个问题"
modified: 2014-11-14
category: articles
tags: [Python]
comments: true
share: true
---

最近使用Python的subprocess模块的时候遇到了一个问题：

~~~
pipe = subprocess.Popen(‘xxx’, shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
out, err = pipe.communicate()
~~~

上面这段代码运行结束后，对应的Python进程会卡住，在网上搜了一下倒是找到了解决方案：添加close_fds=True这个参数。

现在来分析下这个问题，之前运行ls这样的程序，并不会卡住，只是运行像/etc/init.d/iwolfd start这样需要有个daemon进程的情况才会卡住，
猜测原因是因为后台有一直运行的一个进到导致Python对应的进程卡住的。

但是从网上搜的资料来看，都是说像stdin，stdout，stderr这样的文件没有关闭导致的，具体的原因还不清楚，可以参考下面这个issue：

http://bugs.python.org/issue7213
