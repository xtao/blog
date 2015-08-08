---
layout: post
title: Zimbra internal certificate expired
description: "Zimbra 证书过期"
modified: 2014-11-14
category: articles
tags: [Zimbra]
comments: true
share: true
---

Zimbra 用了一年，内部证书竟然过期了，启动的时候报错:

~~~ bash
$ sudo /etc/init.d/zimbra start
Host xxxx
Starting ldap...Done.
Unable to determine enabled services from ldap.
Enabled services read from cache. Service list may be inaccurate.
Starting zmconfigd...Done.
Starting logger...Failed.
Starting logswatch...ERROR: service.FAILURE (system failure: ZimbraLdapContext)     (cause: javax.net.ssl.SSLHandshakeException     sun.security.validator.ValidatorException: PKIX path validation failed:     java.security.cert.CertPathValidatorException: signature check failed)
zimbra logger service is not enabled!  failed.

Starting mailbox...Done.
Starting memcached...Done.
Starting antispam...Done.
Starting antivirus...Done.
Starting snmp...Done.
Starting mta...Done.
Starting stats...Done.
~~~

网上搜了以下，确定原因是证书过期，算了下时间，刚好一年了。

找到一个修改方法，试了一下，问题消失了，方法如下(在zimbra启动后，做下面的修改，然后重启zimbra):

~~~ bash
sudo /opt/zimbra/bin/zmcertmgr createca -new
sudo /opt/zimbra/bin/zmcertmgr createcrt -new -days 365
sudo /opt/zimbra/bin/zmcertmgr deploycrt self
sudo /opt/zimbra/bin/zmcertmgr deployca
sudo /etc/init.d/zimbra restart
~~~

最后说下我的系统，是Ubuntu 10.04.1 LTS，zimbra版本是zcs-7.1.3_GA_3346。
