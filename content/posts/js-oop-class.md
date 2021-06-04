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
description: 现代 JS OOP 语法糖及新特性。
---

> {{<reprint>}}

{{< param description >}}

## ES6 Class 特点

- 仅仅是[构造函数]({{< relref "/posts/js-oop#构造函数" >}})的语法糖
- 不会 Hoisted
- 是 First Class Citizen，即类可以被当成参数使用
- Class Body 永远都只在 Strict Mode（`'use strict';`）下执行

## 类表达式 vs 类声明

与函数一样，类的定义也有两种方式：

- 类表达式（Class Expression）
- 类声明（Class Declaration）
  
<br>但与函数不同的是：类声明**不会** Hoisted！

```js
// 类声明
class User {
}

// 类表达式
const Guest = class {};
```

## 语法糖

```js
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  // 方法是被加入到 `.prototype` 原型对象中
  sayHi() {
    console.log(`你好，${this.name}`);
  }
}

// 证明 ES6 Class 仅仅是构造函数的语法糖
User.prototype.showAge = function () {
  console.log(`年龄：${this.age}`);
};

const jason = new User('Jason', 25);
jason.sayHi(); // 你好，Jason
jason.showAge(); // 年龄：25

// 检查原型链
console.dir(jason);
```

## Getter 和 Setter

{{< notice info "JS Getter 和 Setter 同名属性" >}}
约定俗成：当 Getter/Setter 中的属性名与字段同名时，加上 `_` 前缀作为区分。
{{< /notice >}}

```js
class User {
  constructor(name, age) {
    // 这里定义的变量叫字段（field）
    this.name = name;
    this.age = age;
  }

  // 这里定义的变量叫属性（property）
  set age(age) {
    if (age >= 0 && age <= 100) {
      // 当属性和字段同名时，加上 `_` 前缀，并定义相应 Getter 方法
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

## 静态属性和方法

```js
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  // 方式一（推荐）
  static versionPrefix = 'v';

  static info () {
    console.log(`版本：${User.versionPrefix}${User.versionNum}`);
  }
}

// 方式二
User.versionNum = '1.0.0';

const jason = new User('Jason', 25);

console.log(jason.versionNum); // undefined

console.log(`${User.versionPrefix}${User.versionNum}`); // v1.0.0
User.info(); // 版本：v1.0.0
```

## 继承与多态

```js
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  };

  sayHi() {
    console.log(`你好，${this.name}`);
  }
}

class Vip extends User {
  constructor(name, age, level) {
    // 必须位于构造方法的第一句
    super(name, age);

    this.level = level;
  };

  sayHi() {
    console.log(`用户：${this.name}，VIP 等级：${this.level}`);
  }
}

const jason = new Vip('Jason', 25, '3');

jason.sayHi(); // 用户：Jason，VIP 等级：3

// 检查原型链
console.log(jason instanceof Vip); // true
console.log(jason instanceof User); // true
console.log(jason instanceof Object); // true
```

## 封装

### 伪私有属性和方法

{{< notice info "JS 中的私有属性（Properties）和方法" >}}
因很长一段时间 JS 都是没有私有特性，故约定俗成：以 `_` 开头的属性或方法就是私有的，开发人员不应直接操作这类名称的变量。
{{< /notice >}}

```js
class User {
  constructor(name, passwd) {
    // 受保护的属性
    this._rpasswd = '123';

    this.name = name;
    this.passwd = passwd;
  };

  login() {
    if (this._check()) return console.log(`${this.name}，登录成功`);

    console.log('用户名或密码错误');
  }

  // 受保护的方法
  _check() {
    return this.passwd === this._rpasswd;
  }
}

const jason = new User('Jason', '123');
jason.login(); // Jason，登录成功

// 因为不是语言特性，所以仍可访问，但此时开发者自身是知道执行了不合规的操作
console.log(jason._rpasswd); // 123
console.log(jason._check()); // true
```

### 真私有字段和方法

[Class field declarations for JavaScript](https://github.com/tc39/proposal-class-fields) 中为 JS 引入了私有字段和方法（同样适用于静态）。

```js
class User {
  // 1) Public fields
  name;
  passwd;

  // 2) Private field
  #rPasswd = '123';

  constructor(name, passwd) {
    this.name = name;
    this.passwd = passwd;
  };

  // 3) Public method
  login() {
    if (this.#check()) return console.log(`${this.name}，登录成功`);

    console.log('用户名或密码错误');
  }

  // 4) private method
  #check() {
    return this.passwd === this.#rPasswd;
  }
}

const jason = new User('Jason', '123');
jason.login(); // Jason，登录成功

// 因为是语言特性，所以真正实现了私有
// console.log(jason.#rPasswd); // Uncaught SyntaxError
// console.log(jason.#check()); // Uncaught SyntaxError
```

## 链式调用

```js
class Account {
  constructor() {
    this._movements = [];
  }

  deposit(val) {
    this.movements.push(val);
    return this; // 返回对象本身
  }

  withdraw(val) {
    this.deposit(-val);
    return this; // 返回对象本身
  }

  get movements() {
    return this._movements;
  }
}

const account = new Account();

account
  .deposit(100)
  .withdraw(50)
  .withdraw(20)
  .deposit(70);

console.log(account.movements); // [100, -50, -20, 70]
```
