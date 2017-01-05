---
tag: [cocos2d-js,android]
title: 最近cocos2d-js编译的一些坑
layout: post
author: samael
---


Android 5.0下做成APK之后在android会闪退

错误log是找不到cocos2d-js.so

解决办法: 在jni/Application.mk下修改APP_ABI的编译参数为armeabi-v7a

-----

使用NDK10e 编译cocos2d-js(3.10)时游戏可以进入, 但是点击MenuItem会发生闪退

解决办法: 换NDK, 我换成了r9d可用
