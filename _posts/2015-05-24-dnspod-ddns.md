---
layout: post
title: DNSPod DDNS
description: "使用 DNSPod 做 DDNS"
modified: 2015-05-24
category: articles
tags: [DNSPod, DDNS]
comments: true
share: true
---

我家是联通的宽带，在路由器上给某台内网的主机设置了 DMZ 映射暴露给公网，为了动态的获取家里的外网 IP，尝试使用 DNSPod 做动态域名解析。

1. 说明

    * `API_TOKEN`: DNSPod 的 API Token，应该是 `ID,Key` 的组合
    * `DOMAIN_ID`: 某个域名的 ID
    * `RECORD_ID`: 某个域名的记录 ID
    * `SUB_DOMAIN`: 需要设置 DDNS 的域名名称, 例如: `www`

1. 注册 DNSPod 帐号

    请去这里: http://dnspod.cn

1. 增加域名及记录

    请去这里: http://dnspod.cn

1. 设置 API Token

    请去这里: http://dnspod.cn, 在帐号设置哪里。DNSPod 默认不保存 Key，需要自己保存一下，否则后面查不到了。

    本来可以用其他认证方式，比如用户名和密码，但是在一些公共机器上，用这个不太安全，DNSPod 最近提供了 API KEY 的功能，推荐大家用这个。以后应该会根据域名提供 API Key 权限的功能，这样就更安全了。

    另外，如果开启了 D 令牌，那认证很麻烦，为了使用 API 我关闭了 D 令牌。。。

1. 查询 domain id & record id

    * 获取 domain id

~~~
    curl -v https://dnsapi.cn/Domain.List -d "login_token=API_TOKEN&format=json"
~~~

    * 获取 domain id 下面的 record id

~~~
    curl -k https://dnsapi.cn/Record.List -d "login_token=API_TOKEN&domain_id=DOMAIN_ID&format=json"
~~~

1. 在 Linux 主机上加一个 cron


    我设置的是一个小时更新一次，另外，在访问 Recorde.Ddns 这个 URL 的时候，DNSPod 会自动记录你的公网 IP 的，所以用起来非常方便。注意不要访问太频繁，否则会被封禁的。

~~~
    sudo touch /etc/cron.d/dnspod
    cat /etc/cron.d/dnspod
    * /1 * * * root curl -X POST https://dnsapi.cn/Record.Ddns -d "login_token=API_TOKEN&format=json&domain_id=DOMAIN_ID&record_id=RECORD_ID&record_line=默认&sub_domain=SUB_DOMAIN"
~~~

1. 测试

~~~
    sudo run-parts -v /etc/cron.d
~~~
