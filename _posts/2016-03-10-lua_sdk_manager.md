---
layout: post
title: "使用Lua管理不同的渠道"
categories: lua, cocos
---

最近做的项目需要去接不同渠道. 由于各种因素, 导致逻辑代码里分布了各种 if .. else 去进行不同渠道的需求处理, 时间一长会造成原有的代码逻辑会更加混乱. 为了防止之前更新混乱, 所以把不用渠道的逻辑进行了相对统一的处理

# 分析

和接SDK的妹子了聊了聊, 基本的SDK都会有以下的功能

* 获取openid

* 支付

* 向渠道发送玩家信息

* 调用Back的实体键


# 实现思路



# 可能有的问题

[相关代码](https://github.com/samael65535/toy_code/tree/master/lua/SDKManager)
