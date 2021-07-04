---
toc: true
categories:
  - JavaScript
tags:
  - Async
  - Promise
title: JavaScript 使用 Promise：Async/Await
date: 2021-05-31
description: ES2017 async/await 及 Promise Combinator
---

通过 ES2017 `async`/`await` 及 Promise Combinator 实现优雅的异步代码。

## 以同步写异步

语法糖：`async`/`await` 并没有为 JS 语言添加新特性，仅仅是为 Promise 提供了一种更易写易懂的语法。

```js
const wait = sec =>
  new Promise(resolve => setTimeout(resolve, sec * 1000));

// 通过 async/await 使用 Promise
(async () => {
  await wait(1);
  console.log('1秒后执行');

  await wait(1);
  console.log('2秒后执行');

  await wait(1);
  console.log('3秒后执行');

  await wait(1);
  console.log('4秒后执行');
})();

// Promise 原使用方式
// wait(1)
//   .then(() => {
//     console.log('1秒后执行');
//
//     return wait(1);
//   })
//   .then(() => {
//     console.log('2秒后执行');
//
//     return wait(1);
//   })
//   .then(() => {
//     console.log('3秒后执行');
//
//     return wait(1);
//   })
//   .then(() => {
//     console.log('4秒后执行');
//
//     return wait(1);
//   });
```

<!--more-->

## 代码求证语法糖

```js
const asyncFunc = async () => '语法糖';

console.log(asyncFunc()); // Promise {<fulfilled>: "语法糖"}
```

## 错误处理

`tray...catch...finally`

```js
(async () => {
  try {
    const postUrl = 'https://jsonplaceholder.typicode.com/posts/101';
    const postRes = await fetch(postUrl);

    if (!postRes.ok) {
      throw new Error(`服务出错（${postRes.status}）`);
    }

    const post = await postRes.json();

    console.log(post);

  } catch (err) {
    console.log(err.message);
  }
})();
```

## 返回值

### async 调用 async（推荐）

```js
const getPost = async () => {
  try {
    const postUrl = 'https://jsonplaceholder.typicode.com/posts/1';
    const postRes = await fetch(postUrl);

    return postRes.json();

  } catch (err) {
    // 当作为返回值使用时，需要再抛出错误，以供外部处理
    throw err;
  }
};

console.log('1：开始查询');

(async () => {
  try {
    const post = await getPost();
    console.log(`2：获取文章${post.id}`);

    console.log('3：结束查询');

  } catch (err) {
    console.log(err);
  }
})();

// 1：开始查询
// 2：获取文章1
// 3：结束查询
```

### Promise 回调

```js
const getPost = async () => {
  try {
    const postUrl = 'https://jsonplaceholder.typicode.com/posts/1';
    const postRes = await fetch(postUrl);

    return postRes.json();

  } catch (err) {
    // 当作为返回值使用时，需要再抛出错误，以供外部处理
    throw err;
  }
};

console.log('1：开始查询');

getPost()
  .then(post => console.log(`2：获取文章${post.id}`))
  .finally(() => console.log('3：结束查询'));

// 1：开始查询
// 2：获取文章1
// 3：结束查询
```

## Promise Combinator

组合多个 Promise 共同执行。

### Promise.all()

```js
const getPost = async (postId) => {
  const baseUrl = 'https://jsonplaceholder.typicode.com';
  const postUrl = `${baseUrl}/posts/${postId}`;
  const postRes = await fetch(postUrl);
  return await postRes.json();
};

/*
(async () => {
  // 按顺序阻塞执行 AJAX 请求，糟糕的代码！！！
  const post1 = await getPost(1);
  const post2 = await getPost(2);
  const post3 = await getPost(3);

  console.log([post1.id, post2.id, post3.id]);
})();
*/

(async () => {
  // 并行执行 AJAX 请求
  const posts = await Promise.all([
    getPost(1),
    getPost(2),
    getPost(3)
  ]);

  const postIds = posts.map(post => post.id);
  console.log(postIds);
})();
```

**Short Circuit**：只要有一个 Rejected Promise，就会导致 `Promise.all()` 结果错误。

```js
(async () => {
  const proAll = await Promise.all([
    Promise.resolve('成功'),
    Promise.reject('失败')
  ]);

  console.log(proAll); // Uncaught (in promise) 失败
})();
```

### Promise.race()

约定俗成：以 `_` 作为被忽略的变量名称，即表示在上下文中不需要的变量。

> 此外，`_` 也是 [Lodash](https://lodash.com/) 的默认命名空间。

```js
const getPost = async (postId) => {
  const baseUrl = 'https://jsonplaceholder.typicode.com';
  const postUrl = `${baseUrl}/posts/${postId}`;
  const postRes = await fetch(postUrl);
  return await postRes.json();
};

const timeout = sec => new Promise((_, reject) => {
  // 约定俗成：以 `_` 表示被忽略的变量
  setTimeout(() => reject(new Error(`请求超时${sec}秒`)), sec * 1000);
});

(async () => {
  const post = await Promise.race([
    getPost(1),
    timeout(0.18)
  ]);

  console.log(post.id);
  // 1
  // 或
  // Uncaught (in promise) Error: 请求超时0.18秒
})();
```

**Short Circuit**：只要有一个 Settled Promise，就会使 `Promise.race()` 返回结果。

```js
(async () => {
  const proRace = await Promise.race([
    Promise.resolve('成功1'),
    Promise.reject('失败1')
  ]);

  console.log(proRace); // 成功1
})();

(async () => {
  const proRace = await Promise.race([
    Promise.reject('失败2'),
    Promise.resolve('成功2')
  ]);

  console.log(proRace); // Uncaught (in promise) 失败2
})();
```

### Promise.allSettled()

**No Short Circuit**：ES2020 引入，与 `Promise.all()` 类似，除了 `Promise.allSettled()` 一定会保证所有 Promise 都为 Settled 状态。

```js
(async () => {
  const proAll = await Promise.allSettled([
    Promise.resolve('成功'),
    Promise.reject('失败')
  ]);

  console.log(proAll);
  // [{...}, {...}]
})();
```

### Promise.any()

**Short Circuit**：ES2021 引入，与 `Promise.race()` 类似，除了 `Promise.any()` 只返回第一个 Fulfilled Promise，而忽略 Rejected Promise。

```js
(async () => {
  const proAny = await Promise.any([
    new Promise(resolve => {
      console.log('执行：成功1');
      resolve('成功1');
    }),
    new Promise((_, reject) => {
      console.log('执行：失败1-1');
      reject('失败1-1');
    }),
    new Promise(resolve => {
      console.log('执行：成功1-1');
      resolve('成功1-1');
    }),
  ]);

  // 执行：成功1
  // 执行：失败1-1
  // 执行：成功1-1

  console.log(proAny); // 成功1
})();

(async () => {
  const proAny = await Promise.any([
    Promise.reject('失败2'),
    Promise.resolve('成功2'),
    Promise.resolve('成功2-1')
  ]);

  console.log(proAny); // 成功2
})();
```
