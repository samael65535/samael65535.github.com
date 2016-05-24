---
layout: post
title: "基于node的绅士小爬虫"
categories: node
---
最近在看node.js, 心想做个爬小本子的爬虫吧. 现在把大概思路去记录一下

# 使用模块

* async   用于管理异步调用

* cheerio  用于提取网页的节点

* minimist  用于命令行操作参数

* request   用于对网页发送请求


# 实现思路

其实思路很简单, 首先分析网页的URL的结构, 使用request去抓取相应的URL进行二级访问, 并最终抓取真正的下载地址.

# 遇到问题

* 本子的文件名其实应该是一段hash, 并不是网页的标题名, 所以需要重命名

* 之前没有理解异步模型, 在同一时刻发送大量的request, 会导致callback时写同一文件发生错误, 所以用了async来保证时同一时刻只有一个去写文件(不过这样也不好, 没有使用并发访问, 这个回头研究一下)


[相关代码](https://github.com/samael65535/toy_code/tree/master/comic)
