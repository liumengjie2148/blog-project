---
title: javascript执行机制
date: 2018-07-09 17:51:47
tags:
---

## 概览

   ![概览](images/gaiLan.png "")

 js引擎是单线程的，如上图中，他负责维护任务队列，并通过Event Loop的机制按顺序把任务放入栈中执行。而图中的异步处理模块就是runtime提供的，
 拥有和js引擎互不干扰的线程。
   * 引擎： 解释并编译代码，让它变成能交给机器运行的代码。
   * runtime： 就是运行环境，它提供一些对外接口供js调用以跟外界打交道，比如，浏览器环境、Node.js环境。不同的runtime会停供不同的接口，比如，在Node.js环境中，我们可以通过`require`来引入模块，而在浏览器中，我们有`window`、DOM。

接下来介绍图中的： 栈和任务队列

## 栈
 运行下面这段代码：

 ```
  function bar() {
     console.log(1)
  }

  function foo() {
    console.log(2)
    bar();
  }

  setTimeout(() => {
    console.log(3)
  });

  foo();
 ```
 它在栈中的入栈、出栈过程，如下图：

   ![函数入栈、出栈过程图](images/articlex.png "")

## 任务队列

 js中有两类任务队列：宏任务队列（macro tasks）和 微任务队列（micro tasks）。宏任务队列可以有多个，微任务队列只有一个。那么什么任务会分到那个队列呢？

 * 宏任务：script（全局任务），setTimeout，setInternal，setImmediate, I/O, UI rendering.
 * 微任务：process.nextTick, Promise, Object.observer, MutationObserver.

 我们上面讲到，当stack空的时候，就会从任务队列中，去任务来执行。共分3步：

 1. 取一个宏任务来执行，执行完毕后，执行下一步；
 2. 取一个微任务来执行，执行完毕后，再取一个微任务来执行，直到微任务队列为空，执行下一步；
 3. 更新UI渲染。

 Event Loop 会无限循环执行上面3步，这就是Event Loop的主要控制逻辑。其中，第3步（更新UI渲染）会根据浏览器的逻辑，决定要不要马上执行更新，毕竟更新UI成本大，所以，一般都会比较长的时间间隔执行一次更新。

 从执行步骤来看，我们发现微任务受到了特殊待遇！我们代码开始执行都是从script（全局任务）开始，所以，一旦我们的全局任务（属于宏任务）执行完，就马上执行完整个微任务队列。看个例子：

 ```
 console.log('script start');

 Promise.resolve().then(() => {
   console.log('p 1');
 });

 setTimeout(() => {
   console.log('setTimeout');
 }, 0);

 var s = new Date();
 while(new Date() - s < 50); // 阻塞50ms

 Promise.resolve().then(() => {
  console.log('p 2');
 });

 console.log('script end');

 /*** output ***/

 // one macro task
 script start
 script end

 // all micro tasks

 p 1
 p 2

 // one macro task again
 setTimeout

 ```
上面之所以加50ms的阻塞，是因为`setTimeout`的delayTime最少是4ms，为了避免认为`setTimeout`是因为4ms的延迟而在后面才被执行的，我们加了50ms的阻塞。在微任务中，`process.nextTick`是一个特殊的任务，它会被直接插入到微任务的队首（当然了，多个`process.nextTick`之间也是先入先出的），优先级最高。