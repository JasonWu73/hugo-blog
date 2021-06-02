---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- JavaScript
tags:
- oop
series:
- JavaScript OOP
title: JavaScrip OOP：ES6 Class
date: 2021-06-01T18:46:24+08:00
description: ES6 Class - 现代 JS OOP 编码方式。
---

> {{<reprint>}}

{{< param description >}}

## 类表达式 vs 类声明

与函数一样，类的定义也有两种方式：

- 类表达式（Class Expression）
- 类声明（Class Declaration）
  
<br>但与函数不同的是：类声明**不会** hoisted！

```js
// 类声明
class User {
}

// 类表达式
const Guest = class {};
```

ES6 Class 特点：

- 不会 hoisted
- First Class Citizen，即类可以被当成参数使用
- Class Body 永远在 Strict Mode（`'use strict';`）下执行

## 语法糖

```js
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  // 方法是被加入到 `.prototype` 原型中
  sayHi() {
    console.log(`你好，${this.name}`);
  }
}

// 证明 ES6 Class 仅仅是构造函数的语法糖
User.prototype.showAge = function () {
  console.log(`年龄：${this.age}`);
}

const jason = new User('Jason', 25);
jason.sayHi(); // 你好，Jason
jason.showAge(); // 年龄：25
```

## Getter 和 Setter

```js
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  set age(age) {
    if (age >= 0 && age <= 100) {
      // 对于同名实例属性，加上 `_` 前缀，并定义相应 Getter 方法
      this._age = age;
    } else {
      throw new Error(`${age}不在0~100的年龄范围内`)
    }
  }

  get age() {
    return this._age;
  }
}

const jason = new User('Jason', 25);
console.log(jason.age); // 25

jason.age = 99;
console.log(jason.age); // 99
```

## 静态方法

```js
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  static version = 'v1.0.0';

  static info () {
    console.log(`${this.name}，开发者：Jason Wu，版本：${User.version}`);
  }
}
User.version = 'v1.0.0';


const jason = new User('Jason', 25);

console.log(jason.version); // undefined

console.log(User.version); // v1.0.0
User.info(); // User，开发者：Jason Wu，版本：v1.0.0
```
