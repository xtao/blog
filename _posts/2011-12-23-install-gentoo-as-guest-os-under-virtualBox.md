---
layout: post
title: Install Gentoo as guest OS under VirtualBox
description: "在 VirtualBox 下安装 Gentoo"
modified: 2014-11-14
category: articles
tags: [Gentoo, VirtualBox]
comments: true
share: true
---

1. 使用的VirtualBox版本为4.1.6,我的笔记本的原生操作系统体Mac OS X Lion。在创建虚拟机的时候，VirtualBox的选项里有Gentoo的选项，所以创建虚拟机后直接按照Handbook里面的步骤安装系统即可，不需要额外的设置，系统就可以成功安装。
2. 因为是Virtual的Guest OS，而我又要安装X，有GUI的需求，所以必须安装VirtualBox相关的软件，参考文章2。

## 配置鼠标和显卡驱动

File: /etc/make.conf

~~~
INPUT_DEVICES=”virtualbox evdev”
VIDEO_CARDS=”virtualbox”
~~~

## 安装显卡鼠标相应软件包

~~~
emerge -av xf86-video-virtualbox xf86-input-virutalbox virtualbox-guest-additions
rc-update add virtualbox-guest-additions default
~~~

## 安装 Awesome

~~~
echo “x11-wm/awesome dbus doc” >> /etc/portage/package.use
emerge -av awesome
~~~

参考文章:

1. http://www.gentoo.org/doc/en/handbook/handbook-x86.xml
1. http://en.gentoo-wiki.com/wiki/Virtualbox_Guest
1. http://en.gentoo-wiki.com/wiki/Awesome
