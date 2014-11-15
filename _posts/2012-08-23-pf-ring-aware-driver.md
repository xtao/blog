---
layout: post
title: PF_RING aware driver
description: "PF_RING 系列之 aware 驱动"
modified: 2014-11-14
category: articles
tags: [PF_RING]
comments: true
share: true
---

## 目录

1. PF_RING 系列之 aware 驱动
1. PF_RING 系列之网卡驱动
1. PF_RING 系列之 DNA 驱动
1. PF_RING 系列之内核模块
1. PF_RING 系列之 vPF_RING
1. PF_RING 系列之 libzero
1. PF_RING 系列之 libDNA
1. PF_RING 系列之 libpfring
1. PF_RING 系列之 TNAPI

## 关键词

NAPI, ixgbe, PF_RING

## 简介

### PF_RING

PF_RING 在4.X版本前需要给内核打补丁，之后则完全不需要了。
PF_RING 核心是内核的一个模块，另外加上网卡驱动，以及用户空间的代码，vPF_RING是PF_RING一个子项目，用于虚拟机。

### 网卡驱动

与 PF_RING 配合的网卡驱动分为3种

1. 普通驱动 – 随操作系统自带或者官方的驱动
1. aware 驱动 – PF_RING修改过的驱动，驱动可以识别PF_RING模块
1. DNA 驱动 – PF_RING修改过的驱动，允许跨过内核，用户程序直接操作网卡

总的来说 aware 驱动的修改不是很大，相对 DNA 驱动来说(我们后面会讨论DNA驱动)。

PF_RING 提供的驱动种类很多，本文以Intel万兆网卡驱动(ixgbe-3.7.17,PF_RING-5.4.4提供的版本)为例进行分析，其它驱动在机制上应该是类似的。

## 驱动细节

从PF_RING_aware驱动到PF_RING的内核模块传递包，有3种transparent模式(需要在载入PF_RING内核模块时指定参数)

1. 0 = standard Linux
1. 1 = direct2pfring + transparent
1. 2 = direct2pfring + non transparent

原生的驱动，PF_RING只修改了ixgbe_main.c这个文件,主要是涉及到收包的函数

### 发包相关

PF_RING_aware驱动在初始化以及发包部分没有做任何修改。

### 收包相关

添加了pf_ring_handle_skb()函数，以及修改了ixgbe_clean_rx_irq_ps()函数的内部流程

#### Driver2pf

网卡中断:

~~~
ixgbe_msix_clean_rings()
-> napi_schedule()
~~~

回调:

~~~
ixgbe_poll()
-> ixgbe_clean_rx_irq()
->> ixgbe_clean_rx_irq_ps()
->>> pf_ring_handle_skb()
->>>> hook->ring_handler()
~~~

(ring_handler()实际定义的是skb_ring_handler()，由此进入PF_RING内核模块)
开启Driver2pf方式后，PF_RING不会向内核注册struct packet_type这个结构，也就是传统的包传递(NAPI or NON-NAPI)机制对PF_RING无效。

#### Standard(默认采用NAPI机制)

网卡中断过程是一样的，回调的时候调用的函数与Driver2pf不一致

回调:

~~~
ixgbe_clean_rx_irq_ps()
-> napi_gro_receive()
(进入PF_RING内核模块)
->> packet_rcv()
->>> skb_ring_handler()
~~~

回到transparent模式上面来

1. 模式0: 只有Standard方式
1. 模式1: Standard + Driver2pf方式
1. 模式2: 只有Driver2pf方式

#### VLAN相关
另外的修改就是VLAN，原来的驱动支持去掉VLAN tags，而PF_RING需要这个VLAN tags，所以

~~~
ixgbe_vlan_stripping_enable()
-> return()
~~~

这个函数在aware驱动中直接返回，不做处理。

参考文章: [PF_RING](http://www.ntop.org/products/pf_ring/)
