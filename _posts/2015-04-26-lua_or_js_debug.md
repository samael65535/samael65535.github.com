---
layout: post
title: "cocos2d-lua/js中错误弹窗"
description: ""
category: 游戏开发
tags: [心得,cocos2d-x]
---
{% include JB/setup %}

# 需求
最近在使用lua制作一款手游。虽然lua支持热更新代码，但是让策划们玩的出错之话没有办法去复现当时是什么情况出现的，于是就制作了一个简单的报错弹窗功能，出错时可以把游戏当时的截图发给开发方便进行调试。

# 原理
其实js与lua的原理都差不多都当运行环境中发生错误时通过一个函数回到C++的环境中进行处理。

在jsb中是:

``` cpp
	JS_SetErrorReporter(context, callFunc);
```

lua有些不一样，当运行出错时会先调用

``` lua
	__G__TRACKBACK__(errorMsg)
```

然后去调用事先注册好的函数

再回到C++的环境进行处理(需要注册一个处理函数回调到C++进行处理)

注意：

实现弹窗要使用Native的开发语言，也就是android与ios都要实现一份，因为cocos2d-x的进程已经挂掉了。

## 实现

代码比较简单，这里只是实现一下示意图
<img style="margin-left:0" src="/img/ExceptionHandler.png"/>

# 运行效果

<img style="margin-left:0" src="/img/ExceptHandleResult.png"/>
