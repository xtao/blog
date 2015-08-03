---
layout: post
title: Beanstalkd source arch
description: "Beanstalkd 源码分析"
modified: 2015-08-03
category: articles
tags: [Beanstalkd]
comments: true
share: true
---

最近在读 Beanstalkd 的代码，这里写一点总结。基于 Beanstalkd 1.9 的代码。

## 概述
1. 单进程
1. linux 下采用 epoll 实现 non-block io 复用
1. 任务会追加到 binlog 里面，实现数据持久化，重启后，会先读取 binlog 的内容恢复队列
1. binlog 是有 gc 的
1. min heap

## 启动流程
* setlinebuf
* parse options
* create socket
* initialize protocol
* initialize signal
* check binglog(wal)
 * lock binlog dir
 * initialize binlog(wal)
 * replay binlog protocol
* start service # 这个操作之前， btd 都不会响应客户端的请求

## 主要数据结构
* Server
 * wal
 * sock
 * conns(Heap)

    系统默认只有一个 server 对象。wal 是 Beanstalkd 实现的一个 binlog 的对象。

* Conn
 * srv
 * sock
 * tube

    连接与 tube 之间的关系。每个连接都会有对应的 tube。

* tubes(ms)

    所有 tube 都会在 tubes 里面有记录。这是个全局的变量。

* default_tube(tube)

    这个一个默认的 tube。

* all_jobs(job)

    所有 job 都会在 all_jobs 里面有记录。这是个全局的变量。

## event
![srvserve Image]({{ site.url  }}/images/beantalkd-srvserve.png)

* `sockinit` 初始化 epoll/kqueue
* `listen` 监听 socket, 之前会设置 socket 对应的处理函数以及参数，`->f` 和 `->x`, 因为 `s->conns` 是一个 min heap，也要初始化一下 `->less` 和 `->rec` 方法。
* `sockwant` 添加 epoll/kqueue 监听事件
* `prottick` 处理 delay queue  和 deadline queue ，以及 conns 过期连接
* `socknext` epoll/kqueue wait, 等待 fd 事件
* `sock->f()` 对应 fd 事件处理

### 事件处理函数
* `srvaccept` accept 处理客户端的连接
* `prothandle` 处理已经建立连接的客户端请求
