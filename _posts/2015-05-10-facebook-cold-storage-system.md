---
layout: post
title: Facebook Cold Storage System
description: "总结一下脸书的冷存储系统"
modified: 2015-05-10
category: articles
tags: [Facebook, Storage]
comments: true
share: true
---

原文: https://code.facebook.com/posts/1433093613662262/-under-the-hood-facebook-s-cold-storage-system-

容量:

1. 机柜(Rack): 41U (也许是 42U，从图上只计算出了 41U)
2. 每机柜 16 台 2U 的机器，Open Vault OCP 标准
3. 每台机器，30 块 4 TB 硬盘，应该是 2 层的，一层 15 块
4. 每机柜一共 480 块 4 TB 的硬盘，实际容量是 1920 TB，约 2 PB 的容量

我们才刚刚用上 1U12 的机器，如果换算成 2U 的话，相当于 24 块 4 TB 的硬盘，单机柜能不能放到 32 台还待定。。。

冗余:

1. 3 副本，允许 2 块磁盘出错
2. RS(14, 10) 允许 4 块盘出错，相当于 1.4 个副本
3. RS(28, 20) 允许 8 块磁盘出错，相当于 1.4 个副本

(请不要问我 Reed Solomon 编码是什么。。。wiki: http://en.wikipedia.org/wiki/Reed%E2%80%93Solomon_error_correction)
