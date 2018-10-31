---
layout: post
title: "解决CocosCreator在微信开发工具中不能触摸的问题"
description: ""
date: 2018-10-31
tags: []
comments: true
share: true
---

在开发调试游戏时使用微信的开发者工具调试网页项目, 会发生触摸不响应的问题
![-w325](http://pic-blog.test.upcdn.net/2018/10/31/15409880240890.jpg)
## 解决办法
![F89E8607A1EE0F01C984E8EE157EDA24](http://pic-blog.test.upcdn.net/2018/10/31/F89E8607A1EE0F01C984E8EE157EDA24.jpg)
![C4C5DF6DC2767E0EDDF30B7CC8ED79EA](http://pic-blog.test.upcdn.net/2018/10/31/C4C5DF6DC2767E0EDDF30B7CC8ED79EA.jpg)
``` javascript
window.navigator.msPointerEnabled = true
```