---
layout: post
title: Docker mount sysfs readonly
description: "Docker 挂载 sysfs 只读问题"
modified: 2014-11-24
category: articles
tags: [Docker]
comments: true
share: true
---

环境：某台运行 docker 的 CentOS 机器。

起因：员外需要调优硬盘的 schedule，但是发现 `echo “deadline”> /sys/block/xvdb1/queue/scheduler` 报错，没有权限。

结论：发现凶手是 docker ，只要启动任意 container，就会 remount sysfs，把其权限改为只读。

首先，使用 mount 命令，查看 /sys 的权限是无效的，显示与 /etc/fstab 的权限一致，但实际权限已经被改为只读的了。查看 `/proc/mounts` 才能看到权限改动。

~~~
[xutao@node1 ~]$ cat /proc/mounts
rootfs / rootfs rw 0 0
proc /proc proc rw,relatime 0 0
sysfs /sys sysfs rw,relatime 0 0
devtmpfs /dev devtmpfs rw,relatime,size=24676828k,nr_inodes=6169207,mode=755 0 0
devpts /dev/pts devpts rw,relatime,gid=5,mode=620,ptmxmode=000 0 0
tmpfs /dev/shm tmpfs rw,relatime 0 0
~~~

~~~
[xutao@node1 ~]$ cat /proc/mounts
rootfs / rootfs rw 0 0
proc /proc proc rw,relatime 0 0
sysfs /sys sysfs ro,relatime 0 0
~~~

搜了一下，发现有人发现了这个问题，解决方式是改用 lxc driver，docker 默认的 driver 是 libcontainer。 但我觉得这个并没有解决问题，如果真的要修改 /sys 可以 remount 一下，加上 rw 的属性即可。

于是又去 GitHub 和 docker 的源码搜了一下，发现了引入这个挂载的 PR 以及代码。

相关 PR:

1. https://github.com/docker/libcontainer/pull/109
1. https://github.com/docker/libcontainer/pull/110
1. https://github.com/docker/docker/issues/7101
1. https://github.com/docker/docker/pull/7109

对应代码:

1. RestrictSys
    https://github.com/docker/docker/blob/d176d1bfb4b59c5f1dcdebd42a8e22887ea1b80f/vendor/src/github.com/docker/libcontainer/config.go
1. RestrictSys
    https://github.com/docker/docker/blob/07996d82c76f0c73555d4de27371757e8879d378/daemon/execdriver/native/create.go
1. sysReadonly
    https://github.com/docker/docker/blob/68a25a5b74af10dd7d0af5f05556128540f9cedc/vendor/src/github.com/docker/libcontainer/mount/init.go

相关的安全问题:

1. http://blog.zx2c4.com/749
1. http://mainisusuallyafunction.blogspot.com/2012/11/attacking-hardened-linux-systems-with.html

大概是因为安全问题，才把 /sys 重新 mount 为只读的。
