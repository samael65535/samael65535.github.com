---
layout: post
title: "socket.io部署时的一些坑"
description: ""
date: 2018-09-11
tags: [CocosCreator,socket,nginx,node]
comments: true
share: true
---
## 微信小游戏打包
使用cocoscreator打包微信小游戏, socket.io是以插件形式存在在程序中的, 变量是全局作用域, 所以可以在微信小游戏game.js里加入
``` js
window.io = require('src/path/socket.io.js')
```
如果不想每次都导入可以创建build-templage
[参考网址](http://docs.cocos.com/creator/manual/zh/publish/custom-project-build-template.html)

## 命名空间与子路径的问题
如果在socket.io中同时使用子路径与namespace
如果我想连接:
*ws://address/path/namespace*

以下的形式是错误的

* 错误的形式

``` js 
io.connect('address/path/namespace'} 
```

* 正确的形式
 
``` js
let config = {
		'path':'/path',
		'query': 'from=abc'
}

let socket = io.connect('address/namespace',config);
```


## express与socket.io
如果socket.io中有用到http的请求, 如果直接请求相同的地址, express不会处理到对应的请求

``` json
{
	"code": 0,
	"message": "Transport unknown"
}
```

要用nginx分成两个

``` json
location /path/server/ {                                                                                            
		proxy_pass http://127.0.0.1:9090/socket.io/;                                                                        
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header Host $host;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;                                                       
		proxy_redirect off;                                                                                                
		proxy_http_version 1.1;                                                                                            
		proxy_set_header Upgrade $http_upgrade;                                                                            
		proxy_set_header Connection "upgrade";                                                                             
}                                                                                                                          
location /path/server/api/ {                                                                                                                                                                                                                    
		proxy_pass http://127.0.0.1:9090/;                                                                                 
}    
```
