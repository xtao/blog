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

~~~
echo "1 eth1" >> /etc/iproute2/rt_tables

ip route add 10.200.6.0/24 dev eth1 src 10.200.6.55 table eth1
ip route add default via 10.200.6.201 dev eth1 table eth1

ip rule add from 10.200.6.55/32 table eth1
ip rule add to 10.200.6.55/32 table eth1

ip route flush table eth1
~~~
