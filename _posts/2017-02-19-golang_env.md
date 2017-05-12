---
layout: post
title: "Golang开发环境配置"
description: ""
date: 2017-02-19
tags: [Go,VSCode,Emacs]
comments: true
share: true
---

## VSCODE

在windows下用VSCODE进行Debug需要去
配置GoLang的环境变量

``` json
{
    "go.buildOnSave": true,
    "go.lintOnSave": true,
    "go.vetOnSave": true,
    "go.buildTags": "",
    "go.buildFlags": [],
    "go.lintTool": "golint",
    "go.lintFlags": [],
    "go.vetFlags": [],
    "go.coverOnSave": false,
    "go.useCodeSnippetsOnFunctionSuggest": false,
    "go.formatOnSave": true, 
    "go.formatTool": "goreturns",
    "go.formatFlags": [],
    "go.goroot": "/usr/local/go", // 这里: 注意windows下的路径与*nix下的格式不同
    "go.gopath": "/Users/lukeh/go",// 这里
    "go.inferGopath": false,
    "go.gocodeAutoBuild": false
}
```


以当前文件夹做为调试的根目录

``` Json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Launch",
            "type": "go",
            "request": "launch",
            "mode": "debug",
            "remotePath": "",
            "port": 2345,
            "host": "127.0.0.1",
            "program": "${fileDirname}", // 这里
            "env": {},
            "args": [],
            "showLog": true
        }
    ]
}
```

## Emacs
设置Emacs的环境变量

``` lisp
(setenv "GOPATH" "YOUR_GOPATH")
```


## 参考网址
<http://tleyden.github.io/blog/2014/05/22/configure-emacs-as-a-go-editor-from-scratch/>
<https://github.com/Microsoft/vscode-go>
<http://awesome-go.com/>


