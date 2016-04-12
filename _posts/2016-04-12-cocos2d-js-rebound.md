---
category: js, cocos
title: cocos2d-js反弹实现
layout: post
author: samael
---
最近开了个新坑, 需要实现一个物体打到边界再产生反弹的效果, 实现很简单, 在些记录一下

## 实现

简单来说就是根据相应的的方向对向量取反.

![files](/img/cocos2d-js_rebound.png)

## 问题
如果想要实现就一段激光打到边界再产生反射效果, 这样光在边界反弹时是有一段的折角, 应该如何实现?
