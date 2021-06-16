---
toc: true
categories:
  - "JavaScript"
tags:
  - "this"
series:
  - "JavaScript Docs"
title: "JavaScript 函数中的 this 值"
date: "2021-05-29"
description: "动态修改 JS 函数中的 this 值"
---

## 通用代码片段

```js
const jsCourse = {
  subject: 'JavaScript',
  description: '一门流行的前端编程语言',
  people: 0,

  score(like, stars) {
    console.log(`${this.subject}：${this.description}
已有${++this.people}人评价，\
您的评价为：${like ? '👍' : '👎'} ${'⭐️'.repeat(stars)}`);
  }
};

jsCourse.score(false, 3);
// JavaScript：一门流行的前端编程语言
// 已有1人评价，您的评价为：👎 ⭐️⭐️⭐️

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
call(thisArg, arg1, ..., argN)
```

示例：

```js
// 将 `jsCourse.score` 作为 `nodeCourse` 的方法使用
jsCourse.score.call(nodeCourse, true, 5);
// Node.js：后端 JS 运行环境
// 已有1人评价，您的评价为：👍 ⭐️⭐️⭐️⭐️⭐️
```

## Function.prototype.apply()

语法：

```js
apply(thisArg)
apply(thisArg, argsArray)
```

示例：

```js
// 将 `jsCourse.score` 作为 `nodeCourse` 的方法使用
jsCourse.score.apply(nodeCourse, [true, 5]);
// Node.js：后端 JS 运行环境
// 已有1人评价，您的评价为：👍 ⭐️⭐️⭐️⭐️⭐️
```

`apply` 和 [`call`]({{< relref "#functionprototypecall" >}}) 除了第二个参数不同外，其他没有任何区别。也可通过 [Spread 语法](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)使用 `call` 代替：

```js
jsCourse.score.call(nodeCourse, ...[true, 5]);
```

## Function.prototype.bind()

语法：

```js
bind(thisArg)
bind(thisArg, arg1, ..., argN)
```

示例：

```js
const scoreNode = jsCourse.score.bind(nodeCourse);

scoreNode(true, 4);
// Node.js：后端 JS 运行环境
// 已有1人评价，您的评价为：👍 ⭐️⭐️⭐️⭐️

// Partial application
const scoreNodeLike = jsCourse.score.bind(nodeCourse, true);

scoreNodeLike(5);
// Node.js：后端 JS 运行环境
// 已有2人评价，您的评价为：👍 ⭐️⭐️⭐️⭐️⭐️
```

`bind` 方法还常用于事件监听器：

```js
const electronCourse = {
  subject: 'Electron',
  likes: 0,

  like() {
    console.log(this);

    console.log(`${this.subject}，点赞数 👍：${++this.likes}`);
  }
}

document.querySelector('.btn-like').addEventListener(
  'click', electronCourse.like.bind(electronCourse)
);
// Object {subject: "Electron", likes: 0, like: ƒ}
// Electron，点赞数 👍：1
```

由于事件处理函数会绑定自己的 this 值。因此下面代码是**错误**的，其中 `this` 的值为 *HTML Button 元素*：

```js
document.querySelector('.btn-like')
  .addEventListener('click', electronCourse.like);
// <button class="btn-like">喜欢</button>
// undefined，点赞数 👍：NaN
```
