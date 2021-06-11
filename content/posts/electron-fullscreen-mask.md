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
    // Linux 中禁用 GPU 渲染，否则无法透明窗口
    if (process.platform === 'linux') {
      app.disableHardwareAcceleration();
    }
    
    app.whenReady().then(() => {
      this._createWindow();
      this._forMacOs();
    });
  }

  _createWindow() {
    // `bounds`：使覆盖全屏幕，包含 MacOS Menu Bar
    // `size`：仅工作区大小
    const { width, height, x, y } = screen.getPrimaryDisplay().bounds;

    this.win = new BrowserWindow({
      width,
      height,
      x,
      y,
      hasShadow: false,
      transparent: true,
      frame: false,
      movable: false,
      resizable: false,

      // 不会将窗口置于 MacOS Menu Bar 之上
      // alwaysOnTop: true,

      // Windows 下必须配置，MacOS 下不需要（否则会打开一个全屏新桌面）
      fullscreen: process.platform !== 'darwin',
      // 使覆盖全屏幕，包含 MacOS Menu Bar
      enableLargerThanScreen: true,

      // Windows 下必须配置 `toolbar`，否则会禁用视频播放
      type: 'toolbar',

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
