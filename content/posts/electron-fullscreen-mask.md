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
description: 创建透明的全屏遮罩。
---

> {{<reprint>}}

{{< param description >}}

```js
const { app, BrowserWindow, screen } = require('electron');

class Main {
  mainWindow;

  constructor() {

    app.whenReady().then(() => {
      this._createWindow();
      this._adjustForMacOs();
    });
  }

  _createWindow() {
    const { width, height, x, y } = screen.getPrimaryDisplay().bounds;

    this.mainWindow = new BrowserWindow({
      width,
      height,
      x,
      y,
      transparent: true,
      frame: false,
      movable: false,
      resizable: false,
      enableLargerThanScreen: true,
      webPreferences: {
        nodeIntegration: true,
        contextIsolation: false
      }
    });

    this.mainWindow.loadFile('renderer/index.html');

    // 使覆盖全屏幕，包含系统任务栏
    this.mainWindow.setAlwaysOnTop(true, 'screen-saver');
  }

  _adjustForMacOs() {
    app.on('activate', () => {
      if (BrowserWindow.getAllWindows().length === 0) this._createWindow();
    });

    app.on('window-all-closed', () => {
      if (process.platform !== 'darwin') app.quit();
    });
  }
}

new Main();
```
