---
tag: [cocos2d-js]
title: cocos2d-js反弹实现
layout: post
author: samael
---

最近开了个新坑, 需要实现一个物体碰到边界再产生反弹的效果, 实现很简单, 在这记录一下

## 实现

简单来说就是根据相应的的方向向量取反.

![rebound](/img/cocos2d-js_rebound.png)

## 问题
如果想要实现就一段激光打到边界再产生反射效果, 这样光线在边界反弹时是有一段的折角, 应该如何实现?
