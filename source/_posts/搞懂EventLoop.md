---
title: 搞懂EventLoop
date: 2022-03-11 08:00:00
categories: 
	- 前端技术
	- 计算机通识
tags:
	- EventLoop
	- 事件驱动
---
> 作者：李旭光
> 引用请标明出处

# 前言
EventLoop 翻译过来就是事件循环，那啥是事件循环啊，这个要从javascript是如何执行的说起。
要知道javascript是一个运行在浏览器或者Node环境的*单线程*执行的脚本语言，但是为了解决单线程阻塞的问题，这里就引入了事件循环的机制，也就是我们常说的异步特性。

但是虽然都是JavaScript，都是事件循环，都有异步特性，但浏览器环境和Node环境的实现方式是不一样的，这里要先说明一下。
浏览器的事件循环是HTML定义的规范，而Node环境是利用libuv库实现的，这里我们先从浏览器的事件循环开始说。
## 浏览器的事件循环
首先我们要知道，浏览器的事件循环是有这么两个部分构成，一个叫做主线程（main thread），另一个叫做调用栈（call-stack），所有的任务（Task）都会被放到调用栈里，等待主线程调用，这个怎么理解呢，打个比方，主线程就像是物流配送中的传送带，如果没有快递往上放的时候，它是空的，当有快递包裹放上去的时候，它就开始运行，而你购买的产品要放倒包裹里，然后包裹在放到传送带上，最终被送到你家里，这里的传送带就是主线程，包裹就是调用栈，而购买的产品就是任务，不知道这样解释是否能好理解一些。

### 同步、异步任务
JavaScript中的任务有两种，一种是同步任务，另一种是异步任务。
- 同步任务会排着队，逐个执行
- 异步任务在执行得到结果后，将回调函数添加到任务队列中，等着主线程空了再执行。

### 调用栈
栈，是一种数据结构，后进先出（LIFO），后入栈的先执行，执行完后出栈执行栈顶新的那个函数，直到栈空，像个瓶子，只有一头有口。

### 任务队列
队列，也是一种数据结构，先进先出（FIFO），队头出，队尾进，先到先得，就像排队买东西，或者汽车排队过隧道。

### 任务
任务分为两种，一种叫做宏任务（MacroTask），另一种叫做微任务（MicroTask），首先他们都是**异步队列**中的任务，那它们之间是什么关系呢？简单说来就是微任务是VIP优先执行，全部微任务执行完之后才轮到普通用户宏任务执行，而且执行完一个后马上要看有没有新的微任务执行，有的话宏任务还得等着，就这样往复，看着像不像是在银行等着排队办业务的你（其实是我）。
![JavaScript的执行流程](https://pic3.zhimg.com/80/v2-eba59eaab15f8a27c1b58f69513f0c0e_1440w.jpg)
![宏任务与微任务](https://upload-images.jianshu.io/upload_images/18747821-d613fb9d7e538069.png)
那么哪些是宏任务，哪些是微任务呢？看下表吧。

|宏任务|微任务|
|-|-|
| setTimeout、setInterval、js主代码、setImmediate(Node)、requestAnimationFrame(浏览器)|process.nextTick、Promise的then方法|

### 举个例子
``` js
console.log('1');

setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})
```

这里的执行顺序应该是怎样的呢？

1. script start
2. async2 end
3. Promise
4. script end
5. async1 end
6. promise1
7. promise2
8. setTimeout

#### 分析一下
JS 运行机制为**从上而下**，
##### 第一轮循环：
1）、首先打印 1
2）、接下来是setTimeout是异步任务且是宏任务，加入宏任务暂且记为 setTimeout1
3）、接下来是 process 微任务 加入微任务队列 记为 process1
4）、接下来是 new Promise 里面直接 resolve(7) 所以打印 7 后面的then是微任务 记为 then1
5）、setTimeout 宏任务 记为 setTimeout2

> 第一轮循环打印出的是 1 7
> 当前宏任务队列：setTimeout1, setTimeout2
> 当前微任务队列：process1, then1,

##### 第二轮循环：
1）、执行所有微任务
2）、执行process1，打印出 6
3）、执行then1 打印出8
4）、微任务都执行结束了，开始执行第一个宏任务
5）、执行 setTimeout1 也就是 第 3 - 14 行
6）、首先打印出 2
7）、遇到 process 微任务 记为 process2
8）、new Promise中resolve 打印出 4
9）、then 微任务 记为 then2

> 第二轮循环结束，当前打印出来的是 1 7 6 8 2 4
> 当前宏任务队列：setTimeout2
> 当前微任务队列：process2, then2
##### 第三轮循环：
1）、执行所有的微任务
2）、执行 process2 打印出 3
3）、执行 then2 打印出 5
4）、执行第一个宏任务，也就是执行 setTimeout2 对应代码中的 25 - 36 行
5）、首先打印出 9
6）、process 微任务 记为 process3
7）、new Promise执行resolve 打印出 11
8）、then 微任务 记为 then3

> 第三轮循环结束，当前打印顺序为：1 7 6 8 2 4 3 5 9 11
> 当前宏任务队列为空
> 当前微任务队列：process3，then3
###### 第四轮循环：
1）、执行所有的微任务
2）、执行process3 打印出 10
3）、执行then3 打印出 12

代码执行结束：
最终打印顺序为：1 7 6 8 2 4 3 5 9 11 10 12

## Node环境的循环机制
Node的Event loop一共分为6个阶段，每个细节具体如下：
timers: 执行setTimeout和setInterval中到期的callback。
pending callback: 上一轮循环中少数的callback会放在这一阶段执行。
idle, prepare: 仅在内部使用。
poll: 最重要的阶段，执行pending callback，在适当的情况下回阻塞在这个阶段。
check: 执行setImmediate(setImmediate()是将事件插入到事件队列尾部，主线程和事件队列的函数执行完成之后立即执行setImmediate指定的回调函数)的callback。
close callbacks: 执行close事件的callback，例如socket.on('close'[,fn])或者http.server.on('close, fn)。