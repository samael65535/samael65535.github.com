---
tags: [Android,Gradle]
title: Gradle在cocos2d-x(2.x)游戏中集成
layout: post
author: samael
---
## 需求

上个游戏项目结束, 开始接手一些接SDK的杂活. 发现了一些问题:

* 接入或者替换新的SDK, 程序都把相同的工程白包复制一份再进行相应的接入, 再对着复制出来的项目进行相应代码的修改. 或者新开一个分支进行相应SDK的集成, 这样就会导致游戏代码管理十分混乱

* 与之前接入SDK的程序沟通发现, SDK接入的需求比我想象的要复杂一些 (比如, 支付在X渠道, 对于20元以下的支付使用短代支付, 对于20元以上的使用支付宝支付)

## 整理

经过整理现在先做出一个简单的实现方案

* 把之前项目的工程从Eclipse迁入到AndroidStudio中

* 集成对应的SDK并把对应的build.gradle迁入对应的SDK中

整理之后基本的SDK结构如下:

![struct](/img/Gradle项目结构.png)

基本APP相当于白包, 如果使用App中的buildi.gradle, build出来的App包不会加入任何SDK的APK

Libraries里是对不同渠道需求用的打包配置(最简单的比如icon需求), 包含各自的asset lib res等等.

游戏源码和共用的资源读取的是APP的, 特殊的在资源对代码对应的文件夹中, 他们之间在也可以相互包含.如:接入cmcc的同时还可以再集成广告的SDK.

Libraries中的文件如果APP与的文件同名, 会优先使用Libraries中的文件, 这样可以保证使每个SDK的特殊需求得以实现.

## 后续工作

1. 集成脚本批量出包, 把APK按规则重命名之后, 放入同一文件夹中.

2. 统一外围代码接口

## 相关参考
[Gradle集成修改AndroidManifest.xml打多个包实践](https://testerhome.com/topics/1708)
