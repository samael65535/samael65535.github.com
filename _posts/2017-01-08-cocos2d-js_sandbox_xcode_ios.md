---
layout: post
title: "cocos2d-js从真机(iOS)中调去存档"
description: ""
date: 2017-01-08
tags: [cocos2d-js,xcode,iOS]
comments: true
share: true
---
在测试游戏时出现bug.我们可能需要让测试把游戏存档给开发，从而可以还原出当时出bug的玩家数据，找到bug。

一般cocos中，玩家存档可以使用userdefault进行存储，在web环境是，可以及浏览器查看localstorage.

那么，在ios环境下这些东西放在了哪里?

1. 在xcode打开device界面选上对应的设备
![device](/img/xcode_device.png)
2. 导出沙盒数据
![sandbox](/img/sandbox_jsb_file.png)
3. 使用sqlite查看文件内容
![content](/img/jsb_savefile_content.png)


