---
category: cocos2d-js
title: 使用cocos2d-js模拟水流波浪的模拟
Layout: post
author: samael
---

其实之前就有想过游戏中那些波浪效果是怎么实现的, 刚好最近有些功夫了可以去做一个演示的小demo出来了解一下原理.

# Demo效果

这里只是实现了基本的表现, 对于增加纹理并是让相邻的长条看起来平滑, 还没有处理, 具体实现可以见参考网址

<img style="margin-left:0" src="/img/cocos2d-js_flow_test.gif"/>

# 原理

## 基本思想

最基本的原理其实是就是积分把水池, 分成N个长条, 每个长条都当成一个弹簧, 根据胡克定律弹簧形变越大, 弹力也就越大.

## 步骤

1. 水面某一点受到力时给于对应的长条一个向下的初始速度

2. 根据预先设置的Spread(传播速度)对长条的速度进行叠加

3. 集中渲染, 刷新每个长条新的高度

# 代码

<https://github.com/samael65535/toy_code/blob/master/EffectLab/fluid.js>

# 参考网址

<http://gamedevelopment.tutsplus.com/tutorials/make-a-splash-with-2d-water-effects--gamedev-236>
