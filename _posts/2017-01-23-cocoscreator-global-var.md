---
layout: post
title: "CocosCreator全局变量声明"
description: ""
date: 2017-01-23
tags: [CocosCreator]
comments: true
share: true
---

由于CocosCreator是基于组件式的开发, 脚本都以组件的形式挂在需要的对象上, 刚接触的开发者可能对这个有些难以理解. 最简单的需求就是


**我要把全局变量放到哪里去声明**

## 使用require
使用类似node.js的require方法, 把全局变量当做一个模块, 当使用时就去require, 这样动态的加载,有些类似单例的方式

> 传入 require 的字符串就是模块的文件名，这个名字不包含路径也不包含后缀，而且大小写敏感。
> require 可以在脚本的任何地方任意时刻进行调用。
> 游戏开始时会自动 require 所有脚本，这时每个模块内部定义的代码就会被执行一次，所以之后无论又被 require 几次，返回的始终是同一份实例。


``` javascript
// config.js

var cfg = {
    moveSpeed: 10,
    version: "0.15",
    showTutorial: true,

    load: function () {
        // ...
    }
};
cfg.load();

module.exports = cfg;

```

## 使用插件

把脚本文件, 设置为插件模式, 让插件脚本文件先于其他脚本文件加载

> 脚本加载顺序如下：
> Cocos2d 引擎
> 插件脚本（有多个的话按项目中的路径字母顺序依次加载）
> 普通脚本（打包后只有一个文件，内部按 require 的依赖顺序依次初始化）


## 参考网址
<http://www.cocos.com/docs/creator/scripting/plugin-scripts.html>
<http://www.cocos.com/docs/creator/scripting/modular-script.html>


