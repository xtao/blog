---
layout: post
title: 2014 ArchSummit Yun Note
description: "2014 真实的云计算总结"
modified: 2014-12-20
category: articles
tags: [Yun,ArchSummit]
comments: true
share: true
---

2014 ArchSummit 真实的云计算

### 主要流程

1. 巨衫
1. 友盟
1. 七牛
1. Amazon
1. 青云
1. UnitedStack
1. 知道创宇

#### 巨衫

[Slide](http://pan.baidu.com/s/1o65czEE)

巨衫自己写了一个 NoSQL ，针对于文档存储的，叫做 SequoiaDB，支持事务。

我来晚了，只听了后面的，提到了二段提交，CAP。

传统数据库是 CA，内存数据库是 AP，MongoDB 等是 PA。

#### 友盟
[Slide](http://pan.baidu.com/s/1sj2o1Mt)

友盟目前有 5PB 的移动数据，每天还以 5TB 为单位增长。

统计产品主要包含 3 部分：SDK，实时计算，离线计算。

关于 SDK

1. SDK 要小，不同的客户会有不同的大小需求
1. SDK 数据包一开始是用的 JSON，后来采用了二进制的数据包格式(Thrift)
1. 向服务器发送数据，采取多种方式，满足实际业务
1. 数据包去重，多重校验
1. SDK 端按规则调整，服务器端动态调整

推荐了一本书 [Big Data - Principles and best practices of scalable realtime data systems](http://book.douban.com/subject/10438832/)

友盟其实 MongoDB 用的非常早，以至于踩坑无数。

技术站，基本采用的是 Twitter 推荐的，Scala，Finagle。

数据库 shard 是应用内做的。

存储空间不够，就改进 HBase 压缩算法，用 LZMA 替代原来的 LZO。LZMA 压缩确实消耗 CPU 资源，但是解压相对来说还好。

做了一个大容量的在线小数据访问系统，HBase + KVProxy (友盟自己写的，Cache + Proxy)

友盟使用的 Hadoop 是 CDH 版本，似乎是 0.4，使用自己打 Patch 的方式升级。

#### 七牛

[Slide](http://pan.baidu.com/s/1bndE7Kj)

讲了七牛的历史，怎么一步一步成长起来的，以满足用户需求为主。

比较有意思的是，当很大的数据要拷贝到七牛机房的时候，他们采取了快递的方式，一开始只是几块硬盘(七牛盒子)，到后来的是机柜。。。

他们还做了一个镜像存储的东西，其实就是 Online 的 CDN 回源，回源过程中，也迁移了数据到七牛机房。

#### Amazon

[Slide](http://pan.baidu.com/s/1bnrlHeR)

高大上的 AWS，亚马逊的一揽子解决方案。

作者主要讲了一下怎么用 AWS 构建高可用的应用，通俗易懂，最佳实践。

原来亚马逊已经把业务的 SLA 提高到了一个很高的水平了，特别是 route53 的 SLA 竟然是 100%。

另外，使用过程中，要明确亚马逊的 region，az，edge location 等概念。

最后，有 2 句话很好:

1. Everything fails all the time.
1. Design for failure.

#### 青云
[Slide](http://pan.baidu.com/s/1kT1KJ9H)

听完之后，只有一个感觉: 霸气。绝对是霸气十足的演讲。

介绍了一下青云的架构。

云平台的设计目标

1. 超大规模
1. 超高性能
1. 高度伸缩
1. 企业级安全

云平台的设计原则

1. 资源软件化
1. 组件标准化

资源软件化

1. Software defined network
1. Software defined storage
1. Software defined computing & operation


#### UnitedStack

[Slide](http://pan.baidu.com/s/1dDCgzBF)

这个讲的是 Ceph，但是似乎讲的太深入了，吸引的人不多。UStack 对 Ceph 有很多改进，有很多性能优化，做的很好了。

比较关键的是 crush 算法，osdmap 灰度发布，ceph domain，fiemap，缓存改用 ARC，rados 采用的是强一致性模型。

运维方面:

1. puppet 部署
1. diamond + graphite 监控
1. zabbix 报警

#### 知道创宇

[Slide](http://pan.baidu.com/s/1jGvbmGe)

知道创宇的某某引擎发展，好几个版本，没听完，我就跑了。。。

