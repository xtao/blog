---
layout: post
title: Beijing MongoDB User Group 2th Meetup
description: "北京 MongoDB 用户组第二次线下活动"
modified: 2015-01-11
category: articles
tags: [MongoDB]
comments: true
share: true
---

北京 MongoDB 用户组第二次线下活动

### 主要流程

1. MongoDB 2.8 版本特性与功能
1. MongoDB 在赶集网的应用
1. MongoDB 最佳实践

### MongoDB 2.8 版本特性与功能

TJ MongoDB 开发者

TJ 强调 MongoDB 没有实际意义上的锁，只有 Latch，门栓。

2.6 库级锁 Latch，没有 Lock，写内存的一刹那锁住内存

2.8 无锁的 MVCC 并发，WIREDTIGER，snapshot isolation

- 2.6 MMAP 内存映射，库级锁
- 2.8 MMAP 集合级锁
- 2.8 WIREDTIGER 无锁

+ WIREDTIGER 存储模式
    + LSM(HBASE, Cassandra) - Log Structured Merge
    + B-TREE

- LSM
    - 数据写内存，异步写硬盘
    - 读性能有问题，性能一般

高并发写应用场景

- 压缩算法
    - SNAPPY - 默认压缩方式，速度快，压缩率OK，32k cache 压缩
    - ZLIB - 压缩率高，占用 CPU 高，随时压缩，凑够 32k 落盘

更大的复制集群数: 12 - 50

- 2.6 Logging 控制
- 2.8 Logging 可控制

- 更新的工具集
    - Go 语言重写
    - 多线程
    - 支持输入校验

- 增强的导入导出性能

- 运维 OpsManager
    - 监控，备份，集群管理

### MongoDB 最佳实践

京东 DBA

1. 复制集
1. 分片架构
1. 监控和备份

- MS 不支持读写分离，不支持 HA，已放弃 MS 架构
- 都是副本级架构
- 部署奇数个节点
- Arbiter
- primary 和 Secondary 一般放在2个机柜里, 机器是双电, 多机房

- write concern 的配置，会导致丢数据，默认的配置安全级低？

- replicate set architecture
    - 官方不建议开启读写分离
    - read preference primary

- sharding architecture

- monitor
    - zabbix + server status + rs status

- backup
    - 全量备份
    - 延迟备份
    - 增量备份和增量恢复

- 使用
    - 商品信息，大字段，很多不固定的属性(NoSQL)
    - 比价系统
    - 关注系统
    - 日志类，手机短信 (TokuMX压缩，数据量比较大，不稳定，半夜重启，正在测试 2.8 的压缩性能)
    - 用户访问日志 (TokuMX)

### MongoDB 在赶集网的应用

赶集网 DBA

1. 选型失败的案例
1. 选型成功的案例
1. 服务化与运维

- 失败案例
    - 发布审核
        - 发帖过滤，实时性要求高
        - 编辑审核，条件非常复杂
        - 时间段集中，高峰期大量读写

    - 失败原因
        - mongodb 1.8 全局锁，读写超时非常严重
        - 直到 2.0 DB 级锁，才有单库单表设计
        - 技术选型过于激进，调研及测评不够充分

    - 社区系统
        - 基于地理位置， mongodb 二级索引
        - schema free，无传统 DB DDL 负担
        - document 丰富的数据结构类型

    - 失败原因
        - 不支持 join，数据库支持反范式
        - 过多内联数据，索引庞大，性能过低
        - 过多聚合及计算功能，数据库不堪重负

- 成功案例
    - pv 浏览技术

    - redis: 浪费内存，数据持久化慢
    - ssdb: 解决内存问题，但仍需客户端实现分片
    - mongodb: 自动分片，弱跨机房应用
    - other?

    - 用户中心
        - 之前用 MySQL，13年底全部切到 MongoDB
        - 提供登录，注册，收藏功能
        - 第三方登录，绑定，手机注册认证，绑定

    - mongodb 2.7.7，32 replicate sets，128 shards
    - 18 台 2U，64G，普通磁盘，raid 0
    - 线上 13 亿，

- Proxy vs DBrouter
    - tddl
    - cobar, atlas

- 服务高可用
    - 服务端 netscaler 硬件负载均衡
    - 优先选本机 mongos，失败后请求 LVS

- 运维3板斧
    - 归档: 隔离冷数据，提高内存利用率
    - SSD: scale up，提升单机性能
    - 分片: scale out，mongo 提前分片，横向扩展

- 跨机房问题
    - 慎用 IP，尽量用内网 DNS
    - 特别是 config，迁移时需要重启集群
    - MongoDB 无跨机房 replset mirror 的概念

- 硬件差异问题
    - 主从复制延迟，slaveok出现数据不一致
    - api 分级，个别功能调用 getlasterr 确保关键业务写入成功
    - 调整 votes 和 priority，使高性能主库抢占

- 磁盘与内存
    - mongodb 使用磁盘非常豪放，定期瘦身
    - 小步快跑，内存脏页均匀刷入磁盘，减少IO冲击

- 官方jira，别人采的坑
