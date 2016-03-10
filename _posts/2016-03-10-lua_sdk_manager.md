---
layout: post
title: "使用Lua管理不同的渠道"
categories: lua, cocos
---

最近做的项目需要去接不同渠道. 由于各种因素, 导致逻辑代码里分布了各种 if .. else 去进行不同渠道的需求处理, 时间一长会造成原有的代码逻辑会更加混乱. 为了防止之前更新混乱, 所以把不用渠道的逻辑进行了相对统一的处理

# 分析

* async   用于管理异步调用

* cheerio  用于提取网页的节点

* minimist  用于命令行操作参数

* request   用于对网页发送请求

# 实现思路



# 可能有的问题



[相关代码](https://github.com/samael65535/toy_code/tree/master/comic)
