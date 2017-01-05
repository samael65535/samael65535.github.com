---
layout: post
title: "ChromeApp"
tags: [Javascript]
---

# Chrome应用的生命周期 #
Chrome 应用的生命周期应该与浏览器窗口的行为或网络连接无关。

1. 安装: 用户选择安装应用程序并明确接受权限。

2. 启动: 加载事件页面，产生 'launch' 事件，并在窗口中打开应用的页面。您可以创建应用需要的窗口，并指定它们的外观以及它们与事件页面和其他窗口的通信方式。

3. 终止: 用户可以在任何时候终止应用，应用可以迅速恢复到原来的状态。储存数据可以避免数据丢失。

4. 更新: 用户可以在任何时候终止应用，应用可以迅速恢复到原来的状态。储存数据可以避免数据丢失。

5. 卸载: 用户可以主动卸载应用，卸载后不会留下可执行代码或私有数据。

应用声明权限: <https://crxdoc-zh.appspot.com/apps/declare_permissions>

# 开发流程 #

## 创建事件页面 ##

要创建事件页面，在应用程序的清单文件中包含 "background" 字段，并将 background.js 包含在脚本（"scripts"）数组中。事件页面使用的所有库脚本需要首先加入 "backrgound" 字段：
``` javascript
    "background": {
        "scripts": [
            "foo.js",
                "background.js"
            ]
    }
```
事件页面必须包含 onLaunched() 函数，当应用以任何方式执行时会调用该函数：
``` javascript
    chrome.app.runtime.onLaunched.addListener(function() {
        // 告诉您的应用执行的内容与方式。
    });
```
## 创建窗口 ##
Chrome 应用的窗口不和任何 Chrome 浏览器窗口关联，它们可以具有可选的框架，包含标题栏和大小控件。

``` javascript
    chrome.app.runtime.onLaunched.addListener(function() {
        chrome.app.window.create('main.html', {
            id: 'MyWindowID',
                bounds: {
                    width: 800,
                height: 600,
                    left: 100,
                top: 100
            },
                minWidth: 800,
                minHeight: 600
            });
    });
```

应用程序首次安装或者更新时将调用 chrome.runtime.onInstalled()，每次调用该函数时将产生 onInstalled 事件。

应用运行时环境在卸载前会向事件页面发送 onSuspend() 事件
