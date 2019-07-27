---
layout: post
title: " LightBulb回顾"
description: ""
date: 2019-07-15
tags: [CocosCreator,游戏开发]
comments: true
share: true
---
# LightBulb回顾
最近帮朋友做在做一个超休闲的物理游戏, 在Android与微信小游戏上发布, 制作过中的一些小问题总结一下, 使用引擎是cocos creator 
## 共有问题
* 谨慎使用物理世界中的rayCast, 如果没有物理碰撞效果的, 可以尝试与cocos自带的碰撞组件进行组合使用
* cc.Graphics效率并不高, 
* 对于不同的版本UI设置可能些不一样, 可以通过组件继承的方式进行
 
``` js
// GameUIWX
cc.Class({
	extends: require('GameUI'), // <= 继承自GameUI
	properties: {},
	onGameReady() {
		this._super() // <= 父类
	},
})

```

## 微信小游戏
* 微信小游戏子域中背景不透明
子域工程中的摄像机改为透明
* 包体超出限制
使用腾讯云的COS进行远程资源的下载, 注意区别版本号, 并用md5 cached

## Android
* 如果release下有些JSB接口调用不了要修改proguard-rules.pro 把相关的代码混乱进行忽略
 
``` shell
-keep public class main.** { *; }
-keep public class com.janusgames.lightbulb.** { *; }
```


### TIPS
绘制瞄准线的代码
![IMG_5081](http://pic-blog.test.upcdn.net/2019/07/15/IMG_5081.jpg)

``` js
rayCast(p1, p2) {
    var manager = cc.director.getPhysicsManager();
    var result = manager.rayCast(p1, p2, cc.RayCastType.Closest)
    //[0];
    if (result.length > 0) {
        result = result[0]
    } else {
        result = null
    }
    if (result && result.collider.node.group == 'line') {
        p2 = result.point;
        this.ctx.circle(p2.x, p2.y, 5);
    }

    //获得从start到end的向量
    var line = p2.sub(p1)
    //获得这个向量的长度
    var lineLength = line.mag()
    //根据每条线段的长度获得一个增量向量
    let length = 45
    var increment = line.normalize().mul(length)
    //确定现在是画线还是留空的bool
    var drawingLine = true
    // 临时变量
    var pos = p1.clone()

    //只要线段长度还大于每条线段的长度
    for (; lineLength > length; lineLength -= length) {
        //画线
        if (drawingLine) {
            this.ctx.circle(pos.x, pos.y, 5)
            pos.addSelf(increment)
            this.ctx.fill()
        }
        //留空
        else {
            pos.addSelf(increment)
        }
        //取反
        drawingLine = !drawingLine
    }

    if (!result) return;
    if (result.collider.node.group != 'line') return
    // 反射
    let l = p2.sub(p1)
    let n = result.normal.normalize()

    var r = l.sub(n.mul(n.dot(l)).mul(2)).mul(500).add(p2)
    p1 = p2;
    // 返回停止点
    // 多次组合可以实现反射的效果
    return {
        p1: p1,
        p2: r,
    }
}
```


