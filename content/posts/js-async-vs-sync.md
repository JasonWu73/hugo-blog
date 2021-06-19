---
toc: true
categories:
  - JavaScript
tags:
  - Async
title: JavaScript 异步 vs 同步
date: 2021-05-29
description: JS 中异步和同步的直观对比
---

## 同步代码

- 代码按顺序一行一行执行
- 当执行耗时操作时，后续代码只能等待当前代码执行完成后才能执行

比如 `alert` 函数就是同步代码。

```js
const p = document.querySelector('.paragraph');
p.textContent = 'JS 修改后内容';
alert('阻塞')
p.style.color = 'red';
```

```html
<p class="paragraph">初始内容</p>

<script src="index.js"></script>
```

<!--more-->

## 异步代码

- 只会在“后台”任务执行完成后执行
- **非阻塞式**，即后续代码不会等待异步代码执行完成后再执行。实际的效果就是，仿佛直接跳过了异步代码

比如 `setTimeout` 函数就是异步代码。

  ```js
  const p = document.querySelector('.paragraph');
  setTimeout(() => {
    p.textContent = 'JS 修改后内容';
  }, 3000);
  p.style.color = 'red';
  ```

```html
<p class="paragraph">初始内容</p>

<script src="index.js"></script>
```

再比如 DOM 加载图片也属于异步操作。

```js
const img = document.querySelector('.logo');
const p = document.querySelector('.paragraph');

// DOM 加载图片是一个异步操作
img.src = 'logo.png';

img.addEventListener('load', () => {
  // 在图片加载完成后触发
  p.textContent = '图片加载完成';
});

p.style.color = 'red';
```

```html
<img class="logo">
<p class="paragraph">未加载图片</p>

<script src="index.js"></script>
```

### JS 中常见的异步操作

- 定时器（`setTimeout`、`setInterval`）
- DOM 加载图片
- 定位 API（Geolocation API）
- AJAX（**A**synchronous **J**avaScript **A**nd **X**ML）
