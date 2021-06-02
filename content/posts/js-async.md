---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- JavaScript
tags:
- async
series:
- 异步 JavaScript
title: JavaScript 异步原理：Event Loop
date: 2021-05-30T19:19:49+08:00
description: 详解 JS 异步机制实现。
pinned: true
---

> {{<reprint>}}

{{< param description >}}

## 原理

{{< img src="/images/posts/js_async_runtime.jpg" title="JS 异步机制" caption="JS 运行时环境" alt="JS 运行时环境" position="center" >}}

{{< notice info "回调队列 vs 微任务队列" >}}
微任务队列和回调队列唯一的不同点在于执行优先级，其中微任务队列拥有更高的优先级，故回调队列可能会出现 starve。
因为微任务队列和回调队列的代码都是来自于回调函数，故下文在非必要情况下，全部以回调队列作为讲解。
{{< /notice >}}

<br>Event Loop
: 负责协调除程序开始运行时，即全局执行上下文（Global Execution Context）以外的整个 JS 执行过程，并决定每个回调应该在何时被执行。

<br>Event Loop Tick
: 当 Call Stack 为空，Event Loop 将回调队列的第一个回调放入 Call Stack 执行。

<br>
{{< notice info "回调队列" >}}
回调队列中的回调函数既可以来自异步代码，也可以来自同步代码（比如 DOM 事件：`click`、`keydown` 等）。
{{< /notice >}}

<br>JS 引擎对时间是无感知的，因为异步根本不发生在 JS 引擎中，什么时间执行什么代码全部都由 Event Loop 决定，JS 引擎（单线程）只负责执行 Event Loop 交给它的代码而已。

简而言之，浏览器的 Web API 环境、回调队列以及 Event Loop 这些一起才实现了单线程 JS 引擎的非阻塞机制。

## 示例：DOM 加载图片

```js
const img = document.querySelector('.logo');

// DOM 加载图片是一个异步操作
img.src = 'logo.png';

img.addEventListener('load', () => {
  // 在图片加载完成后触发
  p.textContent = '图片加载完成';
});
```

以上代码在浏览器运行环境下的执行过程：

1. 程序开始运行时，Call Stack 中加入全局执行上下文
2. 在 Call Stack 顶部加入执行上下文 `querySelector()`，结束并移除
3. 在 Call Stack 顶部加入执行上下文 `img.src`（将加载图片的行为交给 Web API），结束并移除
4. 在 Call Stack 顶部加入执行上下文 `addEventListener()`（将回调函数注册到 Web API），结束并移除
5. 图片加载完成，浏览器 Web API 将回调放入回调队列的尾部
6. Event Loop 发现 Call Stack 为空，检查微任务队列也为空，然后从回调队列中取第一个回调放入 Call Stack
7. JS 引擎执行回调

## 代码求证

需证明以下几个结论：

- `setTimeout` 是异步行为，其回调函数是被放入回调队列
- Promise 是异步行为，其回调函数是被放入微任务队列
- 微任务队列的优先级要高于回调队列

```js
// 先注册回调队列
setTimeout(() => console.log('0秒定时器'), 0);

// 再注册微任务队列
Promise.resolve('3秒 Promise').then(msg => {
  // 假设在 Call Stack 中运行一段耗时代码
  const start = Date.now();

  while (Date.now() - start <= 3000) {
    // 模拟3秒耗时操作
  }

  console.log(msg);
});

console.log('1：开始执行耗时同步代码')

// 假设在 Call Stack 中正在运行一段耗时代码
const start = Date.now();

while (Date.now() - start <= 1000) {
  // 模拟1秒耗时操作
}

console.log('2：结束执行耗时同步代码')

// 1：开始执行耗时同步代码
// 2：结束执行耗时同步代码
// 3秒 Promise
// 0秒定时器
```
