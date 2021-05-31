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
title: JavaScript AJAX（使用 Promise）：Fetch API
date: 2021-05-30T19:11:33+08:00
description: 通过现代 Fetch API 发送 AJAX 请求，并说明 Promise 的使用方式。
---

> {{<reprint>}}

{{< param description >}}

## fetch 会立即触发 AJAX 请求

调用 `fetch` 函数时就会在后台**立即**执行 AJAX 请求。比如：

```js
const baseUrl = 'https://jsonplaceholder.typicode.com';

const p = fetch(`${baseUrl}/posts/1`); // 立即执行 AJAX，并返回 Promise

console.log(p); // Promise {<pending>}
```

{{< notice info "异步行为的执行与 then 和 catch 无关" >}}
`then` 和 `catch` 仅仅代表是否使用异步结果，但不会影响异步行为的执行。
{{< /notice >}}

## then、catch 及 finally 都只返回 Promise

`then`、`catch` 和 `finally` 函数的返回值都会被自动包装成 Promise，从而支持持续的 Promise 链式调用。比如：

```js
const baseUrl = 'https://jsonplaceholder.typicode.com';

fetch(`${baseUrl}/posts/1`)
  .finally(() => {
    console.log('不考虑结果正确与否的代码块1');
  })
  .then(() => {
    return '字符串';
  })
  .then(data => {
    console.log(data);

    throw Error('错误');
  })
  .catch(err => {
    console.error(err)

    return '错误已处理';
  })
  .then(data => {
    console.log(data);
  })
  .finally(() => {
    console.log('不考虑结果正确与否的代码块2');
  });

// 不考虑结果正确与否的代码块1
// 字符串
/*
Error: 错误
    at index.js:13
*/
// 错误已处理
// 不考虑结果正确与否的代码块2
```

{{< notice info "then、catch 和 finally 是顺序相关的" >}}
`then`、`catch` 和 `finally` 都代表异步操作执行完毕，从而对结果进行处理：

- `then` 处理异步操作行成功后的结果
- `catch` 处理异步操作执行失败后的结果
- `finally` 则不考虑异步操作成功还是失败，它仅仅考虑在异步操作经执行完毕后，需要处理的事情

因 `then`、`catch` 和 `finally` 三者具有相同优先级，故代码编写顺序就显得非常重要了。一般三者在源代码中的顺序为先 `then`，然后 `catch`，最后 `finally`。
{{< /notice >}}

## response.json() 返回 Promise

在 Fetch API 中，`response.json()` 被设计成返回 Promise。比如：

```js
const baseUrl = 'https://jsonplaceholder.typicode.com';

fetch(`${baseUrl}/posts/1`)
  .then(response => {
    const data = response.json();

    console.log(data); // Promise {<pending>}

    return data;
  })
  .then(data => console.log(data));
```

## Fetch API 错误机制

{{< notice success "HTTP 服务返回错误信息（HTTP 状态码非2xx）不会导致 Fetch API 出错" >}}
Fetch API 将 HTTP 请求失败也作为 AJAX 请求已经被正确执行来处理，这对请求 RESTful API 是方便的。
因为在标准的 RESTful API 中，我们不需要关心 HTTP 状态码（仅用于标识请求状态），而只要关心响应体内容（只要请求有到达服务，不论成功与否都会在响应体中体现）。
{{< /notice >}}

只有一个原因会导致 Fetch API 出错，即丢失网络连接。比如：

```js
const baseUrl = 'https://jsonplaceholder.typicode.com';

fetch(`${baseUrl}/posts/1`)
  .then(response => response.json())
  .then(post => console.log(post))
  .catch(err => console.error(err.message));

// Failed to fetch
```

## Fetch API 正确编码方式

Fetch API 的正确写法：

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
  .then(response => response.json())
  .then(post => {
    console.log(post);

    return fetch(`${baseUrl}/posts/${post.id}`)
  })
  .then(response => {
    // 因 Fetch API 认为 HTTP 请求失败不意味着出错，故需手动抛出错误
    if (!response.ok)
      throw new Error(`服务返回错误信息（${response.status}）`);

    return response.json();
  })
  .then(data => console.log(data))
  .catch(err => console.error(err.message))
```

{{< alert theme="danger" dir="ltr" >}}
虽然 Promise 可以很优雅地处理异步问题，避免回调地狱（callback hell）。但如果使用方式不当，则同样会出现回调地狱。比如：

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
  .then(response => response.json())
  .then(post => {
    console.log(post);

    // 错误的使用方式，出现回调地狱
    fetch(`${baseUrl}/posts/${post.id}`)
      .then(response => {
        // 因 Fetch API 认为 HTTP 请求失败不意味着出错，故需手动抛出错误
        if (!response.ok)
          throw new Error(`服务返回错误信息（${response.status}）`);

        return response.json();
      })
      .then(data => console.log(data))
      .catch(err => console.error(err.message))
  })
  .catch(err => console.error(err.message))
```
{{< /alert >}}
