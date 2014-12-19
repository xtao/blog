---
layout: post
title: 2014 ArchSummit AliYun Note
description: "2014 云平台技术全景剖析总结"
modified: 2014-12-19
category: articles
tags: [AliYun,ArchSummit]
comments: true
share: true
---

2014 ArchSummit 阿里云专场 云平台技术全景剖析

### 主要流程

1. 云计算架构实践
1. 阿里云虚拟化自研之路
1. 阿里云 CDN
1. 分析数据库 ADS

### 云计算架构实践

感觉说了一些分布式系统遇到的问题，以及设计原则。

比如:

1. 计算节点，存储节点，分离
1. 容错容灾，不能人为维护
1. 负载均衡

### 阿里云虚拟化自研之路

阿里云在虚拟化的一些工作，没太记住，各种动态升级，打补丁，迁移问题。

### 阿里云 CDN

这个我觉得讲的最好了，讲了阿里系 CDN 这几年的发展，以及架构变迁。

首先，CDN 是内容分发网络，阿里有 5 到 6 年的积累。

1. 2008-2011 淘宝 CDN
1. 2011-2014 阿里 CDN
1. 2014 阿里云 CDN

2009 年之前采用的是: 3DNS, NetScaler, Squid, NetCache。

2009 - 2011 采用的是: GTM, LVS, HAProxy, Squid。

2012 - 2013: Pharos, LVS, HAProxy, Tengine, Swift。

到现在，已经遍布全球 30 个国家，有 260+ 的节点。7Tbps 的带宽，1.6Tbps 防 DDoS。 单机柜，单节点 40Gbps。

现在的架构:

1. Pharos

    一个自己研发的 DNS 服务器。

1. CMOS

    一个自己研发的实时调度系统。

1. LVS

    四层负载，采用的是 DR 模式。

1. Tengine

    七层负载。

1. Swift

    Swift 阿里 HTTP 缓存服务器 ，没有使用文件系统，裸盘读写。

杂七杂八:

~~~
二级cache，一级cache，回源
组件分层
红帽，CentOS
应用层
DNS 服务器: Pharos 自助研发调度系统
实时调度系统 : CMOS 5分钟级准实时 LDNS
Pharos + CMOS 架构
调度准确性的重要数据，IP地址库，包裹什么的校准
阿里CDN节点系统
LVS 四层 DR 模式
Tengine 七层负载
Swift 阿里 HTTP 缓存服务器 ，没有使用文件系统，裸盘读写
万兆网络
Swift的文件存储
TCOSS
三层存储 内存-SSD-SATA
根据对象热度决定到哪层
TCP 协议栈优化
页面优化
trim,智能gzip，SDCH，Combo
阿里4层防攻击: AliGuard
基于 DPDK，旁路设备
七层： TMD 系统
TMD
模块化
socketpair
7层防攻击：Web 应用防火墙
基于 Tengine 的模块 WAF
可运维性改进
域名管理，不再依赖配置文件
不需要 reload
~~~

### 分析数据库 ADS

2011 年开始做，做了 3 年。一个阿里的实时大数据库系统。

架构变迁:

redis -> hbase 0.90 -> mysql proxy

为了统一接口，方便用户接入，节省不必要的接口问题，他们最终采用了 MySQL 协议。
用 Java 实现完整的 MySQL 协议。

Fault tolerance -> Tail tolerance
