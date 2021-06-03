---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- JavaScript
tags:
- async
- promise
series:
- JavaScript Async
title: JavaScript 构建 Promise
date: 2021-05-30T19:11:33+08:00
description: 通过 Promise 优化旧的 JS 异步 API。
---

> {{<reprint>}}

{{< param description >}}

## 介绍 Promise

Promise
: 一个包含异步操作结果（将来结果）的占位符对象。

<br>Promise 的优点：

- 无需依赖将事件和回调传递给异步函数，就可以处理异步结果
- 可为一系列异步操作提供 Promise 链式调用，从而避免回调地狱（callback hell）

<br>Promise 生命周期：

1. Pending：构建 Promise 对象时的初始状态，内部还是立即执行的同步代码
    - `new Promise()`
2. Settled：异步任务执行完毕
    - Fulfilled：异步任务执行成功，且结果可用
        - `then()`
    - Rejected：异步任务执行失败，即发生了错误
        - `then(onFulfilled[, onRejected])` 或 `catch()`

## 构建 Promise

Promise 是一个特殊的对象，接收一个执行器（executor）函数，其中执行器函数包含两个参数：

1. `resolve`：代表处理成功时的回调函数
2. `reject`：代表处理失败时的回调函数

```js
const lotteryPro = new Promise((resolve, reject) => {
   if (Math.random() >= 0.5) {
      resolve('恭喜，您中奖啦！！！');
   } else {
      reject(new Error('很遗憾，您未中奖'));
   }
});

lotteryPro
        .then(res => console.log(res))
        .catch(err => console.error(err.message));
```

此外，还可以使用 `Promise.resolve` 或 `Promise.reject` 来构建立即执行的 Promise。

```js
Promise.resolve('结果').then(res => console.log(res));

Promise.reject(new Error('错误'))
  .catch(err => console.error(err.message));
```

## Promise ≠ 异步

{{< notice warning "Promise 不等于异步" >}}
Promise 不会也不能将同步代码转为异步代码，它的作用仅仅是包装异步代码，从而使得异步编码方式更加优雅，并使异步代码支持 [Async/Await 关键字]({{< relref "/posts/js-async-vs-sync" >}})。
{{< /notice >}}

```js
const startMilli = Date.now();
console.log(`1：开始执行`);

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
const duration = Math.trunc((Date.now() - startMilli) / 1000);
console.log(`2：结束执行，耗时：${duration}秒`);

// 1：开始执行
// 2：结束执行，耗时：2秒
// 2秒 Promise
```

## Promisifying

Promisifying
: 将基于回调的异步代码转换为基于 Promise 的异步代码。

### setTimeout

```js
// 因为 `setTimeout` 不会返回错误，故只需要 `resolve` 即可
const wait = seconds =>
  new Promise(resolve => setTimeout(resolve, seconds * 1000));

// 使用 Promisifying `setTimeout`
wait(1)
  .then(() => {
    console.log('1秒后执行');

    return wait(1);
  })
  .then(() => {
    console.log('2秒后执行');

    return wait(1);
  })
  .then(() => {
    console.log('3秒后执行');

    return wait(1);
  })
  .then(() => {
    console.log('4秒后执行');

    return wait(1);
  });
```

{{< alert theme="warning" dir="ltr" >}}
欢迎来到回调地狱（callback hell）：

```js
setTimeout(() => {
   console.log('1秒后执行');

   setTimeout(() => {
      console.log('2秒后执行');

      setTimeout(() => {
         console.log('3秒后执行');

         setTimeout(() => {
            console.log('4秒后执行');
         }, 1000);
      }, 1000);
   }, 1000);
}, 1000);
```
{{< /alert >}}

### Geolocation

```js
navigator.geolocation.getCurrentPosition(
  pos => console.log(pos),
  err => console.error(err.message)
);

// Promisifying
const geoPro = new Promise((resolve, reject) => {
   navigator.geolocation.getCurrentPosition(resolve, reject);
});

geoPro
  .then(pos => console.log(pos))
  .catch(err => console.error(err.message));

console.log('获取位置');
```

### DOM 加载图片

```js
const body = document.querySelector('body');

(() => {
  // DOM 加载图片是一个异步操作
  const img = document.createElement('img');
  img.src = 'qgs.png';

  img.addEventListener('load', () => {
    body.insertAdjacentElement('afterbegin', img);
  });
})();

// Promisifying
const createImage = imgPath =>
  new Promise((resolve, reject) => {
    const img = document.createElement('img');
    img.src = imgPath;

    img.addEventListener('load', () => {
      resolve(img);
    });

    img.addEventListener('error', () => {
      reject(new Error('未找到图片'));
    });
  });

createImage('j.png')
  .then(img => {
    body.insertAdjacentElement('afterbegin', img);
  })
  .catch(err => console.error(err.message));

console.log('DOM 开始加载图片');
```
