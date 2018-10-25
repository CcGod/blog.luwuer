---
title: JavaScript与浏览器的线程
tags: [js, 浏览器]
date: 2017-09-19 17:40:50
---
> 转载自[这里](https://mp.weixin.qq.com/s?__biz=MzU0NDU3NzM0Ng==&mid=2247483675&idx=1&sn=447b8b89b20de742a0b0d0fe6cc69896&chksm=fb7b4336cc0cca2077074a90945efc35289819d227146e3e03e2566eb655c57ebbf7a06cd3ca&scene=0&key=88007341ad887ed217d60acffb25ad5ba768c9a798cd4bc3f8e68cd319d78b27010ad7f228e66e1aea1d199a196a1f8187a4480141309bc82fb8c0d0a4b670b5677d478df3ba0a164da5583b0dd1aa42&ascene=0&uin=MjEzMTAwMzgyNQ%3D%3D&devicetype=iMac+MacBookAir7%2C1+OSX+OSX+10.12.6+build(16G29)&version=12020810&nettype=WIFI&lang=zh_CN&fontScale=100)，部分修改。

> 参考[这里](https://www.cnblogs.com/xuan52rock/p/4718259.html)

### JavaScript 是单线程的

作为浏览器脚本语言，JavaScript 主要用于处理页面中用户交互，以及操作 DOM 树、CSS 样式树（当然也包括服务器逻辑的交互处理），这决定了它只能是单线程，否则会带来很复杂的同步问题。比如，假定 JavaScript 同时有两个线程，一个线程在某个 DOM 节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准？（当然我们可以通过锁来解决上面的问题，但大大提高了复杂性）

> 为了利用多核 CPU 的计算能力，在 HTML5 中引入的工作线程使得浏览器端的 JavaScript 引擎可以并发地执行 JavaScript 代码，从而实现了对浏览器端多线程编程的良好支持。Web Worker 允许 JavaScript 脚本创建多个线程，但是子线程完全受主线程控制，且不得操作 DOM 。所以，这个新标准并没有改变 JavaScript 单线程的本质。

### 浏览器是多线程的

前端某些任务是非常耗时的，比如网络请求，定时器和事件监听，如果让他们和别的任务一样，都老老实实的排队等待执行的话，执行效率会非常的低，甚至导致页面的假死。所以浏览器是多线程的，它们一般会实现一下这几个线程：
- JavaScript 解释线程（主线程）

- UI 渲染线程

- [异步]HTTP 请求线程：在 XMLHttpRequest 在连接后是通过浏览器新开一个线程请求， 将检测到状态变更时，如果设置有回调函数，异步线程就产生状态变更事件放到 JavaScript 引擎的处理队列中等待处理

- [异步]定时触发器线程：浏览器定时计数器并不是由 JavaScript 引擎计数的, 因为 JavaScript 引擎是单线程的, 如果处于阻塞线程状态就会影响记计时的准确, 因此通过单独线程来计时并触发定时是更为合理的方案

- [异步]浏览器事件触发线程：当一个事件被触发时该线程会把事件添加到事件队列的队尾，等待 JavaScript 引擎的处理。这些事件可以是当前执行的代码块如定时任务、也可来自浏览器内核的其他线程如鼠标点击、AJAX 异步请求等，但由于 JavaScript 的单线程关系所有这些事件都得排队等待 JavaScript 引擎处理

> 由于 JavaScript 是可操纵 DOM 的，如果在修改这些元素属性同时渲染界面（即 JavaScript 线程和 UI 线程同时运行），那么渲染线程前后获得的元素数据就可能不一致了。为了防止渲染出现不可预期的结果，浏览器设置 UI 渲染线程与 JavaScript 引擎线程为互斥的关系，当 JavaScript 引擎线程执行时 UI 渲染线程会被挂起，UI 更新会被保存在一个队列中等到 JavaScript 引擎线程空闲时立即被执行。

> 主线程把异步操作交给相应的独立线程处理，独立线程获得异步结果（比如触发了点击、取到了数据、到了触发时间），就会把相应的回调函数push到**任务队列**中供主线程执行。（关键词，event-loop）