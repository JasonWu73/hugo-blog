---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- JavaScript
tags:
- electron
- sass
series:
- Electron Samples
title: Electron 悬浮窗
date: 2021-06-03T15:09:14+08:00
description: 通过 Electron BrowserWindow API 创建悬浮窗。
---

> {{<reprint>}}

{{< param description >}}

## 目录结构

```
.
├── main
│   ├── floating_window.js
│   └── main.js
├── package-lock.json
├── package.json
├── renderer
│   ├── floating_window.html
│   ├── img
│   │   └── logo.png
│   └── style.css
└── sass
    ├── abstracts
    │   ├── _mixins.scss
    │   └── _variables.scss
    ├── base
    │   └── _base.scss
    ├── main.scss
    └── pages
        └── floating_window.scss
```

## NPM

```package.json
{
  "name": "demo",
  "version": "1.0.0",
  "description": "",
  "main": "main/main.js",
  "scripts": {
    "start": "electron .",
    "watch": "nodemon --exec electron .",
    "compile:sass": "sass --no-source-map --watch sass/main.scss:renderer/style.css"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "electron": "^12.0.5",
    "electron-builder": "^22.11.2",
    "nodemon": "^2.0.7",
    "sass": "^1.33.0"
  }
}
```

## 主进程

```:main/main.js
const { app } = require('electron');

const FloatingWindow = require('./floating_window');

app.whenReady().then(() => {
  new FloatingWindow({ dev: true });
});
```

```:main/floating_window.js
const { BrowserWindow } = require('electron');

class FloatingWindow {
  width = 36;
  height = 36;
  dev;
  win;

  constructor({ dev = false } = {}) {
    this.dev = dev;

    this.#createWindow();
  }

  #createWindow() {
    this.win = new BrowserWindow({
      width: this.width,
      height: this.height,
      alwaysOnTop: true,
      transparent: true,
      frame: false,
      resizable: false,
      hasShadow: false,
      webPreferences: {
        nodeIntegration: true,
        contextIsolation: false
      }
    });

    this.win.loadFile('renderer/floating_window.html');

    this.dev && this.win.webContents.openDevTools();
  }
}

module.exports = FloatingWindow;
```

## 渲染器进程

```:renderer/floating_window.html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <meta http-equiv="Content-Security-Policy"
          content="script-src 'self' 'unsafe-inline'">
    <link rel="stylesheet" href="style.css">
    <title>悬浮窗</title>
  </head>
  <body style="-webkit-app-region: drag">
    <div class="f_win">
      <img class="f_win__logo" src="img/logo.png">
    </div>
  </body>
</html>
```

## 样式（Sass）

```:sass/abstracts/_mixins.scss
@mixin no-drag {
  -webkit-user-drag: none;
}
```
```:sass/abstracts/_variables.scss
$color-primary: #2d2a2e;
```

```:sass/base/_base.scss
@use '../abstracts/mixins' as *;

*,
*::after,
*::before {
  margin: 0;
  padding: 0;
  box-sizing: inherit;
}

html {
  // 定义 1rem = 10px
  font-size: 10px / 16px * 100%;
}

body {
  // Electron：系统字体
  font: caption;
  font-size: 1.6rem;

  box-sizing: border-box;
}

img {
  // Electron：图片不可拖拽
  @include no-drag;
}
```
```:sass/pages/_floating_window.scss
@use '../abstracts/variables' as *;

.f_win {
  height: 100vh;
  background-color: $color-primary;

  &__logo {
    display: inline-block;
    width: 100%;
  }
}
```

```:sass/main.scss
@use 'base/base';

@use './pages/floating_window';
```
