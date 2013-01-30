---
layout: post
title: "emacs_kill的相关操作"
description: ""
category: emacs
tags: [技巧]
---
{% include JB/setup %}
在emacs经常用的有关kill的操作有一些,现在总结一下.
### 基本操作 ###
C-k   kill-line  从当前插入点到行末的数据放入kill-ring
M-w   kill-ring-save   把选定的文本块放入kill-ring 类似于复制
C-w   kill-region  把选定的文本块放入kill-ring, 并删除选定的块
C-d   delete-char 删除一个单字
M-d   kill-word  删除一个单词
M-<del> backward-kill-word 向前删除一个单词
### 进阶操作 ###
#### kill的追加 ####
在编程的时候有时有些特殊的需要把上一次的kill与这次的结合起来就需要用到
C-M-w append-next-kill 如果下个命令与kill相关,则把上个kill与之append
比如:
abcde(光标)fghijkl
执行C-k后,再把光标移动到行头:
(光标)abcde
执行C-k C-M-k   C-y:
fghijklabcde
这样就把abcde加再上次kill的尾部了
注意:
1. C-M-w下个命令一定是有关kill的,在emacs中向前向后移动也被归为命令
2. M-w kill-ring-save 不支持
#### kill的矩阵操作 ####
如果我们要删除一个文本区的矩形区域则可以用 C-x r k 比如
1111111222222
1111111333333
1111111444444
1111111555555
首先C-<SPC> 选取区域 之后使用C-x r k 就可删除从起点到终点对角线的文本区
222222
333333
444444
555555
#### 使用C-u返回前n次的kill ####
如(C-u 4 C-y)
### 推荐插件 ###
可以浏览kill-ring内容的
brower-kill-ring+.el

### 参考 ###
[GNU Emacs Manual   Kill Ring](http://www.gnu.org/software/emacs/manual/html_node/emacs/Kill-Ring.html#Kill-Ring)    其实这个Emacs自带的有
