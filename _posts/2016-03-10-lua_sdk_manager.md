---
layout: post
title: "使用Lua管理不同的渠道"
categories: lua, cocos
---

最近做的项目需要去接不同渠道. 由于各种因素, 导致逻辑代码里分布了各种 if .. else 去进行不同渠道的需求处理, 时间一长会造成原有的代码逻辑会更加混乱.

为了防止之前的代码更加混乱, 所以把不用渠道的逻辑进行了相对统一的处理, 对于游戏逻辑代码, 只需要使用SDKManager:XXX就可自动调用相应的渠道代码, 如:

{% highlight lua %}
SDKManager:getOpenId()
{% endhighlight %}


# 分析

和接SDK的妹子了聊了聊, 基本的SDK都会有以下的功能

* 获取openid

* 支付

* 向渠道发送玩家信息

* 调用Back的实体键

* etc

# 实现思路

生成一个基本的SDKManager, 游戏启动时可以从Android的层面去读取出相应的渠道号, 再通过setmetatable把SDKManager转成相应的Manager

生成流程如下:

![](/img/lua-SDKManager.jpg)

## 关键代码

* 渠道号对应的Manager的代码, 如果没有Manager需要一个默认的渠道,所以就要使用setmetatable在获取相应的key的时候进行判断
{% highlight lua %}
-- 根据相应的渠道号实例相应的Manager
local ChannelDic = {
   ["DEV"] = DevManager,
   ["TEST"] = TestManager,
   ["UC"] = UCManager
}

ChannelDic = setmetatable(ChannelDic, {
__index = function(t, key)
		local f = rawget(ChannelDic, key) -- 为了防止递归读取要使用rawget
		return f or DevManager
	end})

{% endhighlight %}

* 生成相应的渠道Manager的代码, 注意: SDKManager里需要做个mock的方法, 防止在游戏逻辑调用时发生错误
{% highlight lua %}
SDKManager = {
   __channel = "DEV"
}

function SDKManager:InitByChannel(channel)
   SDKManager = setmetatable(ChannelDic[channel], {__index = self}) -- 更新元表
   self.__channel = channel
end
{% endhighlight %}

* 对于需要分不同渠道实现的游戏逻辑做了一个特殊的方法去进行处理,如果是当前的渠道调用funcSucess, 否则funcFail.
{% highlight lua %}
-- 处理对应渠道的特殊需求
function SDKManager:handleSpecialFunc(channel, funcSucess, funcFail)
   if (channel ~= self.__channel) then
	  if (type(funcFail) ~= "function") then return nil  end
	  return funcFail()
   end
   if (type(funcSucess) ~= "function") then return nil end
   return funcSucess()
end
{% endhighlight %}

为什么不用table去做也是为了代码的美观性
{% highlight lua %}
SDKManager:handleSpecialFunc("DEV", DEVFunc);
SDKManager:handleSpecialFunc("TEST", TESTFunc);
SDKManager:handleSpecialFunc("UC", UCFunc);
{% endhighlight %}

个人觉得这样看起了比较整齐一些.

# 相关资料

[LUA程序设计](http://book.douban.com/subject/3076942/)

[相关代码](https://github.com/samael65535/toy_code/tree/master/lua/SDKManager)
