---
layout: post
title: "Creating_Web_Applications_with_Go学习笔记"
description: ""
date: 2017-05-24
tags: [Go, web]
comments: true
share: true
---

最近看了一个关于Go开发Web网站的视频教程, 感觉挺不错的, 在此做一下笔记. 
    
# 相关知识
* template
* http
* 一点前端知识

# 项目结构
``` shell
.
├── controllers     负责对渲染模板并返回生成的html
│   └── util        一些功能, 如gzip
├── converters      models转换(虽然我也不知道作用有多大)
├── main            入口
├── models          数据模型, 用于存入数据库
├── public          css 图片等静态文件
│   ├── css
│   └── img
├── templates       模板文件
└── viewmodels      视图模型

```

# 路由
这个项目的使用路由工具是:

github.com/gorilla/mux

有机会阅读一下这个源码

# template
模板没有使用html/template用的是text/template, 为了复用, 把页面分成了好几块进行封装

``` shell
templates
├── _category.html
├── _footer.html
├── _head.html
├── _header.html
├── _productSummary.html
├── categories.html
├── home.html
├── login.html
├── product.html
├── products.html
├── profile.html
└── stand_locator.html
```

# 数据持久化
教程中使用的是PostgreSQL, 因为习惯问题我使用了MongoDB, 驱动使用mgo
这个项目很简单只有两个表, 
1. member存储会员信息的 
2. session存储登陆用户cookie, 保持登陆状态

# 压缩

如果客户端HTTP的请求头包含Accept-Encoding 并值为gzip说明客户端支持gzip的压缩方式, 这样就可以进行html压缩
压缩效果对比:
![未压缩](/img/14956998038597.jpg)

![压缩](/img/14956997461279.jpg)

[源码](https://github.com/samael65535/Learning/blob/master/Golang/webstore/src/controllers/util/responseWriter.go)

# Ajax
教程里使用Ajax的用途是调用GoogleMap的API, stand_locator.go里有两个函数
1. HTTP请求使用的**get**
2. Ajax使用的**apiSearch**

# session
对于已经登陆成功的用户为了给用户标识需要创建一个cookie进行存储

# 工作流程
## 启动
### controllers/main.go
1. 路径处理函数的注册
2. 静态文件读取文件的设置
### main/main.go
1. 程序的入口函数
2. templates的解析缓存
3. 端口的监听

## 请求响应
红线是请求
蓝线是响应
![](/img/14957054821094.jpg)

# Tips
## mgo为什么Find之后result是空值
https://stackoverflow.com/questions/37188760/mgo-v2-find-select-one-returns-blank

## 管理GOPATH
很有用的一个工具
http://tammersaleh.com/posts/manage-your-gopath-with-direnv/




