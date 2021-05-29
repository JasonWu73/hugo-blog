---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- JavaScript
tags:
- async
- xhr
series:
- 异步 JavaScript
title: JavaScript AJAX 调用：XMLHttpRequest
date: 2021-05-30T04:25:52+08:00
description: JS 早期 AJAX 调用方式 XMLHttpRequest。
---

> {{<reprint>}}

{{< param description >}}

## GET 请求

```js
const baseUrl = 'https://jsonplaceholder.typicode.com';

const request = new XMLHttpRequest();
request.open('GET', `${baseUrl}/posts/1`);
request.send();

request.addEventListener('load', function () {
  const data = JSON.parse(this.responseText);
  console.log(data);
});
```

## POST 请求

```js
const baseUrl = 'https://jsonplaceholder.typicode.com';

const request = new XMLHttpRequest();
request.open('POST', `${baseUrl}/posts`);

request.setRequestHeader('Content-Type',
    'application/json; charset=UTF-8');

request.send(JSON.stringify({
  title: '测试标题',
  body: '测试内容',
  userId: 101,
}));

request.addEventListener('load', function () {
  const data = JSON.parse(this.responseText);
  console.log(data);
})
```
