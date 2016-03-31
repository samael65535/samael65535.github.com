--- 
category: js, cocos
title: cocos2d-js热更新优化
layout: post
author: samael
--- 

最近自己做的项目需要使用热更新来下载每个章节的资源包, 但是cocos2d-js里的AssetsManager并不能满足我的需要, 所以需要进行修改

# 需求

cocos2d-js里自带的AssetManager, 每次都是把所有的更新资源一起下下来, 但是对于一些分章节的游戏来说, 玩家想玩第几章, 第一次打开的时候去进行下载, 而不是把所有资源一起进行下载.

# 实现思路

对于cocos2d-js里的AsssetManager, 都会默认去从资源服务器上请求project.manifest的文件去进行资源的版本号比对, 但是如果要进行分章节的下载就要进行多个章节的更新进行下面. 其实是要对应多个manifest文件

文件结构如下:

这样就可以去处理分章节下载的情况了, project:

但有需要注意.manifest对应的manifest文件的初始版本号应为0.0, 因为在cocos2d-js在热更新的时候会首先比对版本号, 如果相同的话就不去更新了, 这样点开章节就会和服务器上对应的manifest版本号进行比对, 从而进行更新.

热更流程如下:


# 参考
[cocos2d-js热更新](http://karelgt.com/Cocos2dJS%20%E7%83%AD%E6%9B%B4%E6%96%B0/)

