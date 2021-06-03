---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- JavaScript
tags:
- oop
series:
- JavaScript OOP
title: JavaScript OOP：必知必会
date: 2021-06-01T15:42:47+08:00
description: JS 中实现 OOP（Object-Oriented Programming）的原理及方式。
---

> {{<reprint>}}

{{< param description >}}

## 原型继承

JS 中的 OOP 与 Java 不同，JS 中没有真正意义上的类，它是通过原型（Prototype）实现 OOP 的。

{{< notice success "面向对象的四大基本特征" >}}
抽象（Abstraction）、封装（Encapsulation）、继承（Inheritance）和多态（Polymorphism）在 JS OOP 中和其他语言没有任何区别。
{{< /notice >}}

<br>原型继承（Prototypal Inheritance）
: 原型对象的所有属性和方法都可以被链接到它的对象访问。

<br>JS 中实现原型继承有三种方式：

- [构造函数]({{< relref "#构造函数" >}})
    - 使用普通函数创建对象，然后指定原型
    - JS 最早实现 OOP 的方式
- [ES6 Class]({{< relref "/posts/js-oop-class" >}})
    - 现代 JS 创建对象的方式，但它仅仅是构造函数的“语法糖”
- [`Object.create()`]({{< relref "#objectcreate" >}})
    - 将现有对象作为原型对象，从而创建一个新对象（空对象 `{}`）

## 构造函数

构造函数与普通函数的唯一区别：构造函数使用 `new` 操作符调用。

{{< notice warning "箭头函数" >}}
由于箭头函数没有自己的 `this` 关键字，因此箭头函数不能作为构造函数，也不能用于作为原型对象的方法。
{{< /notice >}}

### 创建对象（错误示例）

```js
const User = function (name, age) {
  // 实例属性（Instance properties）
  this.name = name;
  this.age = age;

  // 这是错误的，不要在构造函数中定义方法！！！
  // 这会导致每个实例都有一份方法拷贝，这将严重影响 JS 性能。
  // 正确做法应该是使用原型对象的方法！
  this.sayHi = function () {
    console.log(`你好，${this.name}`);
  }
};

const jason = new User('Jason', 25);
jason.sayHi(); // 你好，Jason

console.log(jason instanceof User); // true
```

### 原型链

```js
const User = function (name, age) {
  // 实例属性（Instance properties）
  this.name = name;
  this.age = age;
};

User.prototype.type = '测试用户';

User.prototype.sayHi = function () {
  console.log(`你好，${this.name}`);
};

const jason = new User('Jason', 25);

jason.sayHi(); // 你好，Jason
console.log(jason.type); // 测试用户

console.log(jason instanceof User); // true

// 检查原型对象
console.log(User.prototype === jason.__proto__); // true
console.log(User.prototype.isPrototypeOf(jason)); // true
console.log(User.prototype.isPrototypeOf(User)); // false

console.log(jason.hasOwnProperty('name')); // true
console.log(jason.hasOwnProperty('type')); // false

// 查看原型链
console.log(jason.__proto__);
// {type: "测试用户", sayHi: ƒ, constructor: ƒ}

console.log(jason.__proto__.__proto__); // 原型链的顶端
// {constructor: ƒ, hasOwnProperty: ƒ, isPrototypeOf: f, …}

console.log(jason.__proto__.__proto__.__proto__); // null

console.dir(User.prototype.constructor); // User(name, age)

// 探索 DOM 元素的原型链
console.dir(document.querySelector('body'));
```

关键字 `new` 的工作过程：
1. 创建一个新对象 `{}`
2. 调用函数，`this = {}`
3. `{}`（通过 `__proto__` 属性）链接到构造函数的 `prototype` 属性
4. 函数自动返回 `{}`

<br>原型链（Prototype Chain）
: 用于查找原型上的属性和方法。

<br>{{< img src="/images/posts/js_prototype_chain.jpg" title="JS 原型链" caption="示例图" alt="JS 原型链示例图" position="center" >}}

### 静态属性和方法

```js
const User = function (name, age) {
  // 实例属性（Instance properties）
  this.name = name;
  this.age = age;
};

// 静态属性和方法是绑定在 User Construct，而非 Prototype Constructor
User.version = 'v1.0.0';

User.info = function () {
  console.log(`${this.name}，开发者：Jason Wu，版本：${User.version}`);
}

const jason = new User('Jason', 25);

console.log(jason.version); // undefined

console.log(User.version); // v1.0.0
User.info(); // User，开发者：Jason Wu，版本：v1.0.0
```

### 继承与多态

```js
const User = function (name, age) {
  this.name = name;
  this.age = age;
};

User.prototype.sayHi = function () {
  console.log(`你好，${this.name}`);
};

const Vip = function (name, age, level) {
  // 1. 使用父类构造函数
  User.call(this, name, age);

  this.level = level;
};

// 2. 链接原型对象
Vip.prototype = Object.create(User.prototype);

// 3. 修正原型对象的构造函数
// console.log(Vip.prototype.constructor); // `User` 函数
Vip.prototype.constructor = Vip;
// console.log(Vip.prototype.constructor); // `Vip` 函数

Vip.prototype.sayHi = function () {
  console.log(`用户：${this.name}，VIP 等级：${this.level}`);
};

const jason = new Vip('Jason', 25, '3');

jason.sayHi(); // 用户：Jason，VIP 等级：3

// 检查原型链
console.log(jason instanceof Vip); // true
console.log(jason instanceof User); // true
console.log(jason instanceof Object); // true
```

## Object.create()

```js
const userProto = {
  init(name, age) {
    this.name = name;
    this.age = age;
  },

  sayHi() {
    console.log(`你好，${this.name}`);
  }
};

// 使用已有对象为原型对象从而创建一个新的空对象
const jason = Object.create(userProto);

jason.init('Jason', 25);
jason.sayHi(); // 你好，Jason

// 检查原型对象
console.log(jason.__proto__ === userProto); // true
```

### 继承与多态

```js
const userProto = {
  init(name, age) {
    this.name = name;
    this.age = age;
  },

  sayHi() {
    console.log(`你好，${this.name}`);
  }
};

// 1. 创建新的原型对象
const vipProto = Object.create(userProto);

vipProto.init = function (name, age, level) {
  // 2. 使用父类构造函数
  userProto.init.call(this, name, age);

  this.level = level;
}

vipProto.sayHi = function () {
  console.log(`用户：${this.name}，VIP 等级：${this.level}`);
};

const jason = Object.create(vipProto);

jason.init('Jason', 25, 3);
jason.sayHi(); // 用户：Jason，VIP 等级：3

// 检查原型链
console.dir(jason);
```
