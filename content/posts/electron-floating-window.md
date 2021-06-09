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
description: 创建支持内容切换及拖拽的悬浮窗，包含完整目录结构及示例代码。
---

> {{<reprint>}}

{{< param description >}}

## 目录结构

```
.
├── main
│   └── floating-window.js
├── renderer
│   ├── img
│   │   └── logo.png
│   ├── floating-window.html
│   └── style.css
├── sass
│   ├── abstracts
│   │   ├── _helpers.scss
│   │   ├── _mixins.scss
│   │   └── _variables.scss
│   │── base
│   │   └── _base.scss
│   │── pages
│   │   └── _floating-window.scss
│   └── main.scss
├── main.js
├── package.json
└── package-lock.json
```

## NPM

```package.json
{
  "name": "floating-window",
  "version": "1.0.0",
  "description": "悬浮窗",
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
    "electron": "^13.1.1",
    "nodemon": "^2.0.7",
    "sass": "^1.34.1"
  }
}
```

## 主进程

```:main.js
const { app, BrowserWindow } = require('electron');

const FloatingWindow = require('./main/floating-window');

class Main {
  floWin = new FloatingWindow();

  constructor() {
    app.whenReady().then(() => {
      this._createWindow();
      this.forMacOs();
    });
  }

  _createWindow() {
    this.floWin.resetWindow();
  }

  forMacOs() {
    app.on('activate', () => {
      if (BrowserWindow.getAllWindows().length === 0)
        this._createWindow();
    });

    app.on('window-all-closed', () => {
      if (process.platform !== 'darwin') app.quit();
    });
  }
}

new Main();
```

```:main/floating-window.js
const { BrowserWindow, ipcMain } = require('electron');

class FloatingWindow {
  width = 36;
  height = 36;
  _min = true; // 是否是最小化的窗口尺寸

  win;

  resetWindow() {
    if (this.win && !this.win.isDestroyed()) return;

    this._createWindow();
  }

  _createWindow() {
    this.win = new BrowserWindow({
      width: this.width,
      height: this.height,
      frame: false,
      alwaysOnTop: true,
      resizable: false,

      // 将窗口背景设置与页面背景一致，避免闪烁
      backgroundColor: '#2d2a2e',

      webPreferences: {
        nodeIntegration: true,
        contextIsolation: false
      }
    });

    // this.win.webContents.openDevTools();

    this.win.loadFile('renderer/floating-window.html').then(() => {
      this._toggleWindowSize();
    });
  }

  _toggleWindowSize() {
    ipcMain.handle('toggle-floating-window', async () => {
      this._min = !this._min;

      if (this._min) {
        this.win.setSize(this.width, this.height);
      } else {
        this.win.setSize(180, 180);
      }

      return this._min;
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
    <div class="floating-window">
      <img class="floating-window__logo" src="img/logo.png">
      <div class="floating-window__tip hidden">双点切换</div>
    </div>

    <script>
      const { ipcRenderer } = require('electron');

      class Main {
        containerEl = document.querySelector('.floating-window');
        logoEl = document.querySelector('.floating-window__logo');
        tipEl = document.querySelector('.floating-window__tip');

        constructor() {
          this._handleDoubleClick();
        }

        _handleDoubleClick() {
          this.containerEl.addEventListener('dblclick', () => {
            this._hideAll();

            ipcRenderer.invoke('toggle-floating-window').then(min => {
              if (min) {
                this.logoEl.classList.remove('hidden');
              } else {
                this.tipEl.classList.remove('hidden');
              }
            });
          });
        }

        _hideAll() {
          !this.logoEl.classList.contains('hidden')
          && this.logoEl.classList.add('hidden');

          !this.tipEl.classList.contains('hidden')
          && this.tipEl.classList.add('hidden');
        }
      }

      new Main();
    </script>
  </body>
</html>
```

## 样式（Sass）

```:sass/abstracts/_helpers.scss
.hidden {
  display: none !important;
}
```

```:sass/abstracts/_mixins.scss
@mixin no-drag {
  -webkit-user-drag: none;
}

@mixin no-selection {
  user-select: none;
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
@use '../abstracts/mixins' as *;

.floating-window {
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
    @include no-selection;
  }
}
```

```:sass/main.scss
@use 'abstracts/helpers';

@use 'base/base';

@use 'pages/floating-window';
```
