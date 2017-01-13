CocosCreator全局通知的实现
---
layout: post
title: "CocosCreator全局通知的实现"
description: ""
date: 2017-01-09
tags: []
comments: true
share: true
---

CocosCreator中暂没有集成全局通知的功能, 事件分发都是基于结点结构进行传递的.不过, 有些需求下实现一个全局的通知会更有好一些. 比如:玩家的数值变化. 这种变化可能会改变很多UI的表现.

刚接触了CocosCreator不久, 只是总结, 并不算是最佳实现吧

## 全局生成一个EventTarget
这样可以支持事件的冒泡传递

``` javascript
    var NotificationCenter = new cc.EventTarget()
```

## EventManager
EventManager是之前Cocos2d-x的事件发分发实现, 这里得到了保留
不过官方不建议这样使用

<http://www.cocos.com/docs/creator/scripting/player-controls.html>

>注意：目前已经不建议直接使用 cc.eventManager 来注册任何事件，cc.eventManager 的用法也不保证持续性，有可能随时被修改

以下代码未经完全验证, 只是网上的代码


``` javascript
cc.eventManager.addListener(listener, this.node)

var event = new cc.Event.EventCustom('xxxxxx', true);
event.detail = value;
this.node.dispatchEvent(event);
```

## 自己实现一套
参考的网上代码自己做了一些修改

``` javascript
 var NotificationCenter = {
    handles_: {},
    //发送事件
    emit: function (eventName, data)
    {
        var returns = []; //返回值
        data = data || {};


        var name = eventName;
        data.eventName = name; //保存一下事件名字

        for (var findEvenName in this.handles_)
        {
            if (findEvenName == eventName)
            {
                for (var i = 0; i < this.handles_[name].length; i++)
                {
                    var handler = this.handles_[name][i]
                    var returnValue = handler.callback.call(handler.target, data);
                    returns.push(returnValue);
                }
            }
        }
        return returns
    },
    //添加普通事件
    on: function (eventNames, callback, target)
    {

        if (Array.isArray(eventNames) == false) {
            eventNames = [eventNames];
        }
        for (var i = 0, len = eventNames.length; i < len; i++)
        {
            // console.log('收到事件', eventName);
            var name = eventNames[i];
            this.handles_[name] = this.handles_[name] || [];

            this.handles_[name].push({
                callback: callback,
                target: target
            });
        }
    },

    //通过事件名和target移除一个监听器
    off: function (eventNames, callback, target)
    {

        if (target == undefined)
        {
            target = callback;
            callback = undefined;
        }
        if (Array.isArray(eventNames) == false) {
            eventNames = [eventNames];
        }
        for (var idx in eventNames)
        {
            var eventName = eventNames[idx];
            for (var i = 0; i < this.handles_[eventName].length; i++)
            {
                var handler = this.handles_[eventName][i];
                if (target == handler.target &&
                    (callback.toString() == handler.callback.toString() || callback == undefined))
                {
                    this.handles_[eventName].splice(i, 1);
                }
            }
        }
    },
};
```
# 参考网址

<http://forum.cocos.com/t/creator-x-notificationcenter/41318>

<http://www.cocos.com/docs/creator/api/classes/EventTarget.html>

<http://www.cocos.com/docs/creator/scripting/player-controls.html>

<http://forum.cocos.com/t/topic/40408> 

*注意*: 最后一个网址给的代码 off的实现是有bug的. 表现为如果移除了一个事件名所有监听应该事件的callback都会被移除

``` javascript
     //通过事件名和target移除一个监听器
    off: function (eventName) {
        for (var i = 0; i < this.handles_[eventName].length; i++) {
            this.handles_[eventName][i] = null
        }
    },
```


