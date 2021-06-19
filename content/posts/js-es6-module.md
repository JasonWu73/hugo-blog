---
toc: true
categories:
  - JavaScript
tags:
  - Module
title: 📌 ES6 模块
weight: 1
date: 2021-06-04
description: ES6 import/export 模块
---

## ES6 模块 vs JS 普通文件

| 对比项 | ES6 模块 | JS 普通文件 |
| :---: | :---: | :---: |
| 顶级变量 | 模块范围 | 全局范围 |
| 默认模式 | 严格模式（Strict Mode）| 非严格模式（No-Strict Mode，Sloppy Mode）|
| 顶级 `this` 值 | `undefined` | `window` |
| 导入/导出 | 支持，但只支持顶级导入/导出，且导入会 Hoisted | 不支持 |
| HTML 中 | `<script type="module">` | `<script>` |
| 下载文件 | 异步 | 同步 |

<!--more-->

## ES6 模块特点

- 一个文件代表一个模块
- 模块通过**同步**导入，但模块文件通过**异步**下载
- 导出/导入是 Live Binding，即引用（或理解为指针）

## 导入/导出模块

导入语法：

```js
// 静态导入
import defaultExport from 'module-name';
import * as name from 'module-name';
import { export1, export2 as alias2, ... } from 'module-name';
import defaultExport, { export1, ...  } from 'module-name';
import defaultExport, * as name from 'module-name';
import 'module-name';

// 动态导入
const promise = import('module-name');
const module = await import('module-name');
```

导出语法：

```js
// 命名导出（一个模块可以有0个或多个）
// 分别导出
export let name1, ..., nameN; // 同 `var`，`const`
export let name1 = value1, ..., nameN = valueN; // 同 `var`，`const`
export function functionName() {...}
export class ClassName {...}

// 批量导出
export { name1, ..., nameN };

// 重命名导出
export { variable1 as name1, ..., nameN };

// 重命名解构赋值导出
export const { name1, name2: bar } = o;

// 默认导出（一个模块仅有一个）
export default expression;
export default function (...) { ... } // 同 `class`，function*
export default function name1(...) { ... } // 同 `class`，function*
export { name1 as default, ... };
```

示例，`index.js`

```js
import {
  lan, // 导入命名导出
  programs as p // 导入命名导出，并重命名引用
} from './m1.js';

console.log(lan);
console.log(p);

// 导入模块中的一切
import * as m1 from './m1.js';

console.log(m1.lan);
console.log(m1.programs);

// 导入默认导出
import info from './m1.js';

info();

// 混合导入（不推荐，命名导出和默认导出应该是二选其一）
import log, { programs } from './m1.js';

log();
console.log(programs);

// 验证 Live Binding
import { arr, add } from './m1.js';

add(1);
add(2);
console.log(arr); // [1, 2]

// 动态导入
import('./m1.js').then((module) => {
  module.default();
});

// 动态导入 `await`
const module = await import('./m1.js');
console.log(module.arr);
```

`m1.js`

```js
// 命名导出
export const programs = ['JS', 'Java'];

// 重命名后导出
const languages = ['zh', 'en'];

export { languages as lan };

// 默认导出，一个模块仅存在一个默认导出
export default () => {
  console.log('默认模块');
};

// 验证 Live Binding
export const arr = [];

export const add = (val) => {
  arr.push(val);
};
```

`index.html`

```html
<script type="module" src="index.js"></script>
```

## ES6 以前的模块模式

在 ES6 模块引入之前，我们常用的一种编码模式是通过 IIFE（Immediately Invoked Function Expression）封装代码块，从而区分私有和公开。

```js
var api = (function () {
  var privateVal = '私有变量';

  var publicFunc = function () {
    console.log('需暴露出的公有 API');
  }

  return {
    publicFunc
  };
})();

api.publicFunc();

console.log(api.privateVal); // undefined
```

缺点：

- 无法避免变量名的全局污染
- 需手动识别 JS 文件间的依赖关系，在 HTML 中必须是被依赖的 JS 先导入 

## ES6 以前常用的 JS 模块库

- AMD（Asynchronous module definition）
  - 如 [RequireJS](https://requirejs.org/)
- [CommonJS](https://nodejs.org/api/modules.html)
  - 用于 Node.js
  - Web 浏览器之外的 JS 模块生态系统
