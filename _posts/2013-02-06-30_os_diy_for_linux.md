---
layout: post
title: "30_os_diy_for_linux"
tag: [Linux]
---
#### 起因 ####
最近在看30天自制操作系统,准备自己写一个小操作系统当做毕业设计的项目来做, 因为自己日常的环境是Linux, 但是书中用的操作系统是windows,

所以为了方便在Linux下开发,需要对一些东西进行修改
#### 工具 ####
随书光盘中的z_tools是在windows下环境下用的一些工具,已经有人制作出了对应的Linux工具了

[下载地址 z_tools ](http://hrb.osask.jp/z_tools.tar.bz2)

#### Makefile ####

在项目中的Makfile 也需要进行相应的修改

> TOOLPATH = (工具的目录)

> INCPATH  = (工具的目录)

> MAKE     = make -r

> NASK     = $(TOOLPATH)nask

> CC1      = $(TOOLPATH)cc1 -I $(INCPATH) -Os -Wall -quiet

> GAS2NASK = $(TOOLPATH)gas2nask -a

> OBJ2BIM  = $(TOOLPATH)obj2bim

> BIN2OBJ  = $(TOOLPATH)bin2obj

> BIM2HRB  = $(TOOLPATH)bim2hrb

> RULEFILE = $(TOOLPATH)haribote/haribote.rul

> EDIMG    = $(TOOLPATH)edimg

> IMGTOL   = $(TOOLPATH)imgtol.com

> MAKEFONT = $(TOOLPATH)makefont

> GOLIB	 = $(TOOLPATH)golib00

> COPY     = cp

> DEL      = rm


> haribote.sys : asmhead.bin bootpack.hrb Makefile

>	cat asmhead.bin bootpack.hrb > haribote.sys


>	$(EDIMG)   imgin:$(TOOLPATH)fdimg0at.tek \
