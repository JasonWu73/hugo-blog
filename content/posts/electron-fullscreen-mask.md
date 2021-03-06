---
categories:
  - JavaScript
tags:
  - electron
title: Electron 全屏遮罩
date: 2021-06-08
description: Electron 创建透明的全屏遮罩示例代码
---

创建透明的全屏遮罩，可用于屏幕绘制（批注）、截图等。

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
    // `size`：仅应用区大小
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

      // Windows 下必须配置，而 MacOS 则不需要（否则会打开一个全屏新桌面）
      fullscreen: process.platform !== 'darwin',
      // 1）使覆盖全屏幕，包含 MacOS Menu Bar
      enableLargerThanScreen: true,

      // Windows 下必须配置，否则会禁用视频播放
      type: 'toolbar',

      webPreferences: {
        nodeIntegration: true,
        contextIsolation: false
      }
    });

    // 2）使覆盖全屏幕，包含 MacOS Menu Bar
    this.win.setAlwaysOnTop(true, 'screen-saver');

    this.win.loadFile('renderer/index.html');
  }

  _forMacOs() {
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

<!--more-->
