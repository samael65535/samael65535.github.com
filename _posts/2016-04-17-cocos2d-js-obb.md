---
tags: [cocos2d-js]
title: cocos2d-js中obb碰撞检测的实现
layout: post
author: samael
---
在cocos中并没有提供OBB碰撞检测的接口, 如果想实现碰撞,简单粗暴的办法

1. 使用物理引擎, 但是为了一个小需求把整个物理模块引入, 显然有些太重了.
2. cocos的3d模块中有OBB相关的接口, 不过暂时没有深入调查.

为了补习数学, 就手动实现了一个:

![Demo](/img/OBB_demo.gif)

## 核心思想

> This method depends on the observation that
for two boxes to be disjoint (i.e. *not* intersecting), there must be some axis
along which their projections are disjoint.

简单来就是于A与B两个oob来说, 把B投影到A的两个旋转轴上. 如果对于B的四个边在A旋转轴上的投影线段**都与**A四个顶点向量在旋转轴上的投影的重叠, 那么就可以说这两个OOB是有重叠

[相关代码](https://github.com/samael65535/GameLab/blob/master/src/OBBTest.js)

## 优化
因为OBB的碰撞检测是实时的, 所以减少运算量了是一个很重要的问题.这里在代码中用了两个方法

1. 对B的四个投影线段进行合并
2. 不求标量, 因为如果求标量就会使用平方根计算

在下面的网站里面有个关于检测数的统计:

> In general, the first test is extremely likely to fail (and return "no overlap") when there is no overlap. If it passes,the second test is even more likely to fail if there is no overlap, and so on. Only when the boxes are in extremely close proximity is there even a 50% chance of executing more than 2 tests.

## 相关网站
[方向包围盒(OBB)碰撞检测](http://www.cnblogs.com/iamzhanglei/archive/2012/06/07/2539751.html)

[2D OBB Intersection](http://www.flipcode.com/archives/2D_OBB_Intersection.shtml)
