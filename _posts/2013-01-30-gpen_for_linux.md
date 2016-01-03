---
layout: post
title: "gpen_for_linux"
---

### 起因 ###
今天翻东西,发现了以前的一个小玩意 GPEN, 这个东西可以把你的笔记记录到一个闪存里之后在电脑上查看笔记. 但是对应的软件只能在windows下使用, 现在用了Linux所以没有官方的软件.

### 解决 ###
很早就想着怎么笔记在Linux上读取.经过查看有个日本牛人 dayflower 已经把这个功能给实现了.[网址](http://d.hatena.ne.jp/dayflower/20080903/1220407032)
大概方法就是把GPEN里闪存的东西读取,再经过脚本解析成对应的SVG软件
其实自己根本没写一行代码,只是把别人的东西复制了.对于那个人的钻研精神十分佩服, 看来自己的努力还不够啊.
有个论坛里已经有人把perl脚本改了,可以把笔里的笔记拆分输出. [网址](http://f.pil.tw/thread-89931-1-1.html)
其实不需要安装libusb, 如果和我一样懒的话可以把那个 dayflower 7 8章的代码复制下来编译就行了.

### 网址 ###
1. [dayflower的Blog](http://f.pil.tw/thread-89931-1-1.html)
2. [论坛地址](http://d.hatena.ne.jp/dayflower/20080903/1220407032)
