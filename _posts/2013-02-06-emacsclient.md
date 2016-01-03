---
layout: post
title: "emacsclient对emacs的启动进行加速"
---
#### 起因 ####
用emacs有一段时间了,随着配置的增加emacs的开启会越来越慢, 为了提高开启速度 可以创建一个emacs的守护进程

`emacs --daemon`

每次开机的时候运行一次就可以了  

以后用emacsclient 去打开emacs,可以提高不少加载速度

`emacsclient -c`    GUI模式下打开

`emacsclient -t`    终端模式下打开

如果经常用emacs可以在~/.profile 添加一条

`emacs --daemon`

这样每次开机的时候就可以启动了

#### 设置时出现的问题 ####
在设置时出现了 

(0.4)的问题(具体错误信息记不清了)

原因是以前在GUI下隐藏工具栏的代码出现了问题

可以改为

`(tool-bar-mode nil)`

#### defunct ####

在开机测试的时间发现多了一个 标记为 **defunct** 的进程

defunct在Linux表示僵尸进程  

设置开机启动emacs -daemon 时在.profile 中这多加了个& 导致出现该进程
