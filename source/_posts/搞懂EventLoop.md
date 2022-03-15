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
<!-- more -->

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

#### 分析一下
JS 运行机制为**从上而下**，那么这里的执行顺序应该是什么样的呢？
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
##### 第四轮循环：
1）、执行所有的微任务
2）、执行process3 打印出 10
3）、执行then3 打印出 12

代码执行结束：
最终打印顺序为：1 7 6 8 2 4 3 5 9 11 10 12

## Node环境的循环机制
Node的Event loop一共分为**6**个阶段，每个细节具体如下：
- **`timers`**: 执行setTimeout和setInterval中到期的callback。
- **`pending callback`**: 上一轮循环中少数的callback会放在这一阶段执行。
- **`idle, prepare`**: 仅在内部使用。
- **`poll`**: 最重要的阶段，执行pending callback，在适当的情况下回阻塞在这个阶段。
- **`check`**: 执行setImmediate(setImmediate()是将事件插入到事件队列尾部，主线程和事件队列的函数执行完成之后立即执行setImmediate指定的回调函数)的callback。
- **`close callbacks`**: 执行close事件的callback，例如socket.on('close'[,fn])或者http.server.on('close, fn)。

![Node EventLoop](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/1/18/16860f8f8f7f053d~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

具体细节如下：

### timers
执行setTimeout和setInterval中到期的callback，执行这两者回调需要设置一个毫秒数，理论上来说，应该是时间一到就立即执行callback回调，但是由于system的调度可能会延时，达不到预期时间。
以下是官网文档解释的例子：

```js
const fs = require('fs');

function someAsyncOperation(callback) {
  // Assume this takes 95ms to complete
  fs.readFile('/path/to/file', callback);
}

const timeoutScheduled = Date.now();

setTimeout(() => {
  const delay = Date.now() - timeoutScheduled;

  console.log(`${delay}ms have passed since I was scheduled`);
}, 100);


// do someAsyncOperation which takes 95 ms to complete
someAsyncOperation(() => {
  const startCallback = Date.now();

  // do something that will take 10ms...
  while (Date.now() - startCallback < 10) {
    // do nothing
  }
});

```

当进入事件循环时，它有一个空队列（fs.readFile()尚未完成），因此定时器将等待剩余毫秒数，当到达95ms时，fs.readFile()完成读取文件并且其完成需要10毫秒的回调被添加到轮询队列并执行。
当回调结束时，队列中不再有回调，因此事件循环将看到已达到最快定时器的阈值，然后回到timers阶段以执行定时器的回调。
在此示例中，您将看到正在调度的计时器与正在执行的回调之间的总延迟将为105毫秒。

### pending callbacks
此阶段执行某些系统操作（例如TCP错误类型）的回调。 例如，如果TCP socket ECONNREFUSED在尝试connect时receives，则某些* nix系统希望等待报告错误。 这将在pending callbacks阶段执行。

### poll
该poll阶段有两个主要功能：
- 执行I/O回调。
- 处理轮询队列中的事件。

**当事件循环进入poll阶段并且在timers中没有可以执行定时器时，将发生以下两种情况之一**
如果poll队列不为空，则事件循环将遍历其同步执行它们的callback队列，直到队列为空，或者达到system-dependent（系统相关限制）。

**如果poll队列为空，则会发生以下两种情况之一**
- 如果有setImmediate()回调需要执行，则会立即停止执行poll阶段并进入执行check阶段以执行回调。
- 如果没有setImmediate()回到需要执行，poll阶段将等待callback被添加到队列中，然后立即执行。

**当然设定了 timer 的话且 poll 队列为空，则会判断是否有 timer 超时，如果有的话会回到 timer 阶段执行回调。**

### check
此阶段允许人员在poll阶段完成后立即执行回调。
如果poll阶段闲置并且script已排队setImmediate()，则事件循环到达check阶段执行而不是继续等待。
setImmediate()实际上是一个特殊的计时器，它在事件循环的一个单独阶段运行。它使用libuv API来调度在poll阶段完成后执行的回调。
通常，当代码被执行时，事件循环最终将达到poll阶段，它将等待传入连接，请求等。
但是，如果已经调度了回调setImmediate()，并且轮询阶段变为空闲，则它将结束并且到达check阶段，而不是等待poll事件。

```js
console.log('start')
setTimeout(() => {
  console.log('timer1')
  Promise.resolve().then(function() {
    console.log('promise1')
  })
}, 0)
setTimeout(() => {
  console.log('timer2')
  Promise.resolve().then(function() {
    console.log('promise2')
  })
}, 0)
Promise.resolve().then(function() {
  console.log('promise3')
})
console.log('end')

```

如果Node版本是v11.x，那么执行结果和浏览器一致，结果如下：
```js
start
end
promise3
timer1
promise1
timer2
promise2
```

如果是v10版本上，那结果将会出现两种情况
**如果time2定时器已经在执行队列中了**
```js
start
end
promise3
timer1
timer2
promise1
promise2
```

**如果time2定时器没有在执行对列中，执行结果为**
```js
start
end
promise3
timer1
promise1
timer2
promise2
```

具体情况可以参考poll阶段的两种情况。

![NodeEventLoop](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/1/19/1686530bcd4e456a~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)

## setImmediate() 的setTimeout()的区别
**setImmediate和setTimeout()是相似的，但根据它们被调用的时间以不同的方式表现。**
- setImmediate()设计用于在当前poll阶段完成后check阶段执行脚本 。
- setTimeout() 安排在经过最小（ms）后运行的脚本，在timers阶段执行。

### 举个例子
```js
setTimeout(() => {
  console.log('timeout');
}, 0);

setImmediate(() => {
  console.log('immediate');
});

```

**执行定时器的顺序将根据调用它们的上下文而有所不同。 如果从主模块中调用两者，那么时间将受到进程性能的限制。**
**其结果也不一致**

**如果在I / O周期内移动两个调用，则始终首先执行立即回调：**

```js
const fs = require('fs');

fs.readFile(__filename, () => {
  setTimeout(() => {
    console.log('timeout');
  }, 0);
  setImmediate(() => {
    console.log('immediate');
  });
});

```

其结果可以确定一定是immediate => timeout。
主要原因是在I/O阶段读取文件后，事件循环会先进入poll阶段，发现有setImmediate需要执行，会立即进入check阶段执行setImmediate的回调。
然后再进入timers阶段，执行setTimeout，打印timeout。

```js
   ┌───────────────────────────┐
┌─>│           timers          │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │
│  └─────────────┬─────────────┘      ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │
│  │           poll            │<─────┤  connections, │
│  └─────────────┬─────────────┘      │   data, etc.  │
│  ┌─────────────┴─────────────┐      └───────────────┘
│  │           check           │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │
   └───────────────────────────┘

```

## Process.nextTick()
process.nextTick()虽然它是异步API的一部分，但未在图中显示。这是因为process.nextTick()从技术上讲，它不是事件循环的一部分。

process.nextTick()方法将 callback 添加到next tick队列。 一旦当前事件轮询队列的任务全部完成，在next tick队列中的所有callbacks会被依次调用。

换种理解方式：

**当每个阶段完成后，如果存在 nextTick 队列，就会清空队列中的所有回调函数，并且优先于其他 microtask 执行。**

### 举个例子
```js
let bar;

setTimeout(() => {
  console.log('setTimeout');
}, 0)

setImmediate(() => {
  console.log('setImmediate');
})
function someAsyncApiCall(callback) {
  process.nextTick(callback);
}

someAsyncApiCall(() => {
  console.log('bar', bar); // 1
});

bar = 1;

```
在NodeV10中上述代码执行可能有两种答案，一种为：

```js
bar 1
setTimeout
setImmediate

```
另一种为
```js
bar 1
setImmediate
setTimeout

```

无论哪种，始终都是先执行process.nextTick(callback)，打印bar 1。

