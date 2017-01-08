---
layout: post
title: "折腾树莓派"
description: ""
date: 2017-01-05
tags: [Linux,RaspberryPi]
comments: true
share: true
---

最近桃子同学送我了一个树莓派，所以我就折腾折腾。这里只是提供一下思路和相关资料，具体的实施就不多说了。
需求
1. 随身携带到咖啡馆进行一些简单的折腾。
2. 在家的时候当一个服务器跑一些简单的服务

# 硬件环境
* MacBook
* 树莓派3
* 一个支持hdmi的显示器
* 键盘鼠标
* 一台东京的linode
* 个人域名一个

注意，如果用树莓其实使用kali还是有一些配置需要解决的，并没有根据树莓派的特点进行相应的定制。如果没有折腾需求的话还是建议用原生的系统比较好，其实我也不知道当时怎么想的，半只脚进坑，然后全进坑了，ヽ(ﾟДﾟ)ﾉ。

# 系统安装

系统从原生换成kali还是比较很简单的，这里需要一个sd卡的烧制软件ApplePi-Baker，把kali的img下载了，然后，叮! 就好了

之后插上显示器，键鼠，等开机

刚刚烧制的系统并不是完全版的，所以在进入kali之后需要对kali进行升级，需要用apt下载些升级包

kali不会默认打开ssh的登陆所以需要自己去设置开启

# 需求实现

## 能带到咖啡馆去

这里的最主要问题是:

**如何能让树莓派开机之后通过显示器去连上咖啡馆的wifi并知道ip**

在咖啡馆刚通上电肯定是连不到有密码的wifi的，字里我们可以通过读卡器去修改/etc/network里的interface里的内容，还可以在自启动脚本中加一nmcli之类和命令去解决

但是如果像星巴克咖啡馆那种要用手机认证，暂时没的解决

## 可跑一些简单的服务

本质上就是内网穿透，本来是想用花生壳去解决的，无奈花生壳的树莓派有不少问题，而且每月和流量太少了。刚好自己有个vps和域名，那就干脆跑ngrok吧，
注意两点: 
1. ngrok的Server与client都要在同一台机器上进行编译，再scp到别的机器上
2. 域名转发星号需要注意一下，如果同时有了*.samael.cn和ssh.samael.cn那么ngrok连接不上

# 相关连接

[性能测试](https://www.howtoforge.com/how-to-benchmark-your-system-cpu-file-io-mysql-with-sysbench#-file-io-benchmark)

[ngrok配置](http://www.jianshu.com/p/91f01e30a9b0)

[开机自启动设置](https://www.denpe.com/debian-autorun/)

[VNC](http://blog.sevagas.com/?VNC-to-access-Kali-Linux-on-Raspberry-Pi)
