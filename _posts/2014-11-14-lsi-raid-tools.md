---
layout: post
title: LSI RAID tools
description: "LSI RAID 卡工具总结"
modified: 2014-11-14
category: articles
tags: [LSI,MegaCLI,Douban]
comments: true
share: true
---

LSI 是一家硬件 RAID 制造商，许多第三方都是贴牌 LSI 的 RAID 卡，例如 DELL 的 PERC。

## 分类

1. LSI FusionMPT

    低端货，功能比较简单，仅支持 RAID0 和 RAID1

1. LSI FusionMPT SAS2

    低端货，新版本，功能比较简单

1. LSI MegaRAID

    高端货，但是已经不推荐使用了，可许多服务器上仍然是这个卡

1. LSI MegaRAID SAS

    高端货，目前最新的

1. LSI MegaRAID SAS (megasr crap)

    似乎不受待见?

1. LSI MegaIDE

    似乎不受待见?

在豆瓣目前的几种硬件 RAID 卡:

1. 超微:

> 02:00.0 Serial Attached SCSI controller [0107]: LSI Logic / Symbios Logic SAS2308 PCI-Express Fusion-MPT SAS-2 [1000:0086] (rev 05)

> 04:00.0 Serial Attached SCSI controller [0107]: LSI Logic / Symbios Logic SAS2116 PCI-Express Fusion-MPT SAS-2 [Meteor] [1000:0064] (rev 02)

1. DELL:

> 03:00.0 RAID bus controller: LSI Logic / Symbios Logic MegaRAID SAS 2008 [Falcon] (rev 03)

> 03:00.0 RAID bus controller [0104]: LSI Logic / Symbios Logic MegaRAID SAS 2108 [Liberator] [1000:0079] (rev 05)

## SAS2IRCU
对应驱动: mpt2sas

[下载链接](http://www.lsi.com/support/Pages/download-results.aspx?component=Storage+Component&productfamily=Host+Bus+Adapters&productcode=0&assettype=Miscellaneous&productname=&keyword=SAS2IRCU)

SAS2IRCU 支持的设备: Fusion-MPT SAS-2

|LSI SAS 2004|
|LSI SAS 2008|
|LSI SAS 2108|
|LSI SAS 2208|
|LSI SAS 2304|
|LSI SAS 2308|
|Dell PERC H200, H700|
|IBM System x3200 M2 (4367, 4368)|
|IBM System x3200 M3 (7327, 7328)|
|IBM System x3250 M2 (4190, 4191, 4194)|
|IBM System x3250 M3 (4251, 4252, 4261)|
|IBM System x3350 (4192, 4193)|
|IBM System x3400 (7973, 7974, 7975, 7976)|
|IBM System x3400 M2 (7836, 7837)|
|IBM System x3455 (7940, 7941)|
|IBM System x3500 (7977)|
|IBM System x3500 M2 (7839)|
|IBM System x3550 (7978, 1913)|
|IBM System x3550 M2 (7946, 4198)|
|IBM System x3650 (7979, 1914)|
|IBM System x3650 M2 (7947, 4199)|
|IBM System x3650 NAS (7979)|
|IBM System x3655 (7985, 7943)|
|IBM System x3755 (8877, 7163)|
|IBM System x3850 M2 (7141, 7144, 7233, 7234)|
|IBM System x3850 X5 (7145, 7146)|
|IBM System x3950 M2 (7141, 7233, 7234)|
|IBM System x3950 X5 (7145)|

SAS2IRCU 仅俱备一些简单的 RAID 卡管理功能，相比 MegaCLI 要弱很多。

### 常用命令

查看所有信息：

~~~ bash
sudo /opt/lsi/sas2ircu 0 DISPLAY
~~~

## MegaCLI
对应驱动: megaraid_sas

[下载链接](http://www.lsi.com/downloads/Public/RAID%20Controllers/RAID%20Controllers%20Common%20Files/8.07.14_MegaCLI.zip)

MegaCLI 支持的设备: LSI Logic / Symbios Logic MegaRAID SAS

MegaCLI 功能比较强大，除了 RAID 卡的管理功能外，他的日志功能很好用，可以有效的判断硬盘是否有坏道，及时更换。

### 常用命令

显示所有逻辑磁盘组信息：

~~~ bash
sudo megacli -LDInfo -LALL -aAll
~~~

查看物理磁盘状态:

~~~ bash
sudo megacli -PDList -aAll | grep Firmware
~~~

移除硬盘前的准备: 标记 offline，标记 missing，标记 removal

~~~ bash
sudo megacli -PDOffline -PhysDrv ‘[32:2]’ -a0
sudo megacli -PDMarkMissing -PhysDrv ‘[32:2]’ -a0
sudo megacli -PDPrpRmv -PhysDrv ‘[32:2]’ -a0
~~~

亮灯:

~~~ bash
sudo megacli -PdLocate -start -PhysDrv \[32:2\] -aALL
sudo megacli -PdLocate -stop -PhysDrv \[32:2\] -aALL
~~~

创建裸设备的 RAID0:

~~~ bash
sudo megacli -CfgLdAdd -r0 [32:5] WT NORA Direct -a0
~~~

### TIPS

一般在日志里看到 `WARNING: Predictive failure: PD 0d(e1/s3)` 类似这样的内容，就意味着硬盘有坏道了，可以通过查看 `Media Error Count` 和 `Other Error Count` 确认。

## 参考链接
http://hwraid.le-vert.net/wiki/LSI
