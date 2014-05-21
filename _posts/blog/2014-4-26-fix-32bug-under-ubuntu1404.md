---
layout: post
title: 如何在ubuntu14.04(64位)编译运行32位程序
category: blog
---

##缘起

---

我之前是ubuntu12.04(32bit),在一次手贱的apt-get remove之后呵呵了,大家都懂的..T_T,恰逢最近ubuntu14,04新鲜出炉,于是down了一个Ubuntu14.04(64bit)的iso安装玩玩(之前是因为没没注意,所以才装的ubuntu12,04-32bit,不然应该是装ubuntu12.04-64bit的),ubuntu的安装还是很简单的,我的电脑因为买的早也没有坑爹的EFI的问题,分分钟系统就OK了,整体体验还是不错的,但是当我装完软件,开始coding的时候悲催的发现make出错了,于是各种google+baidu+oschina+stackoverflow,经过6次重装系统,最终还是被我搞定了!!爽!!现写成博客给有相同问题的人参考一下.

##我的解决方法

---

安装系统:

    Install ubuntu14.04-64bit(Trusty Tahr)

安装32位库:

    sudo apt-get install libc6:i386

用之前的源安装ia32-libs:

    sudo -i
    cd /etc/apt/sources.list.d
    echo "deb http://archive.ubuntu.com/ubuntu/ raring main restricted universe multiverse" >ia32-libs-raring.list
    apt-get update
    apt-get install ia32-libs
    rm ia32-libs-raring.list
    apt-get update
    exit

安装gcc编译时需要的一些类库:

    sudo apt-get install gcc-multilib

在gcc的时候加-m32参数
再次尝试:

    make clean
    make

##最后

---

> - 我的解决方案是不是每步都一定需要我也不知道,但是我做完这些之后,我在我的系统中就OK了
> - 我的系统环境是:Ubuntu 14.04-64bit(Trusty Tahr), gcc version 4.8.4
> - 参考的方法来源是:我oschina上问的问题:[Ubuntu14.04如何安装32位兼容库,即ia32-libs](http://www.oschina.net/question/1470892_151825)和我在stackoverflow上问的:[How to install ia32-libs in ubuntu 14.04 LTS](http://stackoverflow.com/questions/23182765/how-to-install-ia32-libs-in-ubuntu-14-04-lts)

*Andy(andy.at.working@gmail.com) 2014-04-23*
