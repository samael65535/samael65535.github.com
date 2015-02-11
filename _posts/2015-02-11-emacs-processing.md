---
layout: post
title: "在Emacs下配置processing-mode的开发环境"
description: ""
category: emacs
tags: [心得]
---
{% include JB/setup %}

最近买了[代码本色：用编程模拟自然系统], 所以对Processing颇感兴趣, 本这能在Emacs下面用就搞到Emacs下面的原则, 就配置一下processing-mode吧.

# 配置 #

关于安装processing-mode有两种办法:

* 在github上找到processing2-emacs这个repo手动安装

* 使用Emacs的插件管理器进行安装

注意:

* 不管是用哪种方法安的插件都要在自己的el中配置Processing的路径环境

* Processing.app不会默认去安装processing-java, 所以需要手动去安装一下

<img style="margin-left:0" src="/img/processing-mode.jpg"/>

* 支持简单的 Auto-Complete, 但是需要去配置

# 使用 #

其实使用方法很简单, 以下的这些操作的快捷键:

```
C-c C-p r    Run a sketch.
C-c C-p b    Compile a sketch into .class files.
C-c C-p p    Run a sketch full screen.
C-c C-p e    Export sketch.
C-c C-p d    Find in reference.
C-c C-p f    Find or create sketch.
C-c C-p s    Search in Processing forum.
```

运行效果如下:

<img style="margin-left:0" src="/img/processing-run-in-emacs.jpg"/>

# 参考资料 #

<https://github.com/ptrv/processing2-emacs>

[代码本色：用编程模拟自然系统]:<http://book.douban.com/subject/26264736/>
