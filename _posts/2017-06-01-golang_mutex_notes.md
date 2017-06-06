---
layout: post
title: "Go并发笔记"
description: ""
date: 2017-06-01
tags: [Go]
comments: true
share: true
---

最近在读[Go程序设计语言](https://book.douban.com/subject/27044219/), [9.7并发的非阻塞缓存](https://github.com/adonovan/gopl.io/tree/master/ch9)里面有很好的例子, 在这里记下笔记一下:

# Memoizing
是一种用换取时间的方法, 通过缓存之前的结果来提高执行速度. 示例代码中去在调用http.GET之前会先去查看内存中是否缓存了之前的调用, 如果有的话则是从内存中读取,这个例子中没有给缓存增加过期时间.

# 并发的Memoizing

之前写node的时候使用过undersore.js的memoizing,但是node是基本单线程的, 所以在访问缓存时不存同时读写的问题.
但在Go中, 可能会有多个Goroutine去调用同一个代码, 所以不可避免会发上同时调用的情况

# 解决办法
这里用Mem来表示缓存的map
## v1
最简单的办法是在操作的Mem代码段前后加上锁, 这样就可以防止同时调用(这里的代码都做了简化)

``` Go
func Get(url string) string {
    mu.Lock()
    defer mu.Unlock()    
    _, ok := Memo[url]
    if !ok {
        res := httpGetBody(url) // 最慢的调用
        Memo[url] = res
    }
    return Memo[url]
}
```

虽然解决了同时访问的冲突, 但是在这里的操作依然变成了串行的操作, 而且如果有些url没有命中httpGetBody, 那么会拖慢后续的操作

## v2
进一点思考, 我们其实并不需要读取的时候加上锁, 其实是在写入的时候加上锁, 这时改进如下: 

``` Go
func Get(url string) string {
    mu.Lock()
    res, ok := Memo[url]
    mu.Unlock()
    if !ok {
        res = httpGetBody(url) // 最慢的调用
        mu.Lock()
        Memo[url] = res
        mu.Unlock()    
    }
    return Memo[url]
}
```
这样就可以减少当url没有命中时对后面读取操作产生的阻塞, 但是这样依然有一个问题就是, 当几个Goroutine调用一个url时, Mem并没写入, 那么httpGetBody可能会同时调用很多次也会拖慢执行速度.

## v3
由于httpGetBody是调用最慢的, 如果说我们可以在Memo中加入一个channel, 在httpGetBody进行阻塞, 这样就可以保证 httpGetBody(url)最多只能调用一次

``` Go
type entry struct {
    url   string
    ready chan struct{}
}

// Memo map[string]*entry
func Get(url string) string {
    mu.Lock()
    e, ok := Memo[url]
    if !ok {
        // 如果没有缓存
        Memo[url].ready = &entry{ready: make(chan struct{})}
        // 注意这个Unlock要放到httpGetBody之前, 否则会在调用httpGetBody之后解锁这样会拖慢性能
        mu.Unlock()          
        
        res := httpGetBody(url) // 最慢的调用
        Mem[url].url = res
        close(Memo[url].ready)
    } else {
       //阻塞
       mu.Unlock() 
       <- e.ready

    }

   return Memo[url].url
}
```
## v4
其实书上还写了一种办法,建立一个Gorouinte, 相当于生产者, 每次调用Get时相当于向server发一个读取请求之后使用两个Gorouinte, 对得到的数据进行传递

核心代码, 摘自示例代码:

``` GO

func (memo *Memo) server(f Func) {
	cache := make(map[string]*entry)
	for req := range memo.requests {
		e := cache[req.key]
		if e == nil {
			// This is the first request for this key.
			e = &entry{ready: make(chan struct{})}
			cache[req.key] = e
			go e.call(f, req.key) // call f(key)
		}
		go e.deliver(req.response)
	}
}

func (e *entry) call(f Func, key string) {
	// Evaluate the function.
	e.res.value, e.res.err = f(key)
	// Broadcast the ready condition.
	close(e.ready)
}

func (e *entry) deliver(response chan<- result) {
	// Wait for the ready condition.
	<-e.ready
	// Send the result to the client.
	response <- e.res
}
```

简单的使用基准测试测试了一下, 发现这种方式的情况与v3还是差了一点


