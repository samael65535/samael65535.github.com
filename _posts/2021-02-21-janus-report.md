---
layout: post
title: "JanusReport流水账"
description: ""
date: 2021-02-21
tags: [JavaScript,React]
comments: true
share: true
---
## 背景
很久之前就想做个A股行业的每日宽度图。刚好今天春节不用回家有了一点时间，就当成前端入门实践了。

## 项目拆解
本质上这东西从技术上来说并不难，无非就是做做把数据转成另一个我看着舒服的模式展示出来。

### 数据源
现成的TuShare的接口，因为不想再搞个Python后端了，就直接用他们的Http接口。

### 存储
因为是一个纯前端的项目，项目实行过程中发现了几个问题：
1. 接口请求是有限制的，频繁的请求数据源很容易被Ban。
2. 请求大批量的数据，响应时间有些不能接受，所以需要数据本地缓存一部分。
3. 因为是纯数据，对于数据的二次加工还要进行一些查找排序之些的二次加工，有些麻烦。

### 展示
1. 表格展示
2. 比例展示
3. 多曲线
4. 方便扩展

## 技术选型
1. 数据源：TuShare
2. 存储：IndexedDB (Dexie.js封装)
3. 数据展示：AntDesginPro/BizChart

![](https://tva1.sinaimg.cn/large/008eGmZEly1gnv4hev36xj30bb0cv3ye.jpg)

## 项目预览
![](https://tva1.sinaimg.cn/large/008eGmZEly1gnv3tx5u22j30z20rjdhi.jpg)
![](https://tva1.sinaimg.cn/large/008eGmZEly1gnv41z5lrvj30zc0mn40i.jpg)
![](https://tva1.sinaimg.cn/large/008eGmZEly1gnv41pmya2j30x50u075l.jpg)

## 后续计划
这东西本身对我来说就一个辅助工具，顺便了解下前端的开发流程，边用边完善吧。
1. 仓位配置的速算
2. 盈亏比的速算
3. Electron（这个看心情吧）