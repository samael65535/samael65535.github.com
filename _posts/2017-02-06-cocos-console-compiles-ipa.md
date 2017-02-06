---
layout: post
title: "cocos2d-js打包重签名"
description: ""
date: 2017-02-06
tags: [cocos2d-js,xcode]
comments: true
share: true
---
## 问题
游戏要出包, 由于使用cocos2d-js开发, 所以在发布游戏的时候js文件要编译成jsc的格式. 如果使用xcode进行Archive那么需要以下步骤

1. 对js文件进行编译
2. 修改项目结构中的Resource/src(游戏代码)与script(引擎代码), 使之指向jsc的目录
3. 打包

这种方式其实对开发很不友好, 因为在发布之后我们又需要改一遍项目结构进行开发, 而且不能进行自动化的集成.

`cocos compile`其实可以对js文件进行编译并输出ipa, 之后输出的ipa包使用`iOS App Signer` 重签名. 虽然签名成功了, 但是在安装到真机上时会报出`CPU类型不符`这样的错误

## 原因
查看引擎目录下的

`/tools/cocos2d-console/plugins/plugin_compile/project_compile.py`

``` python
   def build_ios(self):
        if not self._platforms.is_ios_active():
            return

        if not cocos.os_is_mac():
            raise cocos.CCPluginError(MultiLanguage.get_string('COMPILE_ERROR_BUILD_ON_MAC'),
                                      cocos.CCPluginError.ERROR_WRONG_ARGS)
        # 这里
        if self._sign_id is not None:     
            cocos.Logging.info(MultiLanguage.get_string('COMPILE_INFO_IOS_SIGN_FMT', self._sign_id))
            self.use_sdk = 'iphoneos'
        else:
            self.use_sdk = 'iphonesimulator'

    ... 以下省略 ...
     try:
            cocos.Logging.info(MultiLanguage.get_string('COMPILE_INFO_BUILDING'))

            command = ' '.join([
                "xcodebuild",
                "-project",
                "\"%s\"" % projectPath,
                "-configuration",
                "%s" % 'Debug' if self._mode == 'debug' else 'Release',
                "-target",
                "\"%s\"" % targetName,
                # 这里
                "%s" % "-arch i386" if self.use_sdk == 'iphonesimulator' else '', 
                "-sdk",
                "%s" % self.use_sdk,
                "CONFIGURATION_BUILD_DIR=\"%s\"" % (output_dir),
                # 这里
                "%s" % "VALID_ARCHS=\"i386\"" if self.use_sdk == 'iphonesimulator' else ''
                ])
    ... 以下省略 ...
```

这里可以发现, 如果在`cocos compile`中不使用SIGN_ID时, 生成的其实**是让模拟器使用**的i386的包

## 解决
真正的命令是: 
cocos compile -p ios -t TARGET -m release –sign-identity SIGN_ID
之后在会在游戏项目publish下生成ipa文件


