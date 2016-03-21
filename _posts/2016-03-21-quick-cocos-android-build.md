--- 
category: lua, android
title: 分渠道项目打包流程梳理
layout: post
author: samael
--- 

# 基本原则

* 不同渠道下对应不同的分支
* 不同渠道分支下的游戏代码(lua)不能修改
* 对应的实现在dev上去修改对应的SDKManager

# 实现思路

## 不同的渠道下面有关的文件有

* AndroidManifest.xml
* strings.xml
  可以把quick-cocos里对应的打包脚本去修改一下按照渠道名去copy对应的配置文件并改名

## 修改打包脚本

把以上两个文件按渠道名去修改对应的文件名, 并放入channel_config文件夹中如

channel_config/AndroidManifestUC.xml

# 发包操作流程

* 修改channel_config对应渠道的AndroidManifest 的版本号
* ./clean.sh
* ./build_native.sh UC
* 测试

