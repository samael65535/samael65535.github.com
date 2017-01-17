---
tags: [cocos2d-js]
title: 用cocos2d-js修改Sprite的色调
layout: post
author: samael
---

在游戏中我们要实现同样的敌人样子，但是还有是一定的区别

总不能让美术一套一套的出图吧

我们需要对精灵的**色相**进行调整而不是简单的setColor,如图
![jpg](http://ojk5fzxey.bkt.clouddn.com/2017011314843083896270.jpg?imageView2/0/format/jpg)


基本原理就是让纹理中的每种颜色在色盘中平移一个值

![jpg](http://ojk5fzxey.bkt.clouddn.com/20170113148430948881314.jpg?imageView2/0/format/jpg)

## shader代码
``` c
// hue.fsh
#ifdef GL_ES
precision mediump float;
#endif

varying vec2 v_texCoord;
uniform mat3 u_hue;
uniform float u_alpha;

void main()
{
    vec4 pixColor = texture2D(CC_Texture0, v_texCoord);
    vec3 rgbColor = u_hue * pixColor.rgb;
    gl_FragColor = vec4(rgbColor, pixColor.a * u_alpha);
}


// hue.vsh
attribute vec4 a_position;
attribute vec2 a_texCoord;
attribute vec4 a_color;

#ifdef GL_ES
varying lowp vec4 v_fragmentColor;
varying mediump vec2 v_texCoord;
#else
varying vec4 v_fragmentColor;
varying vec2 v_texCoord;
#endif

void main()
{
    gl_Position = CC_PMatrix * a_position;
    v_fragmentColor = a_color;
    v_texCoord = a_texCoord;
}
```
完整的代码可以参考相关链接里

## 相关链接
<http://color-wheel-artist.com/hue.html>
<https://github.com/samael65535/GameLab/blob/master/src/HueTest.js>

