---
layout: post
title: "cocos2d-js摄像机跟随的代码片段"
description: ""
date: 2017-01-10
tags: [cocos2d-js]
comments: true
share: true
---
最近项目中要实现一个Boss的进场动画.

在cocos2d-js中并没有实现操作摄像机的binding, 因为只是实现一个简单的镜头跟随, 期间没什么交互, 并没有什么特效, 就简单的把场景地图的进行相对的位置操作

``` javascript
bossEnter: function() {
    // 场景大小变成正常大小
    this.setScale(1); 
    // 把boss坐标转为世界坐标
    this._lastBossPosition = boss.getPosition();
    var pos = boss.parent.convertToWorldSpace(this._lastBossPosition); 
    // 移动到boss出现的位置
    pos = cc.pSub(cc.p(cc.winSize.width / 2, cc.winSize.height / 2), pos);
    this.runAction(cc.sequence(
                    cc.moveTo(0.3, pos)
                ));
 }
update: function(dt) {
    // 通过计算上一个frame与这个frame的boss位置差, 对场景进行相对移动
    var dp = cc.pSub(this._lastBossPosition, boss.getPosition());
    this.setPosition(cc.pAdd(dp, this.getPosition()));
    this._lastBossPosition = boss.getPosition()
}
```

