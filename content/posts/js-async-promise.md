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
description: 通过 Promise 优化旧的 JS 异步 API。
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

## 构建 Promise

Promise 是一个特殊的对象，接收一个执行器（executor）函数，其中执行器函数包含两个参数：

1. `resolve`：代表处理成功时的回调函数
2. `reject`：代表处理失败时的回调函数

比如：

```js
const lotteryPromise = new Promise((resolve, reject) => {
  console.log('彩票开奖中...');

  setTimeout(() => {
    if (Math.random() >= 0.5) {
      resolve('恭喜，您中奖啦！！！');
    } else {
      reject(new Error('很遗憾，您未中奖'));
    }
  }, 2000)
});

lotteryPromise
  .then(result => console.log(result))
  .catch(err => console.error(err));
```

此外，还可以使用 `Promise.resolve` 或 `Promise.reject` 来构建立即执行的 Promise：

```js
Promise.resolve('结果').then(result => console.log(result));

Promise.reject(new Error('错误')).then(err => console.error(err));
```

## Promisifying

Promisifying
: 将基于回调的异步代码转换为基于 Promise 的行为。

### setTimeout

对 `setTimeout` 执行 Promisifying：

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

对 Geolocation API 执行 Promisifying：

```js
navigator.geolocation.getCurrentPosition(
  position => console.log(position),
  err => console.error(err));

// Promisifying
const geoPromise = new Promise((resolve, reject) => {
  navigator.geolocation.getCurrentPosition(resolve, reject);
});

geoPromise
  .then(position => console.log(position))
  .catch(err => console.error(err));

console.log('获取位置');
```

### DOM 加载图片

对 DOM 加载图片执行 Promisifying：

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
  .catch(err => console.error(err));

console.log('DOM 开始加载图片');
```

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
