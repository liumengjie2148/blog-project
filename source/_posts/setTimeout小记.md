---
title: setTimeout小记
date: 2018-07-04 15:08:25
tags:
---
## window setTimeout() 方法

### 定义

 <p>setTimeout() 方法用于在指定的毫秒数后调用函数或计算表达式。</p>

### 语法

 ```
   setTimeout(code, milliseconds, params1, params2, ...)
   setTimeout(function, milliseconds, params1, params2, ...)
 ```

 <p>返回值： 返回一个ID（数字），可以将这个ID传递给clearTimeout()来取消执行setTimeout()方法。</p>

### 实例

* 实例1： 用setTimeout() 方法实现 setInternal()

 ```
   setTimeout(function(){
     console.log('hello');
     setTimeout(function(){
        console.log('hello');
      }, 1000)
   }, 1000)

 ```

* 实例2：下面的代码执行结果是？

 ```
   for(var i = 0; i< 5; i++) {
      setTimeout(function() {
        console.log(i);
      }, 1000)
   }

 ```

 执行结果： 输出5次5
 原因：setTimeout没有实现多线程，它背后的真相是这样的：

 JavaScript引擎是单线程运行的,浏览器无论在什么时候都只且只有一个线程在运行JavaScript程序。
 浏览器的内核是多线程的，它们在内核控制下相互配合以保持同步，一个浏览器至少实现三个常驻线程：JavaScript引擎线程，GUI渲染线程，浏览器事件触发线程。
   * JavaScript引擎是基于事件驱动单线程执行的，JavaScript引擎一直等待着任务队列中任务的到来，然后加以处理，浏览器无论什么时候都只有一个JavaScript线程在运行JavaScript程序。
   * GUI渲染线程负责渲染浏览器界面，当界面需要重绘（Repaint）或由于某种操作引发回流(Reflow)时,该线程就会执行。但需要注意，GUI渲染线程与JavaScript引擎是互斥的，当JavaScript引擎执行时GUI线程会被挂起，GUI更新会被保存在一个队列中等到JavaScript引擎空闲时立即被执行。
   * 事件触发线程，当一个事件被触发时该线程会把事件添加到待处理队列的队尾，等待JavaScript引擎的处理。这些事件可来自JavaScript引擎当前执行的代码块如setTimeout、也可来自浏览器内核的其他线程如鼠标点击、Ajax异步请求等，但由于JavaScript的单线程关系所有这些事件都得排队等待JavaScript引擎处理（当线程中没有执行任何同步代码的前提下才会执行异步代码）。

 所以循环执行的时候，每次循环执行的时候，由于时间没到，所以setTimeout()方法里的函数不执行，添加到事件队列中，循环结束，i等于5，事件队列中存在5个事件函数，当时间到了的时候，会依次执行这5个函数，所以输出5次5。

 * 实例3：如何让实例2中的代码输出每次循环时的i值？

 第一种方法：用setTimeout()方法的第3个参数将i值传递到匿名函数中

 ```
   for(var i = 0; i < 5; i++) {
     setTimeout(function(count) {
       console.log(count)
     }, 1000, i)
   }

 ```
 第二种方法：用ES6的let关键字定义变量i

 ```
  for(let i = 0; i < 5; i++) {
    setTimeout(function() {
      console.log(i)
    }, 1000)
  }
 ```
   解析：上面的代码中，变量i是let声明的，当前的i只在本轮循环有效，所以每一次循环的i其实都是一个新的变量，所以最后输出的是每次循环结束时的i值。你可能会问，如果每一轮循环的变量i都是重新声明的，那它怎么知道上一轮循环的值，从而计算出本轮循环的值？这是因为JavaScript引擎内部会记住上一轮循环的值，初始化本轮变量i时，就在上一轮循环的基础上进行计算。
