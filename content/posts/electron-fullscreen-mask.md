---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- JavaScript
tags:
- electron
series:
- Electron Samples
title: Electron 全屏遮罩
date: 2021-06-08T18:34:37+08:00
description: 创建透明的全屏遮罩，包含 MacOS Menu Bar。
---

> {{<reprint>}}

{{< param description >}}

```js
const { app, BrowserWindow, screen } = require('electron');

class Main {
  win;

  constructor() {
    app.whenReady().then(() => {
      this._createWindow();
      this._forMacOs();
    });
  }

  _createWindow() {
    const { width, height, x, y } = screen.getPrimaryDisplay().bounds;

    this.win = new BrowserWindow({
      width,
      height,
      x,
      y,
      transparent: true,
      frame: false,
      movable: false,
      resizable: false,

      // `alwaysOnTop: true`：不会将窗口置于 MacOS Menu Bar 之上
      // 使覆盖全屏幕，包含 MacOS Menu Bar
      enableLargerThanScreen: true,

      webPreferences: {
        nodeIntegration: true,
        contextIsolation: false
      }
    });

    // 使覆盖全屏幕，包含 MacOS Menu Bar
    this.win.setAlwaysOnTop(true, 'screen-saver');

    this.win.loadFile('renderer/index.html');
  }

  _forMacOs() {
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
