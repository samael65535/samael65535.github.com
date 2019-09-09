---
layout: post
title: "Colyseus源码阅读1"
description: ""
date: 2019-09-09
tags: [Colyseus,源码阅读,Node,TypeScript,服务器开发,游戏开发]
comments: true
share: true
---
最近觉得自己的技能有了瓶颈, 所以想通过阅读一些源码进行提高, 这次就先选定一个我比较喜欢的实时游戏框架[Colyseue](https://colyseus.io)
## 介绍
该项目是由[gamestd](http://gamestd.io/)工作室开源的一套服务器框架, 基于Node.js开发, 使用TypeScript. 并适配了多种游戏引擎
![](http://pic-blog.test.upcdn.net/2019/09/09/15679973724258.jpg)

项目更新节奏稳定. 因为项目之前遇到过有些奇怪问题,开发人员反馈比较积极, 我使用的项目已经成功上线, 至今没遇到过什么大问题

另外开发者也提供了很多别的有用的工具, 给独立游戏开发者提供了很多的帮助, 具体可以去他们的[github](https://github.com/gamestdio)

## 特点
Colyseus特点之一就是提供了一套高效状态同步的方案, 让游戏开发人员专注于游戏逻辑的开发, 刚不用考虑游戏世界同步的问题
以下是同步流程, 来自官网
* Client sends a message to the server, requesting to change its state.
(客户端发送信息到服务器, 请求改变状态)
* The input must be validated by your room handler.
(输入必须经过房间逻辑处理器的验证)
* The room state is updated.
(房间状态更新)
* All clients receive the latest version of the game state.
(所有客户端接收最新游戏状态)
* The visual representation of the game state is updated.
(客户端表现更新)

```
              room.send({ action: "left" })

                           |
      +------------+       |       +-----------------------------------+
+-----+ Client #1  +-------|       |  Room handler #1                  |
|     +------------+       |       |                                   |
|     +------------+       |       |  onMessage (client, data) {       |
|-----+ Client #2  |       --------+    if (data.action === "left") {  |
|     +------------+               |      // update the room state     |
|     +------------+               |    }                              |
|-----+ Client #3  |               |  }                                |
|     +------------+               +-----------------------------------+
|                                                    |
|        patch state broadcast (binary diff)         |
|----------------------------------------------------+
```

## 项目结构
这里只罗列src文件夹, 结构还是直观的
```
src
├── Debug.ts
├── MatchMaker.ts 
├── Protocol.ts
├── Room.ts   
├── Server.ts 
├── Utils.ts
├── discovery
│   └── index.ts
├── errors
│   ├── MatchMakeError.ts
│   └── SeatReservationError.ts
├── index.ts
├── matchmaker
│   ├── RegisteredHandler.ts
│   └── drivers
│       ├── Driver.ts
│       ├── LocalDriver
│       │   ├── Query.ts
│       │   ├── RoomData.ts
│       │   └── index.ts
│       └── MongooseDriver.ts
├── presence
│   ├── LocalPresence.ts
│   ├── Presence.ts
│   └── RedisPresence.ts
├── rooms
│   ├── LobbyRoom.ts
│   └── RelayRoom.ts
├── serializer
│   ├── FossilDeltaSerializer.ts
│   ├── SchemaSerializer.ts
│   └── Serializer.ts
└── transport
    ├── TCPTransport.ts
    ├── Transport.ts
    └── WebSocketTransport.ts
```
以下是项目中的核心类文件
* Server.ts 服务注册, 端口注册等关于服务器的一些方法
* MatchMaker.ts 玩家匹配, 对申请连接的玩家进行房间分配
* Room.ts 加入房间, 接收信息, 离开房间等一些客户端行为的回调

下一篇进行Server.ts的源码分析, 这个也是使用Colyseus能接触到的第一个类

