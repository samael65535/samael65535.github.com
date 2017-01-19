---
layout: post
title: "CocosCreator屏蔽触摸层"
description: ""
date: 2017-01-19
tags: [CocosCreator]
comments: true
share: true
---

有时在游戏在数据传输中不想让玩家进行操作, 这时需要做一个Loading页对全局的触摸进行屏蔽


## 界面实现
其实这一步并不是必要, 但如果游戏中有多个Scene的话为了复用,建议还是加上吧。


## 添加触摸屏蔽
实现一个LoadingLayer的脚本文件并把应该脚本挂在刚刚制作的Prefab上
![jpg](http://7xqmjb.com1.z0.glb.clouddn.com/20170118148471168476542.jpg?imageView2/0/format/jpg)

 
``` js
cc.Class({
    extends: cc.Component,
    properties: {
    },
    // use this for initialization
    onLoad: function () {
        this.node.on(cc.Node.EventType.TOUCH_START, function (event) {
            event.stopPropagation()
        }, this);
        this.node.on(cc.Node.EventType.MOUSE_MOVE, function (event) {
            event.stopPropagation()
        }, this);
    }
});
```

## 屏蔽层唤醒

对屏蔽层唤醒有两种实现
* 动态的addChild

``` javascript
showLoading: function() {
    var l = cc.instantiate(this.loadingLayer); // 获取Prefab
    l.parent = this.canvas.node;
}
```

* 在scene上事先挂一个需要的时候去唤醒

``` javascript
    showLoading: function(flag){
        this.loadingLayer.active = flag;
    },
```

