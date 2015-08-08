---
layout: post
title: Zimbra DNS
description: "Zimbra DNS 配置"
modified: 2014-11-14
category: articles
tags: [Zimbra]
comments: true
share: true
---

在安装完 Zimbra 后，发现一个问题，就是从 Zimbra 服务器向 Gmail 发送的邮件，总是被 Gmail 认为是垃圾邮件。

于是开始追踪问题原因。

1. 首先Gmail提示的信息是

~~~
This message was likely forged and did not originate from your account.
~~~

根据提示信息，猜测邮件被判为垃圾邮件的原因是伪造(forged)。

2. 查看邮件原始信息。

用 Gmail 的 Show original 功能查看邮件原始信息

~~~
Delivered-To: xxx@gmail.com
Received: by 10.100.124.12 with SMTP id w12cs81149anc;
Sat, 29 Oct 2011 10:31:19 -0700 (PDT)
Received: by 10.42.158.3 with SMTP id f3mr11450462icx.7.1319909477474;
Sat, 29 Oct 2011 10:31:17 -0700 (PDT)
Return-Path:
Received: from xx.xx.cn ([218.240.39.246])
by mx.google.com with ESMTP id id10si6607076icc.50.2011.10.29.10.31.16;
Sat, 29 Oct 2011 10:31:16 -0700 (PDT)
Received-SPF: error (google.com: error in processing during lookup of xtao@xx.xx.cn: DNS timeout) client-ip=xx.xx.xx.xx;
Authentication-Results: mx.google.com; spf=temperror (google.com: error in processing during lookup of xtao@xx.xx.cn: DNS timeout) smtp.mail=xtao@xx.xx.cn
Received: from localhost (localhost.localdomain [127.0.0.1])
by xx.xx.cn (Postfix) with ESMTP id 9D1CE202CAC
for ; Sun, 30 Oct 2011 01:33:45 +0800 (CST)
X-Virus-Scanned: amavisd-new at scilab.org.cn
Received: from xx.xx.cn ([127.0.0.1])
by localhost (xx.xx.cn [127.0.0.1]) (amavisd-new, port 10024)
with ESMTP id aNirEPwnBATS for ;
Sun, 30 Oct 2011 01:33:45 +0800 (CST)
Received: from xx.xx.cn (mail.xx.xx.cn [xx.xx.xx.xx])
by xx.xx.cn (Postfix) with ESMTP id 0AC0D202C92
for ; Sun, 30 Oct 2011 01:33:45 +0800 (CST)
Date: Sun, 30 Oct 2011 01:33:44 +0800 (CST)
From: Tao Xu
Reply-To: XTAO
To: XTAO
Subject: UU
Message-ID:
Content-Type: text/plain; charset=utf-8
Content-Transfer-Encoding: 7bit
MIME-Version: 1.0
X-Originating-IP: [xxx.xxx.xx.xx]
X-Mailer: Zimbra 7.1.3_GA_3346 (ZimbraWebClient – GC15 (Mac)/7.1.3_GA_3346)

Test
~~~

查看后发现一个 Error，就是在 Received-SPF 那一行，经过 google，发现这是 DNS 的设置问题。

3. 修改DNS。

在 DNS 管理网站，添加 TXT 记录，内容为 "v=spf1 ip4:xx.xx.xx.xx -all"，xx.xx.xx.xx 为邮件服务器的IP。

4. 测试OK，邮件不在被Gmail认为是垃圾邮件了。

~~~
Delivered-To: xxx@gmail.com
Received: by 10.100.124.12 with SMTP id w12cs81149anc;
Sat, 29 Oct 2011 10:31:19 -0700 (PDT)
Received: by 10.42.158.3 with SMTP id f3mr11450462icx.7.1319909477474;
Sat, 29 Oct 2011 10:31:17 -0700 (PDT)
Return-Path:
Received: from xx.xx.cn ([218.240.39.246])
by mx.google.com with ESMTP id id10si6607076icc.50.2011.10.29.10.31.16;
Sat, 29 Oct 2011 10:31:16 -0700 (PDT)
Received-SPF: pass (google.com: domain of xtao@xx.xx.cn designates xx.xx.xx.xx as permitted sender) client-ip=218.240.39.246;
Authentication-Results: mx.google.com; spf=pass (google.com: domain of xtao@xx.xx.cn designates xx.xx.xx.xx as permitted sender) smtp.mail=xtao@xx.xx.cn
Received: from localhost (localhost.localdomain [127.0.0.1])
by xx.xx.cn (Postfix) with ESMTP id 9D1CE202CAC
for ; Sun, 30 Oct 2011 01:33:45 +0800 (CST)
X-Virus-Scanned: amavisd-new at scilab.org.cn
Received: from xx.xx.cn ([127.0.0.1])
by localhost (xx.xx.cn [127.0.0.1]) (amavisd-new, port 10024)
with ESMTP id aNirEPwnBATS for ;
Sun, 30 Oct 2011 01:33:45 +0800 (CST)
Received: from xx.xx.cn (mail.xx.xx.cn [xx.xx.xx.xx])
by xx.xx.cn (Postfix) with ESMTP id 0AC0D202C92
for ; Sun, 30 Oct 2011 01:33:45 +0800 (CST)
Date: Sun, 30 Oct 2011 01:33:44 +0800 (CST)
From: Tao Xu
Reply-To: XTAO
To: XTAO
Subject: UU
Message-ID:
Content-Type: text/plain; charset=utf-8
Content-Transfer-Encoding: 7bit
MIME-Version: 1.0
X-Originating-IP: [xxx.xxx.xx.xx]
X-Mailer: Zimbra 7.1.3_GA_3346 (ZimbraWebClient – GC15 (Mac)/7.1.3_GA_3346)

Test
~~~

参看文章: http://en.wikipedia.org/wiki/Sender_Policy_Framework
