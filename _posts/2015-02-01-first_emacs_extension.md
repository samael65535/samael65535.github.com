---
layout: post
title: "我的第一个Emacs扩展"
tags: [Emacs, Markdown]
---
之前写过2015年的读书计划，在写的时候发现使用Markdown插入图书链接很不方便, 所以干脆借着这次机会去学习一下如何写Emacs的扩展。
##  功能

代码不长，实现步骤也很简单，就是使用豆瓣的API进行书名的搜索并显示到一个新的Buffer里， 之后通过选择进行插入。包括了以下几个函数:

* search-db-book（调用API去查询图书）

* show-books（把查询结果显示到一个新的Buffer中）

* select-book（选择并插入条目）

## 使用方法

使用方法很简单:

1. M-x search-db-book
2. 输入书名
3. 选择结果

<img style="margin-left:0" src="/img/emacs_md_book.gif"/>

## 现有的不足

因为elisp也是刚学，一些概念也不是很清晰，所以还是有一些不足地方。

* 函数作用域是全局的，但是输出的是Markdown标记，应该在markdown-mode下进行扩展。

* 其实不光应该加图书的标记，还可以加电影之类的。

* 豆瓣的API限制了一些查询，如结果数量，查询次数

## 参考资料
[GNU Emacs Lisp 编程入门]

[Writing GNU Emacs Extensions]

[Writing GNU Emacs Extensions]:<http://book.douban.com/subject/1432819/>

[GNU Emacs Lisp 编程入门]:<http://book.douban.com/subject/1240500/>
