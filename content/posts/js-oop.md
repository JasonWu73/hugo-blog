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

JS 中的 OOP 与 Java、Python 不同，JS 中没有真正意义上类的这个概念，它是通过原型（Prototype）实现面向对象的继承。

{{< notice success "面向对象的四大基本特征" >}}
抽象（Abstraction）、封装（Encapsulation）、继承（Inheritance）和多态（Polymorphism）在 JS OOP 中和其他语言没有任何区别。
{{< /notice >}}

<br>原型继承（Prototypal Inheritance）
: 原型对象的所有属性和方法都可以被链接到它的对象访问。

<br>JS 中实现原型继承有三种方式：

- 构造函数
    - 使用普通函数创建对象，然后指定原型
    - JS 最早实现 OOP 的方式
- [ES6 Class]({{< relref "/posts/js-oop-class" >}})
    - 现代 JS 创建对象的方式，但它仅仅是构造函数的“语法糖”
- `Object.create()`
    - 将现有对象作为原型对象，从而创建一个新对象

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
}

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

### 静态方法

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
