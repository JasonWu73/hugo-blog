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
description: JS 中异步机制的实现方式 - Event Loop。
pinned: true
---

> {{<reprint>}}

{{< param description >}}

## 原理讲解

{{< img src="/images/posts/js_async_diagram.jpg" title="JS 异步图解" caption="异步实现机制" alt="JS 异步原理图" position="center" >}}

{{< notice info "回调队列中的回调函数" >}}
回调队列中的回调不但可以来自于异步代码，同样也可以来自同步代码，比如 DOM 事件（`click`、`keydown` 等）。
{{< /notice >}}

Event Loop
: 负责协调除全局执行上下文（Global Execution Context，即程序开始运行时）外的整个 JS 执行过程，决定每个微任务（优先级更高）或回调应该在何时执行。

Event Loop Tick
: 当 Call Stack 为空时，Event Loop 会将微任务队列或回调队列中第一个微任务或回调放入 Call Stack 中执行的过程。

JS 引擎是没有时间观念的，因为异步根本不发生在 JS 引擎中，所有的异步全部由 Event Loop 决定接下来该执行什么代码，JS 引擎（单线程）只负责执行 Event Loop 交给它的代码而已。

{{< notice warning "微任务饿死（starve）回调队列" >}}
由于微任务队列比回调队列持有更高的优先级，即微任务可以在其他常规回调中插队执行，所以当一个微任务引入另一个微任务，然后引入的微任务再引入下一个微任务，循环往复，就有可能造成回调队列中的代码一直没办法执行。
{{< /notice >}}

简而言之，浏览器 Web API 环境、回调（微任务）队列以及 Event Loop 这些一起才实现了单线程 JS 引擎的并发（concurrent）操作，即非阻塞式异步代码。

## 回调队列示例：DOM 加载图片

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

1. 程序开始运行时，即在 Call Stack 中加入全局执行上下文
2. 在 Call Stack 顶部加入执行上下文（Execution Context）用于执行 `querySelector()`，并等待执行完毕后从 Call Stack 中移除
3. 在浏览器 Web API 环境中异步加载图片 `img.src = `
4. 在 Call Stack 顶部加入执行上下文用于执行 `addEventListener()`，将回调注册到浏览器 Web API 环境中（回调函数会一直驻留在这里，直到图片加载完毕），然后从 Call Stack 中移除
5. 图片加载完毕，浏览器 Web API 将回调放入回调队列的队尾
6. Event Loop 检查 Call Stack 是否为空，若为空，则先检查微任务队列中是否存在微任务，若不存在，则从回调队列中取第一个回调放入 Call Stack
7. JS 引擎执行回调

## 微任务队列示例：Fetch API

```js
fetch('https://jsonplaceholder.typicode.com/posts/1')
  .then(response => console.log(response));
```

以上代码在浏览器运行环境下的执行过程：

1. 程序开始运行时，即在 Call Stack 中加入全局执行上下文
2. 在 Call Stack 顶部加入执行上下文用于执行 `fetch()`，在浏览器 Web API 环境中执行异步 AJAX 请求，返回 Promise 占位符对象，然后从 Call Stack 中移除
3. 在 Call Stack 顶部加入执行上下文用于执行 `then()`，将回调注册到浏览器 Web API 环境中，然后从 Call Stack 中移除
4. AJAX 请求完毕，浏览器 Web API 将微任务放入微任务队列的队尾
5. Event Loop 检查 Call Stack 是否为空，若为空，则从微任务队列中取第一个微任务放入 Call Stack
6. JS 引擎执行微任务

## 利用代码证明以上结论

需证明以下几点结论：

- `setTimeout` 是异步行为，其回调是被放入回调队列中
- Promise 是异步行为，其回调是被放入微任务队列中
- 微任务队列的优先级要高于回调队列

```js
// 先注册回调队列
setTimeout(() => console.log('0秒定时器'), 0);

// 再注册微任务队列
Promise.resolve('2秒 Promise').then(data => {
  // 假设在 Call Stack 中正在运行一段耗时代码
  const start = Date.now();

  while (Date.now() - start <= 2000) {
    // 模拟2秒耗时操作
  }

  console.log(data);
});

console.log('开始执行耗时同步代码')

// 假设在 Call Stack 中正在运行一段耗时代码
const start = Date.now();

while (Date.now() - start <= 3000) {
  // 模拟3秒耗时操作
}

console.log('结束耗时同步代码')

// 开始执行耗时同步代码
// 结束耗时同步代码
// 2秒 Promise
// 0秒定时器
```
