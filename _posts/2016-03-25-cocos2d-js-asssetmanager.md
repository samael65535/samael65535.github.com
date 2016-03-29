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


# 参考
