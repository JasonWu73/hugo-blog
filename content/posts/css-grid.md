---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- HTML & CSS
tags:
- layout
series:
- CSS Docs
title: CSS 布局：Grid
date: 2021-06-16T10:26:13+08:00
description: CSS 二维布局——网格。
---

> {{<reprint>}}

{{< param description >}}

<br>{{< img src="/images/posts/css-grid-overview.jpg" title="CSS Grid 概述图" caption="Grid 结构及名词" alt="Grid 结构及名词" position="center" >}}

## 网格容器

```css
/* 定义显式网格航向（Grid Track） */
grid-templates
grid-template-rows
grid-template-columns
grid-template-areas

/* 定义网格 Gutter */
grid-gap
grid-row-gap
grid-column-gap

/* 定义网格项（Grid Item）的对齐方向 */
justify-items
align-items

/* 定义网格航向的对齐方向 */
justify-content
align-content

/* 定义隐式网格航向 */
grid-auto-rows
grid-auto-columns
grid-auto-flow
```

### 网格单位

数值单位：

- `fr`，类比 Flexbox 的 `flex-grow`
    - 用于延伸剩余的所有可用空间
        - `grid-template-columnsn: repeat(2, 150px) 1fr`
    - 用于按比例划分可用空间
        - `grid-template-columnsn: 1fr 1fr 2fr`
        - `grid-template-columnsn: 50% 1fr 2fr`

<br>关键字单位：

- `max-content`，最大内容尺寸
    - `grid-template-columns: max-content 1fr 1fr max-content`
- `min-content`，最小内容尺寸
    - `grid-template-rows: repeat(2, min-content);`
    - `grid-template-columns: min-content 1fr 1fr min-content`
- `auto-fill`，使用所有可用空间，自动划分网格航向（多余则为空）
    - `grid-template-columns: repeat(auto-fill, 100px);`
- `auto-fit`，仅使用需要的空间，自动划分网格航向
    - `grid-template-columns: repeat(auto-fill, 100px);`
    - `grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));`

<br>函数：

- `repeat(num, unit)`，定义重复值
    - `grid-template-rows: repeat(3, 150px)`
    - `grid-template-rows: repeat(2, 150px) 300px`
- `minmax(v1, v2)`，代表取值最小要 `v1`，最大不能超过 `v2`
    - `grid-template-rows: repeat(2, minmax(150px, min-content));`

### 显式与隐式网格

```css
/* 定义隐式网格航向是行或列 */
grid-auto-flow: row | column;

/* 定义显式网格航向尺寸 */
grid-template-rows: repeat(2, 150px);
grid-template-columns: repeat(2, 1fr);

/* 定义隐式网格航向尺寸 */
grid-auto-rows: 80px;
grid-auto-columns: .5fr;
```

## 网格项

```css
/* 定义网格区 */
grid-area
grid-row
grid-column

/* 定义网格行 */
grid-row
grid-row-start
grid-row-end

/* 定义网格列 */
grid-column
grid-column-start
grid-coumn-end

/* 定义网格项对齐方式 */
justify-self
align-self

/* 定义网格项排序顺序 */
order
```

### 定位与跨越

```css
/* 将网格项放于指定位置 */
grid-row-start: 1;
grid-row-end: 2;
grid-column-start: 3;
grid-column-end: 4;

/* 将网格项放于指定位置（简写，推荐）*/
grid-row: 1 / 2;
grid-column: 3 / 4;

/* 将网格项放于指定位置（简写）*/
grid-area: 1 / 3 / 2 / 4;

/* 跨越多个网格单元 */
grid-column: 1 / 3;
grid-column: 1 / span 2;
grid-column: 1 / -1;
```

- 在未明确定位时，当跨越已被明确定位的网格项，则该跨越项会移到最后一行
- 在同时指定定位和跨越时，则一个单元格可同时被多个网格项填充
    - `z-index` 可控制哪个显示在上层

## 命名网格线

```css
grid-template-rows: [header-start] 100px [header-end main-start] 100px [main-end];

gird-template-columns: repeat(2, [col-start] 1fr [col-end]) 100px [grid-end];

grid-row: main-start / main-end;

grid-column: col-start 2 / grid-end;
```

<br>{{< img src="/images/posts/css-naming-grid-lines.jpg" title="CSS Naming Grid Lines" caption="Name Grid Lines" alt="Name Grid Lines" position="center" >}}

## 命名网格区

