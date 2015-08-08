---
layout: post
title: Multiple Route per Interface
description: "多网卡多路由表设置"
modified: 2015-04-06
category: articles
tags: [Linux, Interface]
comments: true
share: true
---

一台机器有多块网卡，给每个网卡都分配了一个外网 IP，现在需要独立使用每个 IP，
默认的策略是不行的，从网上找了一下解决办法，主要有 2 种。

## 1. rp_filter

~~~
sudo sysctl net.ipv4.conf.all.rp_filter=2
~~~

## 2. route table

~~~
echo "1 eth1" >> /etc/iproute2/rt_tables

ip route add 10.200.6.0/24 dev eth1 src 10.200.6.55 table eth1
ip route add default via 10.200.6.201 dev eth1 table eth1

ip rule add from 10.200.6.55/32 table eth1
ip rule add to 10.200.6.55/32 table eth1

ip route flush table eth1
~~~
