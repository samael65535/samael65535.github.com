---
layout: post
title: "Command_Pattern_第一节"
tag: [C++]
---

# 定义 #
把一个请求包装成一个对象, 从而使用户可以用不同的请求进行参数化.

# 使用情景 #
假设一个游戏中的WSAD键分别代表不能是角色移动方向.
## 简单实现 ##
最简单的办法就是有一连串的if对不同的按键进行不同的处理:

``` C++ 
void handleInput() {
	if (isPressed('W')) doUp();
	else if(isPressed('S')) doDown();
	else if(isPressed('A')) doLeft();
	else if(isPressed('D')) doRight();
}
```

以上就是一个最简单的实现, 但是还有些不够, 比如:

* doUp()这种操作多数的游戏对象都会需要(如NPC), 可以把不同行为抽象成一个新类用于不同对象的操作, 方便维护
* 如果说游戏的doUp()没有完成, 又接收到了doDown()的事件, 这时有游戏人物应该是在空中有一些别的操作, 这时单一函数为了对游戏对象的各个状态进行判断和处理会变得很长, 不利于扩展与易读.

## 扩展一下 ##
对于上下左右的移动我们都可以当做一个指令进行操作, 所以我们先定义一下Command的基类:

``` C++ 
class Command {
public:
	virtual ~Command() {};
	virtual void execute(Actor *actor) = 0; //evecute是一个纯虚函数,子类用于其详细实现
}
```
再定义一个玩家类, 包含了获取输入与处理输入的操作, 并包含了四个指令类

``` C++ 
class InputHandler {
public:
	Command *handleInput();
private:
    Command *_upCommand;
    Command *_downCommand;
    Command *_leftCommand;
    Command *_rightCommand;
}
```  

现在来实现一个操作向上移动的对象

``` C++
class UpCommand {
public:
	vitrual void execute(Actor *actor) {
		actor -> doUp();    // delegate
	}
}
```

这样的话我们就可以对玩家进行向上操作了, 相应的操作代码要改为:

``` C++
Command* HandleInput::handleInput() {
	if(isPressed('W')) return _upCommand;
	if(isPressed('S')) return _downCommand;
	if(isPressed('A')) return _leftCommand;
	if(isPressed('D')) return _rightCommand;
	reutrn NULL;
}

void Actor::doSomething() {
	Command *command = inputHandle.handleInput();
	if (command) {
		command -> execute(this);
	}
}
```

# 还没有完 #
如果我们想要对游戏对象进行撤消操作(比如Braid中的倒流), 应该怎么办?

# 参考 #
[Game Programming Pattern 中的第一章](http://gameprogrammingpatterns.com/command.html)

