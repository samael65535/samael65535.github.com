---
layout: post
title: "Colyseus源码阅读3"
description: ""
date: 2019-09-17
tags: [Colyseus,源码阅读,Node,TypeScript,服务器开发,游戏开发]
comments: true
share: true
---
MatchMaker 管理房间与匹配于连接来的玩家,是Server与Room的中介
```bash
npx arkit -e "rooms/*,errors/*,serializer/*,transport/*,Debug.ts,discovery/*,index.ts,presence/*" -f MatchMaker.ts -o ../matchmaker.svg
```
![matchmaker](http://pic-blog.test.upcdn.net/2019/09/17/matchmaker.svg)

## 启动服务器时
1. 当服务启动后实例化Server的同时, 也在Server类中新建了MatchMaker函数 (attch)
2. 注册MatchMaker的一系列请求 (handleMatchMakeRequest)
3. 通过房间名中把MatchMaker与Room之间的行为进行连接 (defined)

## 玩家连接
#### 查询房间

``` ts
// 匹配到合适的房间并返回对应的房间数据
  public async queryRoom(roomName: string, options: ClientOptions): Promise<RoomListingData> {
    return await this.awaitRoomAvailable(roomName, async () => {
      const handler = this.handlers[roomName];
      if (!handler) {
        throw new MatchMakeError(`no available handler for "${roomName}"`, Protocol.ERR_MATCHMAKE_NO_HANDLER);
      }

      const query = this.driver.findOne({
        locked: false,
        name: roomName,
        ...handler.getFilterOptions(options),
      });

      if (handler.sortOptions) {
        query.sort(handler.sortOptions);
      }

      return await query;
    });
  }
```

### 创建房间
``` ts
 public async createRoom(roomName: string, clientOptions: ClientOptions): Promise<RoomListingData> {
    const registeredHandler = this.handlers[ roomName ];
    // 实例化Room类
    const room = new registeredHandler.klass();

    // set room public attributes
    room.roomId = generateId();
    room.roomName = roomName;
    room.presence = this.presence;

    // create a RoomCache reference.
    // 房间相关信息
    room.listing = this.driver.createInstance({
      name: roomName,
      processId: this.processId,
      roomId: room.roomId,
      ...registeredHandler.getFilterOptions(clientOptions),
    });

    if (room.onCreate) {
      try {
        await room.onCreate(merge({}, clientOptions, registeredHandler.options));

      } catch (e) {
        debugAndPrintError(e);
        throw new MatchMakeError(e.message, Protocol.ERR_MATCHMAKE_UNHANDLED);
      }
    }

    room._internalState = RoomInternalState.CREATED;
    room.listing.maxClients = room.maxClients;

    // imediatelly ask client to join the room
    debugMatchMaking('spawning \'%s\', roomId: %s, processId: %s', roomName, room.roomId, this.processId);

// 注册消息
    room.on('lock', this.lockRoom.bind(this, roomName, room));
    room.on('unlock', this.unlockRoom.bind(this, roomName, room));
    room.on('join', this.onClientJoinRoom.bind(this, room));
    room.on('leave', this.onClientLeaveRoom.bind(this, room));
    room.once('dispose', this.disposeRoom.bind(this, roomName, room));
    room.once('disconnect', () => room.removeAllListeners());

    // room always start unlocked
    await this.createRoomReferences(room, true);
    await room.listing.save();

// 向房间发送创建成功的消息
    registeredHandler.emit('create', room);

    return room.listing;
  }
```

### 加入房间
玩家连接时会使用('joinOrCreate', 'create', 'join', 'joinById')的方法去处理相应的房间操作(在handleMatchMakeRequest里注册方法), 对应四个方法都会调用reserveSeatFor这个方法

``` ts
	 
  protected async reserveSeatFor(room: RoomListingData, options) {
  //在加入或者创建房间后调用, 用于占用一个房间位置
    const sessionId: string = generateId();

    debugMatchMaking(
      'reserving seat. sessionId: \'%s\', roomId: \'%s\', processId: \'%s\'',
      sessionId, room.roomId, this.processId,
    );

    const [_, reserveSeatSuccessful] = await this.remoteRoomCall(room.roomId, '_reserveSeat', [sessionId, options]);
    if (!reserveSeatSuccessful) {
      throw new SeatReservationError(`${room.roomId} is already full.`);
    }

    return { room, sessionId };
  }
```