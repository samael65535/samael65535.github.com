---
layout: post
title: "CocosCreator组件化"
description: ""
date: 2019-12-24
tags: [CocosCreator,JavaScript]
comments: true
share: true
---
# CocosCreator组件化

## 模块加载流程
## cc.Class
一个基础的API, 用于声明游戏引擎中的类, 
## cc.Component
CCC的基础类提供了关于生命周期与编辑器的一些方法
### 基本形式
``` javascript
cc.Class({
    extends: cc.Component,
    properties: {
    },
    onLoad: function () {
    },
    update: function (dt) {
    },
});
```
### require
CommonJS的加载流程
![](http://pic-blog.test.upcdn.net/2019/12/24/15679959144097.jpg)

CCC中对于javascript模块的导入参考了CommonJS的标准,但是也有一定的区别
1. 当 module.exports 没有任何定义时，Creator 会自动优先将 exports 设置为脚本中定义的 Component。如果脚本没定义 Component 但是定义了别的类型的 CCClass，则自动把 exports 设为定义的 CCClass。
2. require中的字符串是**文件名**, 如果是**不同路径的相同文件名**, 会导致歧义
3. 游戏开始时会**自动require所有脚本(目录顺序)**, 以后无论require几次返回的都是一份实例, 在CommonJS中require不会把所有文件一次都加入到缓存里

## 参考
[创建和使用组件脚本](https://docs.cocos.com/creator/manual/zh/scripting/use-component.html)