---
category: Javascript
title: 关于JS中switch和if进行多路判断的一些讨论
layout: post
author: samael
---

昨天给一个学生改js作业是发现一个很有意思的细节, 就是关于多路判断是用switch还是if, 这里把相关的调查总结一下.

MDN中文版里:

<https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/switch>

把这个方法定义成了一个 "hacky"

但是在MDN英文版里:

<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/switch>

关于这个的hacky的描述被删除了

估计这应该是一个争议的用法,不支持不反对, 所以就没写了

有趣的是日本人关于这个用法也做了一个调查:

<http://qiita.com/t_uda/items/1969e09a970d71e4cfd6>

里面有些观点也挺有意思的,拿翻译工具翻译了一下, 基本的两个观点就是
是, 表意优先
还是, 便利优先

关于这个的英文讨论也有, 不过有些短

<http://codereview.stackexchange.com/questions/37424/switch-true-as-alternative-to-else-if>

还有一个关于switch "有害"的观点:

<http://ericleads.com/2012/12/switch-case-considered-harmful/>

相关测试:

<http://jsperf.com/if-else-chain-vs-switch-true>
