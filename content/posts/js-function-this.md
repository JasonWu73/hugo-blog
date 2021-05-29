---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- JavaScript
tags:
- this
series:
- JavaScript Docs
title: JavaScript 函数中的 this 值
date: 2021-05-29T15:25:54+08:00
description: 动态修改 JavaScript 函数中的 this 值
pinned: true  
---

# JavaScript 函数中的 this 值

JS 函数中的 `this` 可被动态修改，这为重用函数提供了极大的灵活性。

假设已有下面代码：

```:index.js
'use strict';

const jsCourse = {
  subject: 'JavaScript',
  description: '一门流行的前端编程语言',
  people: 0,

  show(like, stars) {
    console.log(`${this.subject}：${this.description}
已有${++this.people}人评价，您的评价为：${
        like ? '👍' : '👎'} ${'⭐️'.repeat(stars)}`);
  }
};

jsCourse.show(false, 3);
// JavaScript：一门流行的前端编程语言
// 已有1人评价，您的评价为：👎 ⭐️⭐️⭐️

// 该对象没有 `show` 方法，需复用 `jsCourse.show` 方法
const nodeCourse = {
  subject: 'Node.js',
  description: '后端 JS 运行环境',
  people: 0
};
```

## Function.prototype.call()

语法：

```js
call()
call(thisArg)
call(thisArg, arg1)
call(thisArg, arg1, arg2)
call(thisArg, arg1, ... , argN)
```

示例：

```:index.js
jsCourse.show.call(nodeCourse, true, 5);
// Node.js：后端 JS 运行环境
// 已有1人评价，您的评价为：👍 ⭐️⭐️⭐️⭐️⭐️
```

参考：
- [Function.prototype.call() | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call)

## Function.prototype.apply()

语法：

```js
apply(thisArg)
apply(thisArg, argsArray)
```

示例：

```:index.js
jsCourse.show.apply(nodeCourse, [true, 5]);
// Node.js：后端 JS 运行环境
// 已有1人评价，您的评价为：👍 ⭐️⭐️⭐️⭐️⭐️
```

{{<alert theme="info" dir="ltr">}}
`apply` 方法在现代 JS（从 ES 2015 开始）中很少使用，一般都使用 `call` 方法。

```index.js
jsCourse.show.call(nodeCourse, ...[true, 5]);
```
{{</alert>}}

参考：
- [Function.prototype.apply() | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
- [Spread syntax (...) | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

## Function.prototype.bind()

语法：

```js
bind(thisArg)
bind(thisArg, arg1)
bind(thisArg, arg1, arg2)
bind(thisArg, arg1, ... , argN)
```

示例：

```:index.js
const showNode = jsCourse.show.bind(nodeCourse);

showNode(true, 4);
// Node.js：后端 JS 运行环境
// 已有1人评价，您的评价为：👍 ⭐️⭐️⭐️⭐️

// Partial application
const showNodeLike = jsCourse.show.bind(nodeCourse, true);

showNodeLike(5);
// Node.js：后端 JS 运行环境
// 已有2人评价，您的评价为：👍 ⭐️⭐️⭐️⭐️⭐️
```

`bind` 方法还常用于事件监听器：

{{<codes js html>}}
  {{<code>}}
  ```:index.js
  const electronCourse = {
    subject: 'Electron',
    likes: 0,

    like() {
      console.log(this);

      console.log(`${this.subject}，点赞数 👍：${++this.likes}`);
    }
  }

  document.querySelector('.btn-like')
  .addEventListener('click', electronCourse.like.bind(electronCourse));
  // Object {subject: "Electron", likes: 0, like: ƒ}
  // Electron，点赞数 👍：1
  ```
  {{</code>}}

  {{<code>}}
  ```:index.html
  <div>
    <button class="btn-like">喜欢</button>
  </div>
  ```
  {{</code>}}
{{</codes>}}

{{<alert theme="danger" dir="ltr">}}
下面代码是**错误**的，其中 `this` 的值为 *HTML Button 元素*。

```:index.js
document.querySelector('.btn-like')
.addEventListener('click', electronCourse.like);
// <button class="btn-like">喜欢</button>
// undefined，点赞数 👍：NaN
```
{{</alert>}}

参考：
- [Function.prototype.bind() | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
- [Javascript- Currying VS Partial Application](https://towardsdatascience.com/javascript-currying-vs-partial-application-4db5b2442be8)
