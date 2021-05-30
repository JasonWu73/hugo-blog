---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- JavaScript
tags:
- async
- promise
series:
- 异步 JavaScript
title: JavaScript 构建 Promise
date: 2021-05-30T04:36:08+08:00
description: JS 可通过 Promise 优雅地规避回调地狱（callback hell）。
---

> {{<reprint>}}

{{< param description >}}

## 何为 Promise

Promise
: 一个包含异步操作结果的占位符对象。

Promise 的优点：

- 无需依赖将事件和回调传递给异步函数，就可以处理异步结果
- 可为一系列异步操作提供 Promise 链式调用，从而避免回调地狱（callback hell）

Promise 生命周期：

1. Pending：构建 Promise 对象时的初始状态，此时异步任务会立即在后台运行
    - `new Promise()`
2. Settled：异步任务执行完毕
    - Fulfilled：异步任务执行成功，且结果可用
        - `then()`
    - Rejected：异步任务执行失败，即发生了错误
        - `then()` 或 `catch()`

## Promise 不能将同步转为异步

Promise 本身是同步的，它不能将同步代码转为异步代码，它的作用仅仅是包装异步代码，从而使得异步代码变得更易用且更优雅而已。

比如下面的 Promise 同样会阻塞代码运行：

```js
// Promise 不能也不会将同步代码转换为异步代码
new Promise(resolve => {
   // 假设正在运行一段耗时代码
   const start = Date.now();

   while (Date.now() - start <= 2000) {
      // 模拟2秒耗时操作
   }

   resolve('2秒 Promise');
})
        .then(data => console.log(data));

// 该代码会被阻塞2秒
console.log('同步代码');

// 同步代码
// 2秒 Promise
```
