--- 
tag: [cocos2d-js]
title: cocos2d-js热更新优化
layout: post
author: samael
--- 

最近自己做的项目需要使用热更新来下载每个章节的资源包, 但是cocos2d-js里的AssetsManager并不能满足我的需要, 所以需要进行修改

# 需求

cocos2d-js里自带的AssetManager, 每次都是把所有的更新资源一起下下来, 但是对于一些分章节的游戏来说, 玩家想玩第几章, 第一次打开的时候去进行下载, 而不是把所有资源一起进行下载.

# 实现思路

对于cocos2d-js里的AssetManager, 都会去从资源服务器上请求project.manifest的文件去进行资源的版本号比对。 如果要进行多章节的下载. 其实是要对应多个manifest文件

文件结构如下:

![files](/img/cocos2d-js_assetmanager.png)

这样就可以去处理分章节下载的情况了, 但有需要注意:

* 在project.manifest去下载的story1_res_origin.mainfest 文件里的远程比对文件版本应为story1_res.mainifest

* story1_res_origin.mainfest里的版本应为0.0为资源的初始版本

* 这里用的一个trick, 就是在cocos2d-js在热更新的时候会首先比对版本号, 如果相同的话就不去更新了, 这样点开章节就会和服务器上对应的manifest版本号进行比对, 从而进行更新.

* 通过**修改代码**可以让比对的manifest的文件名不一样, 但是更新完的文件名还是按照本地的文件名.如: 本地的story1_res_origin对比远程的story1_res_, 更新完之后本地的文件名还是story1_res_origin


热更流程如下:

1. 首次登录游戏, 客户端向服务去对比project.manifest, 并下载公共资源与story1_res_origin.manifest
2. 当玩家点击章节一的时候, 客户端用story1_res_origin.manifest去对比远程的story1_res.manifest并对比下载最新的资源

# 参考

[cocos2d-js热更新](http://karelgt.com/Cocos2dJS%20%E7%83%AD%E6%9B%B4%E6%96%B0/)
