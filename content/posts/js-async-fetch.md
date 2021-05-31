---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- JavaScript
tags:
- async
- xhr
- promise
series:
- 异步 JavaScript
title: JavaScript AJAX：Fetch API
date: 2021-05-30T04:36:08+08:00
description: 通过现代 Fetch API 发送 AJAX 请求。
---

> {{<reprint>}}

{{< param description >}}

## fetch 会立即触发 AJAX 请求

调用 `fetch` 函数时就会在后台**立即**执行 AJAX 请求。比如：

```js
const baseUrl = 'https://jsonplaceholder.typicode.com';

// 立即执行 AJAX，并返回 Promise
const postPro = fetch(`${baseUrl}/posts/1`);

console.log(postPro); // Promise {<pending>}
```

{{< notice info "AJAX 的执行与 then、catch 和 finally 无关" >}}
`then`、`catch` 和 `finally` 仅仅代表在 AJAX 请求结束后，应该执行什么的操作，而 AJAX 在执行 `fetch` 函数时就已经开始。
{{< /notice >}}

## 自动装箱 Promise

{{< notice info "自动装箱" >}}
`then`、`catch` 和 `finally` 函数的返回值全部都会被自动包装成 Promise 对象。
{{< /notice >}}

```js
const baseUrl = 'https://jsonplaceholder.typicode.com';

fetch(`${baseUrl}/posts/1`)
  .finally(() => {
    console.log('不考虑结果正确与否的代码块1');
  })
  .then(() => {
    return '字符串';
  })
  .then(str => {
    console.log(str);

    throw Error('错误');
  })
  .catch(err => {
    console.log(err.message)

    return '错误已处理';
  })
  .then(str => {
    console.log(str);
  })
  .finally(() => {
    console.log('不考虑结果正确与否的代码块2');
  });

// 不考虑结果正确与否的代码块1
// 字符串
// 错误
// 错误已处理
// 不考虑结果正确与否的代码块2
```

{{< notice warning "then、catch 和 finally 是顺序相关的" >}}
`then`、`catch` 和 `finally` 都代表异步操作执行完成后的操作：

- `then` 处理异步操作行成功后的结果
- `catch` 处理异步操作执行失败后的结果
- `finally` 则不考虑异步操作成功还是失败，仅仅关心在异步操作执行完毕后，需要处理的事情

因 `then`、`catch` 和 `finally` 三者具有相同优先级，故代码编写顺序就显得非常重要了。一般三者在源代码中的顺序为先 `then`，再 `catch`，最后 `finally`。
{{< /notice >}}

## response.json() 返回 Promise

在 Fetch API 中，`response.json()` 被设计成返回 Promise。比如：

```js
const baseUrl = 'https://jsonplaceholder.typicode.com';

fetch(`${baseUrl}/posts/1`)
  .then(res => {
    const postPro = res.json();

    console.log(postPro); // Promise {<pending>}

    return postPro;
  })
  .then(post => console.log(post.id)); // 1
```

## Fetch API 错误机制

{{< notice success "API 服务出错（HTTP 状态码非2xx）不会导致 Fetch API 出错" >}}
Fetch API 中的 Settled Promise：
- 只要 HTTP 请求能够发送且响应，那么就是 Fulfilled Promise
- 只有当丢失网络连接时，才是 Rejected Promise
{{< /notice >}}

## Fetch API 使用示例

```js
const baseUrl = 'https://jsonplaceholder.typicode.com';

fetch(`${baseUrl}/posts/`, {
  method: 'POST',
  headers: {
    'Content-type': 'application/json; charset=UTF-8'
  },
  body: JSON.stringify({
    title: '测试标题',
    body: '测试内容',
    userId: 101
  })
})
  .then(res => {
    if (!res.ok)
      throw new Error(`服务出错（${res.status}）`);

    return res.json();
  })
  .then(post => {
    console.log(post);

    return fetch(`${baseUrl}/posts/${post.id}`)
  })
  .then(res => {
    if (!res.ok)
      throw new Error(`服务出错（${res.status}）`);

    return res.json();
  })
  .then(data => console.log(data))
  .catch(err => console.error(err.message));
```

{{< alert theme="danger" dir="ltr" >}}
虽然 Promise 可以很优雅地处理异步回调问题，避免回调地狱（callback hell）。但如果使用方式不当，则同样会出现回调地狱。比如：

```js
const baseUrl = 'https://jsonplaceholder.typicode.com';

fetch(`${baseUrl}/posts/`, {
  method: 'POST',
  headers: {
    'Content-type': 'application/json; charset=UTF-8'
  },
  body: JSON.stringify({
    title: '测试标题',
    body: '测试内容',
    userId: 101
  })
})
  .then(res => {
    if (!res.ok)
      throw new Error(`服务出错（${res.status}）`);

    return res.json();
  })
  .then(post => {
    console.log(post);

    fetch(`${baseUrl}/posts/${post.id}`)
      .then(res => {
        if (!res.ok)
          throw new Error(`服务出错（${res.status}）`);

        return res.json();
      })
      .then(data => console.log(data))
      .catch(err => console.error(err.message));
  })
  .then(data => console.log(data))
  .catch(err => console.error(err.message));
```
{{< /alert >}}
