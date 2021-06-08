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
description: 创建一个支持内容切换及拖拽的悬浮窗。
---

> {{<reprint>}}

{{< param description >}}

## 目录结构

```
.
├── main
│   ├── floating-window.js
├── renderer
│   ├── floating-window.html
│   ├── img
│   │   └── logo.png
│   └── style.css
├── sass
│   ├── abstracts
│   │   ├── _mixins.scss
│   │   └── _variables.scss
│   ├── base
│   │   └── _base.scss
│   ├── main.scss
│   └── pages
│       └── floating-window.scss
├── main.js
├── package.json
└── package-lock.json
```

## NPM

```package.json
{
  "name": "demo",
  "version": "1.0.0",
  "description": "",
  "main": "main.js",
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

const FloatingWindow = require('./floating-window');

app.whenReady().then(() => {
  new FloatingWindow({ dev: true });
});
```

```:main/floating-window.js
const { BrowserWindow, ipcMain } = require('electron');

class FloatingWindow {
  width = 36;
  height = 36;
  dev;
  win;
  #mini = true;

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

    this.win.loadFile('renderer/floating-window.html').then(() => {
      this.#toggleMain();
    });

    this.dev && this.win.webContents.openDevTools();
  }

  #toggleMain() {
    ipcMain.on('toggle-floating-window', () => {
      this.#mini = !this.#mini;

      if (this.#mini) {
        this.win.setSize(this.width, this.height);
      } else {
        this.win.setSize(180, 180);
      }
    });
  }
}

module.exports = FloatingWindow;
```

## 渲染器进程

```:renderer/floating-window.html
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
      <div class="f_win__tip hidden">双点切换</div>
    </div>

    <script>
      const { ipcRenderer } = require('electron');

      class FloatingWindow {
        container = document.querySelector('.f_win');
        logo = document.querySelector('.f_win__logo');
        tip = document.querySelector('.f_win__tip');

        constructor() {
          this.#handleDoubleClick();
        }

        #handleDoubleClick() {
          this.container.addEventListener('dblclick', () => {
            if (this.tip.classList.contains('hidden')) {
              this.logo.classList.add('hidden');
              this.tip.classList.remove('hidden');
            } else {
              this.logo.classList.remove('hidden');
              this.tip.classList.add('hidden');
            }

            ipcRenderer.send('toggle-floating-window');
          });
        }
      }

      new FloatingWindow();
    </script>
  </body>
</html>
```

## 样式（Sass）

```:sass/abstracts/_helper.scss
.hidden {
  display: none !important;
}
```

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
```:sass/pages/_floating-window.scss
@use '../abstracts/variables' as *;

.f_win {
  height: 100vh;
  background-color: $color-primary;
  display: flex;
  justify-content: center;
  align-items: center;

  &__logo {
    display: inline-block;
    width: 100%;
  }

  &__tip {
    color: white;
  }
}
```

```:sass/main.scss
@use 'abstracts/helpers';

@use 'base/base';

@use './pages/floating-window';
```
