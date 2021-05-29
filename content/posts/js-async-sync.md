---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- JavaScript
tags:
- async
- sync
series:
- 异步 JavaScript
title: JavaScript 中的异步和同步代码
date: 2021-05-29T21:08:21+08:00
description: 理解 JS 中的异步和同步代码。
---

> {{<reprint>}}

{{< param description >}}

## 同步代码

- 代码按顺序一行一行执行
- **阻塞式**，即当执行耗时操作时，同步代码只有在该耗时操作完成后才能继续执行

比如同步函数 `alert`：

{{< codes index.js index.html >}}
  {{< code >}}
  ```:index.js
  const p = document.querySelector('.paragraph');
  p.textContent = 'JS 修改后内容';
  alert('阻塞')
  p.style.color = 'red';
  ```
  {{< /code >}}

  {{< code >}}
  ```:index.html
  <p class="paragraph">初始内容</p>

  <script src="index.js"></script>
  ```
{{< /code >}}
{{< /codes >}}

## 异步代码

- 只会在"后台"任务执行完毕后执行
- **非阻塞式**，即后续代码不必等待异步代码执行完毕后再执行
- 虽然异步是通过回调函数实现的，但单独的回调函数不会自动变为异步执行。比如 `[1, 2, 3].map(v => v * 2)` 仍然是同步代码

比如异步函数 `setTimeout`：

{{< codes index.js index.html >}}
{{< code >}}
  ```:index.js
  const p = document.querySelector('.paragraph');
  setTimeout(() => {
    p.textContent = 'JS 修改后内容';
  }, 3000);
  p.style.color = 'red';
  ```
{{< /code >}}

{{< code >}}
  ```:index.html
  <p class="paragraph">初始内容</p>

  <script src="index.js"></script>
  ```
{{< /code >}}
{{< /codes >}}

再比如加载图片也属于异步操作：

{{< codes index.js index.html >}}
{{< code >}}
  ```:index.js
  const img = document.querySelector('.logo');
  const p = document.querySelector('.paragraph');

  // 加载图片是一个异步操作
  img.src = 'logo.png';

  img.addEventListener('load', () => {
    // 在图片加载完成后触发
    p.textContent = '图片加载完成';
  });

  p.style.color = 'red';
  ```
{{< /code >}}

{{< code >}}
  ```:index.html
  <img class="logo">
  <p class="paragraph">未加载图片</p>

  <script src="index.js"></script>
  ```
{{< /code >}}
{{< /codes >}}

{{< notice warning "单独的事件监听和回调一样并不能形成异步操作" >}}
比如监听按钮的 `click` 事件就**不是**一个异步操作！上面之所以是异步，完全是因为加载图片（`img.src = 'logo.png'`）的动作是异步的原因。
{{< /notice >}}

### JS 中常见的异步操作

- 执行定时器
- 加载图片
- 定位 API（Geolocation API）
- AJAX（**A**synchronous **J**avaScript **A**nd **X**ML）