{{< codes "未命名前" "命名网格线" "命名网格区" "命名网格区（留空）" html >}}
{{< code >}}
  ```css
  .container {
    width: 1000px;
    margin: 30px auto;

    display: grid;
    grid-template-rows: 100px 200px 400px 100px;
    grid-template-columns: repeat(3, 1fr) 200px;
    grid-gap: 30px;
  }

  .container > div {
    background: crimson;
    color: #fff;
    font-size: 30px;
  }

  .header {
    grid-column: 1 / -1;
  }

  .sidebar {
    grid-row: 2 / span 2;
  }

  .main-content {
    grid-column: 2 / -1;
  }

  .footer {
    grid-column: 1 / -1;
  }
  ```
{{< /code >}}

{{< code >}}
  ```css
  .container {
    width: 1000px;
    margin: 30px auto;

    display: grid;
    grid-template-rows: [header-start] 100px [header-end box-start] 200px [box-end main-start] 400px [main-end footer-start] 100px [footer-end];
    grid-template-columns: repeat(3, [col-start] 1fr [col-end]) 200px [grid-end];
    grid-gap: 30px;
  }

  .container > div {
    background: crimson;
    color: #fff;
    font-size: 30px;
  }

  .header {
    grid-column: col-start 1 / grid-end;
  }

  .sidebar {
    grid-row: box-start / main-end;
  }

  .main-content {
    grid-column: col-start 2 / grid-end;
  }

  .footer {
    grid-column: col-start 1 / -1;
  }
  ```
{{< /code >}}

{{< code >}}
  ```css
  .container {
    width: 1000px;
    margin: 30px auto;

    display: grid;
    grid-template-rows: 100px 200px 400px 100px;
    grid-template-columns: repeat(3, 1fr) 200px;
    grid-gap: 30px;
    grid-template-areas: "head head head head"
                         "box box box side"
                         "main main main side"
                         "foot foot foot foot";
  }

  .container > div {
    background: crimson;
    color: #fff;
    font-size: 30px;
  }

  .header {
    grid-area: head;
  }

  .sidebar {
    grid-area: side;
  }

  .main-content {
    grid-area: main;
  }

  .footer {
    grid-area: foot;
  }
  ```
{{< /code >}}

{{< code >}}
  ```css
  .container {
    width: 1000px;
    margin: 30px auto;

    display: grid;
    grid-template-rows: 100px 200px 400px 100px;
    grid-template-columns: repeat(3, 1fr) 200px;
    grid-gap: 30px;
    grid-template-areas: ". head head ."
                         "box-1 box-2 box-3 side"
                         "main main main side"
                         "foot foot foot foot";
  }

  .container > div {
    background: crimson;
    color: #fff;
    font-size: 30px;
  }

  .header {
    grid-area: head;
  }

  .box-1 {
    grid-area: box-1;
  }

  .box-2 {
    grid-area: box-2;
  }

  .box-3 {
    grid-area: box-3;
  }

  .sidebar {
    grid-area: side;
  }

  .main-content {
    grid-area: main;
  }

  .footer {
    grid-area: foot;
  }
  ```
{{< /code >}}

{{< code >}}
  ```html
  <!DOCTYPE html>
  <html>
    <head>
      <meta charset="UTF-8">
      <title>Grid</title>
      <link rel="stylesheet" href="style.css">
    </head>
    <body>
      <div class="container">
        <div class="header">Header</div>
        <div class="sidebar">Sidebar</div>
        <div class="box-1">Box-1</div>
        <div class="box-2">Box-2</div>
        <div class="box-3">Box-3</div>
        <div class="main-content">Main Content</div>
        <div class="footer">Footer</div>
      </div>
    </body>
  </html>
  ```
{{< /code >}}
{{< /codes >}}

## 对齐网格项

网格项相对于网格单元或网格区的对齐。

```css
/* 在网格容器中整体定义 */
/* 水平方向 */
justify-items: stretch | center | end | start;
/* 垂直方向 */
align-items: stretch | center | end | start;

/* 在网格项单独定义 */
/* override 网格容器的 `justify-items` */
justify-self: stretch | center | end | start;
/* override 网格容器的 `align-items` */
align-self: stretch | center | end | start;
```

## 对齐网格航向

网格航向相对于网格容器的对齐。

```css
/* 在网格容器中定义 */
/* 水平方向 */
justify-content: center | end | start | space-between | space-around | space-evenly;
/* 垂直方向 */
align-content: center | end | start | space-between | space-around | space-evenly;
```

{{< alert theme="info" dir="ltr" >}}
默认情况下，网格会遵循 HTML 源代码顺序，即当剩余网格区不足以放下网格项时，自动留空并创建新的网格航向。

可以使用 `dense` 关键字（`grid-auto-flow: row dense`）修改该策略，即忽略网格项在源代码中的顺序，保持密集，尽量不留空。
{{< /alert >}}
