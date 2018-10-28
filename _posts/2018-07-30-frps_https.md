---
layout: post
title: "frps_https"
description: ""
date: 2018-07-30
tags: []
comments: true
share: true
---
# frps中https使用
## 申请免费证书
这里可以在网上找一个免费的https证书, 生成之后下载到本地
## 设置fprs端口
``` 
[common]                                                                                                                   
bind_port = 7000                                                                                                           
vhost_http_port =8080                                                                                                      
vhost_https_port =9090      # https 端口号, 可以和http设置同一个                                                                                    
# 以下为监控面板的设置
dashboard_port = 7500                                                           
# dashboard's username and password are both optional，if not set, default is admin.                                       
dashboard_user = username           
dashboard_pwd = pwd                                                                                                                   
```

## 设置https服务
这里用了socket.io的教程示例改的https协议
``` js
const app = require('express')();
// const http = require('http').Server(app);
const fs = require("fs");

var options = {
	key: fs.readFileSync('keys'), // 设置证书与私钥文件路径
	cert: fs.readFileSync('cert')
};

var server = require('https').Server(options,app);
const port = 9090
const io =require('socket.io')(server)
app.get('/', function(req, res){
	res.sendFile(__dirname + '/index.html');
});

io.on('connection', (socket) => {
	socket.on('disconnect', () => {
		console.log('user disconnected');
	});
	socket.on('chat message', function(msg){
		console.log('msg')
		io.emit('chat message', msg);
	});
});

server.listen(port, function () {
	console.log('Https server listening on port ' + port);
});

```
## 测试
![-w944](http://pic-blog.test.upcdn.net/2018/10/24/15329190381318.jpg)


## 注意:
网上很多使用frps的教程其实对https的使用没有说清楚. frps其实不用走nginx的服务, 如果设置nginx的端口号反而会让穿透不成功, 所以https这些证书的验证需要做的自己的服务里