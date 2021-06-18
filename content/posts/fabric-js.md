---
categories:
  - JavaScript
tags:
  - Fabric.js
title: HTML Canvas：FabricJS
date: 2021-06-11
description: Fabric.js v4.4.0 示例代码
---

Fabric.js v4.4.0 实现画笔、文本、直线及椭圆等绘制的示例代码。

<!--more-->

`index.html`

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>FabricJS</title>
    <style>
      .fabric-toolbar {
        width: 720px;
        position: absolute;
        left: 0;
        right: 0;
        margin: auto;
        z-index: 1;

        display: grid;
        grid-template-rows: 1fr 1fr;
        grid-template-columns: repeat(7, 1fr);
        border: 1px solid black;
        grid-gap: 10px;
        background-color: black;
      }

      .fabric-toolbar > div {
        background-color: white;
        padding: 15px;
        text-align: center;
      }

      .fabric-toolbar__tool {
        cursor: pointer;
      }

    </style>
  </head>
  <body>
    <div class="fabric-toolbar">
      <div class="fabric-toolbar__tool js-mode" data-mode="draw">
        画笔
      </div>
      <div class="fabric-toolbar__tool js-mode" data-mode="text">
        文本
      </div>
      <div class="fabric-toolbar__tool js-mode" data-mode="line">
        直线
      </div>
      <div class="fabric-toolbar__tool js-mode" data-mode="ellipse">
        椭圆
      </div>
      <div class="fabric-toolbar__tool js-mode" data-mode="rect">
        矩形
      </div>
      <div class="fabric-toolbar__tool js-mode" data-mode="eraser">
        像皮擦
      </div>
      <div id="js-undo" class="fabric-toolbar__tool">上一步</div>

      <div>
        <input type="radio" name="width" data-value="6" checked>6px
      </div>
      <div><input type="radio" name="width" data-value="10">10px</div>
      <div><input type="radio" name="width" data-value="14">14px</div>

      <div style="background-color: #e74c3c;">
        <input type="radio" name="color" data-value="#e74c3c" checked>
      </div>
      <div style="background-color: #2ecc71;">
        <input type="radio" name="color" data-value="#2ecc71">
      </div>
      <div style="background-color: #3498db;">
        <input type="radio" name="color" data-value="#3498db">
      </div>
      <div style="background-color: #e67e22;">
        <input type="radio" name="color" data-value="#e67e22">
      </div>
    </div>

    <canvas id="js-canvas"></canvas>

    <script src="fabric.min.js"></script>
    <script src="index.js" type="module"></script>
  </body>
</html>
```

`index.js`

```js
class Main {
  modeEls = document.querySelectorAll('.js-mode');
  undoEls = document.getElementById('js-undo');
  widthEls = document.querySelectorAll('input[name="width"]');
  colorEls = document.querySelectorAll('input[name="color"]');

  canvas;

  _width = 6;
  _color = '#e74c3c';
  _histories = [];
  _modes = {
    draw: 'draw',
    text: 'text',
    line: 'line',
    ellipse: 'ellipse',
    rect: 'rect',
    eraser: 'eraser'
  };
  _curMode;

  constructor() {
    this._init();
    this._toggleModeClick();
    this._handleUndoClick();
    this._handleRadioChange();
  }

  _init() {
    this.canvas = new fabric.Canvas('js-canvas', {
      width: window.innerWidth,
      height: window.innerHeight,
      isDrawingMode: true,
      selection: false
    });

    this.canvas.freeDrawingBrush.width = this._width;
    this.canvas.freeDrawingBrush.color = this._color;

    // 记录操作历史
    this.canvas.on('object:added', () => {
      this._histories.push(JSON.stringify(this.canvas));
    });

    this._registerCanvasEvent();
  }

  _toggleModeClick() {
    this.modeEls.forEach(el => {
      el.addEventListener('click', e => {
        const tarEl = e.target.closest('.js-mode');
        const mode = tarEl.dataset.mode;

        if (this._curMode === mode) return;

        this._curMode = mode;

        // 重置所有
        this._resetAllMode();

        if (mode === this._modes.draw) {
          this.canvas.isDrawingMode = true;

        } else if (mode === this._modes.eraser) {
          this.canvas.hoverCursor = 'not-allowed';

        } else if (mode === this._modes.text) {
          this.canvas.defaultCursor = 'text';
          this.canvas.hoverCursor = 'text';

        } else if (mode === this._modes.line) {
          this.canvas.defaultCursor = 'crosshair';
          this.canvas.hoverCursor = 'crosshair';

        } else if (mode === this._modes.ellipse) {
          this.canvas.defaultCursor = 'crosshair';
          this.canvas.hoverCursor = 'crosshair';

        } else if (mode === this._modes.rect) {
          this.canvas.defaultCursor = 'crosshair';
          this.canvas.hoverCursor = 'crosshair';
        }
      });
    });
  }

  _handleUndoClick() {
    this.undoEls.addEventListener('click', () => {
      this._histories.pop(); // 排除当前状态

      if (this._histories.length === 0) {
        this.canvas.clear().requestRenderAll();
        return;
      }

      const state = this._histories.pop();
      this._histories = []; // 清空队列
      this.canvas.loadFromJSON(state, () => {
        this.canvas.requestRenderAll();
      });
    });
  }

