---
layout: post
title: "Colyseus源码阅读2"
description: ""
date: 2019-09-11
tags: [Colyseus,源码阅读,Node,TypeScript,服务器开发,游戏开发]
comments: true
share: true
---
这次阅读Server.ts , 这个类比较简单
Server的职责是保证服务器与客户端提供网络连接
所以在程序启动前会先对服务器的连接协议, 使用的web接口进行一系列的配置

![server](http://pic-blog.test.upcdn.net/2019/09/17/server.svg)

## 配置定义 ServerOptions
构造Sever时的配置
``` ts
export type ServerOptions = IServerOptions & {
  pingTimeout?: number, // 超时时间
  pingCountMax?: number, // 最大超时数
  verifyClient?: WebSocket.VerifyClientCallbackAsync // 客户端连接回调, 用于验证客户端
  presence?: any, // 用于弹性扩展
  driver?: any, // 数据存储驱动, 匹配客户端数据的存储
  engine?: any, // 连接方式(TCP/WebSocket, 具体传输协议可以在transport文件夹查看)
  ws?: any,
  express?: any,  // web接口服务
  gracefullyShutdown?: boolean, // 平滑关闭开关
};
```
## 构造流程
当新建一个Sever类时, 框架会进行以下几个步骤
1. 新建 presence
2. 新建 matchMaker
3. 注册相关路径
4. 监听端口
5. 注册关闭方法

### handleMatchMakeRequest
Server里的一个核心方法, 用于处理客户端连接时的一些匹配流程, 这里会涉及到另一个核心类MatchMaker, 用于给客户端分配房间, 与处理数据响应, 具体的源码会在下一文章里解读

``` ts
// MatchMaker 暴露的方法有 ['joinOrCreate', 'create', 'join', 'joinById'];
protected async handleMatchMakeRequest(req: IncomingMessage, res: ServerResponse) {
		//  处理跨域
    const headers = {
      'Access-Control-Allow-Headers': 'Origin, X-Requested-With, Content-Type, Accept',
      'Access-Control-Allow-Methods': 'OPTIONS, POST, GET',
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Max-Age': 2592000,
      // ...
    };

    if (req.method === 'OPTIONS') {
      res.writeHead(204, headers);
      res.end();

    } else if (req.method === 'POST') {
      // 通过路由匹配可用房间名
      const matchedParams = req.url.match(this.matchMaker.allowedRoomNameChars);
      // 房间方法名
      const method = matchedParams[matchedParams.length - 2];
      // 房间名
      const name = matchedParams[matchedParams.length - 1];

      const data = [];
		// 注册事件监听
      req.on('data', (chunk) => data.push(chunk));
      req.on('end', async () => {
        headers['Content-Type'] = 'application/json';
        res.writeHead(200, headers);

        const body = JSON.parse(Buffer.concat(data).toString());
        try {
          if (this.matchMaker.exposedMethods.indexOf(method) === -1) {
            throw new MatchMakeError(`invalid method "${method}"`, Protocol.ERR_MATCHMAKE_UNHANDLED);
          }
			  // 得到对应的方法名的结果并发送
          const response = await this.matchMaker[method](name, body);
          res.write(JSON.stringify(response));

        } catch (e) {
          res.write(JSON.stringify({
            code: e.code || Protocol.ERR_MATCHMAKE_UNHANDLED,
            error: e.message,
          }));
        }

        res.end();
      });

    } else if (req.method === 'GET') {
      // 查询未上锁房间
      const matchedParams = req.url.match(this.matchMaker.allowedRoomNameChars);
      let roomName = matchedParams[matchedParams.length - 1];

      // TODO: improve me, "matchmake" room names aren't allowed this way.
      if (roomName === 'matchmake') { roomName = ''; }
      headers['Content-Type'] = 'application/json';
      res.writeHead(200, headers);
      res.write(JSON.stringify(
        await this.matchMaker.query(roomName, { locked: false }),
      ));
      res.end();
    }

  }
```
