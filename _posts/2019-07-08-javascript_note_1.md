---
layout: post
title: "你不知道的JavaScript(上卷)"
description: ""
date: 2019-07-08
tags: [JavaScript,读书笔记]
comments: true
share: true
---
## 引擎
从头到尾负责整个 JavaScript 程序的编译及执行过程。JavaScript 引擎不会有大量的(像其他语言编译器那么多的)时间用来进行优化，因为与其他语言不同，编译过程不是发生在构建之前的。大部分情况下编译发生在代码执行前的几微秒的时间内
## 编译器 
负责语法分析及代码生成等脏活累活。
## 作用域
负责收集并维护由所有声明的标识符(变量)组成的一系列查询，并实施一套非常严格的规则，确定当前执行的代码对这些标识符的访问权限。作用域共有两种主要的工作模型。 词法作用域与动态作用域

## LHS 与 RHS
变量出现在赋值操作的左侧时进行 LHS 查询，出现在右侧时进行 RHS 查询
``` js
var a = 2;
```
1. 遇到 var a，编译器会询问作用域是否已经有一个该名称的变量存在于同一个作用域的集合中。如果是，编译器会忽略该声明，继续进行编译;否则它会要求作用域在当前作用域的集合中声明一个新的变量，并命名为 a。
2. 接下来编译器会为引擎生成运行时所需的代码，这些代码被用来处理 a = 2 这个赋值操作。引擎运行时会首先询问作用域，在当前的作用域集合中是否存在一个叫作 a 的变量。如果是，引擎就会使用这个变量;如果否，引擎会继续查找该变量

``` js
function foo(a) { // NOTE: 这里有个LHS a=2
     console.log( a ); // RHS
}
foo( 2 );
```
区别:

非严格模式下
* RHS 查询在所有嵌套的作用域中遍寻不到所需的变量，引擎就会抛出 **ReferenceError** 异常 
* LHS 查询时，如果在顶层(全局作用域)中也无法找到目标变量，全局作用域中就会创建一个具有该名称的变量，并将其返还给引擎，前提是程序运行在非 “严格模式”下。 undefined
 
``` js
a = 2 // RIGHT
```
 
``` js
"use strict"
a = 2 // WRONG: ReferenceError
```

严格模式
* LHS 查询失败时，并不会创建并返回一个全局变量，引擎会抛出同 RHS 查询失败时类似的 ReferenceError 异常。
* RHS 查询找到了一个变量，但是你尝试对这个变量的值进行不合理的操作， 比如试图对一个非函数类型的值进行函数调用，或着引用 null 或 undefined 类型的值中的 属性，那么引擎会抛出另外一种类型的异常，叫作 TypeError。

``` js
"use strict";

false.true = "";              //TypeError
(14).sailing = "home";        //TypeError
"with".you = "far away"; 
```

## 编译流程
1. 分词/词法分析(Tokenizing/Lexing) 这个过程会将由字符组成的字符串分解成(对编程语言来说)有意义的代码块，
2. 解析/语法分析(Parsing) 生成 抽象语法树(AST)
3. 生成代码

## 提升
这意味着无论作用域中的声明出现在什么地方，都将在代码本身被执行前首先进行处理。 可以将这个过程形象地想象成所有的声明(变量和函数)都会被“移动”到各自作用域的 最顶端，这个过程被称为**提升**。
只有声明本身会被提升，而赋值或其他运行逻辑会留在原地。
``` js
a = 2;
var a;
console.log( a ); // 2
```

``` js
console.log( a ); // undefine
var a = 2; 
```

## 箭头函数
箭头函数在涉及 this 绑定时的行为和普通函数的行为完全不一致。它放弃了所 有普通 this 绑定的规则，取而代之的是用当前的词法作用域覆盖了 this 本来的值。
## 闭包
``` js
function foo() {
    var a = 2;
    function bar() {
        console.log(a);
    }
    return bar;
}
var baz = foo();
baz(); // 2
```

### 以下代码的区别
* 尽管循环中的五个函数是在各个迭代中分别定义的， 但是它们都被封闭在一个共享的全局作用域中，因此实际上只有一个 i
 
``` js
for (var i = 1; i <= 5; i++) {
    setTimeout(function timer() {
        console.log(i);
    }, i * 1000);
}
```

* IIFE 只是一 个什么都没有的空作用域。它需要包含一点实质内容才能为我们所用。
 
``` js
for (var i = 1; i <= 5; i++) {
    (function () {
        setTimeout(function timer() {
            console.log(i);
        }, i * 1000);
    })();
}
```

* 在迭代内使用 IIFE 会为每个迭代都生成一个新的作用域，使得延迟函数的回调可以将新的
作用域封闭在每个迭代内部，每个迭代中都会含有一个具有正确值的变量供我们访问

``` js
for (var i = 1; i <= 5; i++) {
    (function (j) {
        setTimeout(function timer() {
            console.log(j);
        }, j * 1000);
    })(i); // <= 注意这里
}
```

### 模块
1. 为创建内部作用域而调用的一个包装函数 
2. 包装函数的返回值必须至少包括一个对内部函数的引用，这样就会创建涵盖整个包装函数内部作用域的闭包。
``` js
function CoolModule() {
    var something = "cool";

    var another = [1, 2, 3];
    function doSomething() {
        console.log(something);
    }
    function doAnother() {
        console.log(another.join(" ! "));
    }
    return {
        doSomething: doSomething, doAnother: doAnother
    };
}
var foo = CoolModule(); foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```

ES6 的模块没有“行内”格式，必须被定义在独立的文件中(一个文件一个模块)。浏览 器或引擎有一个默认的“模块加载器”(可以被重载，但这远超出了我们的讨论范围)可 以在导入模块时异步地加载模块文件。

### 示例代码
``` js
var MyModules = (function Manager() {
    var modules = {};
    function define(name, deps, impl) {
        for (var i = 0; i < deps.length; i++) {
            deps[i] = modules[deps[i]];
        }
        modules[name] = impl.apply(impl, deps); // 依赖传入
        console.log(modules[name].hello)
        /** 
         * modules 的结构
         * {
         *    foo: { hello:  function}
         *    bar: { awesome: function}
         * }
         */
    }
    function get(name) {
        return modules[name];
    } 
    
    return {
        define: define,
        get: get
    };
})();
MyModules.define("bar", [], function () {
    function hello(who) {
        return "Let me introduce: " + who;
    }
    return {
        hello: hello
    };
});
MyModules.define("foo", ["bar"], function (bar) {
    var hungry = "hippo";
    function awesome() {
        console.log(bar.hello(hungry).toUpperCase());
    }
    return {
        awesome: awesome
    };
});
var bar = MyModules.get("bar");
var foo = MyModules.get("foo");
console.log(bar.hello("hippo")); // Let me introduce: hippo 
foo.awesome(); // LET ME INTRODUCE: HIPPO
```