  _handleRadioChange() {
    const brush = this.canvas.freeDrawingBrush;

    this.widthEls.forEach(el => {
      el.addEventListener('change', e => {
        this._width = +e.target.dataset.value;

        this._preventAllControl();
        brush.width = this._width;
      });
    });

    this.colorEls.forEach(el => {
      el.addEventListener('change', e => {
        this._color = e.target.dataset.value;

        this._preventAllControl();
        brush.color = this._color;
      });
    });
  }

  _registerCanvasEvent() {
    let mouseDown;
    let line, ellipse, rect;
    let origX, origY;

    this.canvas.on('mouse:down', options => {
      const e = options.e;
      mouseDown = true;

      if (options.target && this._curMode === this._modes.eraser) {
        this.canvas.getActiveObjects().forEach(obj => {
          this.canvas.remove(obj);
        });

      } else if (this._curMode === this._modes.text) {
        const text = new fabric.IText('', {
          // 减多少，我也是凑的，能用就行
          left: e.clientX - 8,
          top: e.clientY - 23,

          fill: this._color,
          fontSize: 24,
          lockMovementX: true,
          lockMovementY: true,
          lockScalingX: true,
          lockScalingY: true,
          lockRotation: true,
          hasControls: false,
          hasBorders: false
        });

        this.canvas.add(text).setActiveObject(text);
        text.enterEditing();

      } else if (this._curMode === this._modes.line) {
        const { x, y } = this.canvas.getPointer(e);
        const points = [x, y, x, y];

        line = new fabric.Line(points, {
          strokeWidth: this._width,
          fill: this._color,
          stroke: this._color,
          originX: 'center',
          originY: 'center',
          lockMovementX: true,
          lockMovementY: true,
          lockScalingX: true,
          lockScalingY: true,
          lockRotation: true,
          hasControls: false,
          hasBorders: false
        });

        this.canvas.add(line);

      } else if (this._curMode === this._modes.ellipse) {
        const { x, y } = this.canvas.getPointer(e);
        origX = x;
        origY = y;

        ellipse = new fabric.Ellipse({
          left: x,
          top: y,
          originX: 'left',
          originY: 'top',
          rx: 0,
          ry: 0,
          fill: '',
          stroke: this._color,
          strokeWidth: this._width,
          lockMovementX: true,
          lockMovementY: true,
          lockScalingX: true,
          lockScalingY: true,
          lockRotation: true,
          hasControls: false,
          hasBorders: false
        });

        this.canvas.add(ellipse);

      } else if (this._curMode === this._modes.rect) {
        const { x, y } = this.canvas.getPointer(e);
        origX = x;
        origY = y;

        rect = new fabric.Rect({
          left: x,
          top: y,
          originX: 'left',
          originY: 'top',
          width: 0,
          height: 0,
          angle: 0,
          fill: '',
          stroke: this._color,
          strokeWidth: this._width,
          lockMovementX: true,
          lockMovementY: true,
          lockScalingX: true,
          lockScalingY: true,
          lockRotation: true,
          hasControls: false,
          hasBorders: false
        });

        this.canvas.add(rect);
      }
    });

    this.canvas.on('mouse:move', options => {
      if (!mouseDown) return;

      const e = options.e;

      if (this._curMode === this._modes.line) {
        const { x, y } = this.canvas.getPointer(e);
        line.set({ x2: x, y2: y });
        this.canvas.requestRenderAll();

      } else if (this._curMode === this._modes.ellipse) {
        const { x, y } = this.canvas.getPointer(e);
        let rx = Math.abs(origX - x) / 2;
        let ry = Math.abs(origY - y) / 2;
        if (rx > ellipse.strokeWidth) {
          rx -= ellipse.strokeWidth / 2;
        }
        if (ry > ellipse.strokeWidth) {
          ry -= ellipse.strokeWidth / 2;
        }
        ellipse.set({ rx: rx, ry: ry });

        if (origX > x) {
          ellipse.set({ originX: 'right' });
        } else {
          ellipse.set({ originX: 'left' });
        }
        if (origY > y) {
          ellipse.set({ originY: 'bottom' });
        } else {
          ellipse.set({ originY: 'top' });
        }

        this.canvas.requestRenderAll();

      } else if (this._curMode === this._modes.rect) {
        const { x, y } = this.canvas.getPointer(e);

        if (origX > x) {
          rect.set({ left: Math.abs(x) });
        }
        if (origY > y) {
          rect.set({ top: Math.abs(y) });
        }

        rect.set({ width: Math.abs(origX - x) });
        rect.set({ height: Math.abs(origY - y) });

        this.canvas.requestRenderAll();
      }
    });

    this.canvas.on('mouse:up', () => {
      mouseDown = false;

      if (this._curMode === this._modes.line) {
        line.setCoords();
      } else if (this._curMode === this._modes.ellipse) {
        ellipse.setCoords();
      } else if (this._curMode === this._modes.rect) {
        rect.setCoords();
      }
    });
  }

  _resetAllMode() {
    // Fabric.js
    this.canvas.isDrawingMode = false;
    this.canvas.defaultCursor = 'default';
    this.canvas.hoverCursor = 'default';

    this._preventAllControl();
  }

  _preventAllControl() {
    this.canvas.getObjects().forEach(obj => {
      // 可交互文本对象
      if (obj instanceof fabric.IText) {
        obj.exitEditing();
      }

      // 固定位置与缩放
      obj.lockMovementX = true;
      obj.lockMovementY = true;
      obj.lockScalingX = true;
      obj.lockScalingY = true;
      obj.lockRotation = true;
      obj.hasControls = obj.hasBorders = false;
    });
  }
}

new Main();
```
