---
toc: true
categories:
  - HTML & CSS
tags:
  - Sass
title: Sass 7-1 Pattern
date: 2021-06-03
description: Sass 目录设计与 CSS（SCSS）代码示例
---

Sass 目录结构设计与 CSS（SCSS）实用代码。

<!--more-->

## Sass 7-1 Pattern

```
sass/
|
|– abstracts/
|   |– _variables.scss   # Sass Variables
|   |– _functions.scss   # Sass Functions
|   |– _mixins.scss      # Sass Mixins
|   |– _helpers.scss     # Class & placeholders helpers
|
|– base/
|   |– _animations.scss
|   |– _base.scss
|   |– _typograhy.scss
|   |– _utilities.scss 
|   ...                  # Etc…
|
|– components/
|   |– _buttons.scss     # Buttons
|   |– _carousel.scss    # Carousel
|   |– _cover.scss       # Cover
|   |– _dropdown.scss    # Dropdown
|   ...                  # Etc…
|
|– layout/
|   |– _navigation.scss  # Navigation
|   |– _grid.scss        # Grid system
|   |– _header.scss      # Header
|   |– _footer.scss      # Footer
|   |– _sidebar.scss     # Sidebar
|   |– _forms.scss       # Forms
|   ...                  # Etc…
|
|– pages/
|   |– _home.scss        # Home specific styles
|   |– _contact.scss     # Contact specific styles
|   ...                  # Etc…
|
|– themes/
|   |– _theme.scss       # Default theme
|   |– _admin.scss       # Admin theme
|   ...                  # Etc…
|
|– vendors/
|   |– _bootstrap.scss   # Bootstrap
|   |– _jquery-ui.scss   # jQuery UI
|   ...                  # Etc…
|
`– main.scss             # Main Sass file
```

## CSS（SCSS）实用代码

`sass/abstracts/_variables.scss`

```scss
$color-grey: #e3e3e3;
```

`sass/abstracts/_mixins.scss`

```scss
// 清除浮动元素
@mixin clearfix {
  &::after {
    content: '';
    display: table;
    clear: both;
  }
}

// Electron App，不可选中
@mixin no-selection {
  user-select: none;
}

// Electron App，禁止拖拽
@mixin no-drag {
  -webkit-user-drag: none;
}

// 将黑色 SVG 图片内容转换为白色
@mixin filter-white {
  filter: invert(100%);
}

// 绝对定位下的垂直居中
@mixin absoluteCenter {
  position: absolute;
  left: 0;
  right: 0;
  margin: auto;
  z-index: 1;
}
```

`sass/abstracts/_helpers.scss`

```scss
// 隐藏元素
.hidden {
  display: none !important;
}
```

`sass/base/_base.scss`

```scss
@use '../abstracts/mixins' as *;

*,
*::after,
*::before {
  margin: 0;
  padding: 0;
  box-sizing: inherit;
}

html {
  font-size: 10px / 16px * 100%; // 定义 1rem = 10px
}

body {
  font: caption; // Electron App，使用系统字体
  font-size: 1.6rem;
  box-sizing: border-box;
}

img {
  @include no-drag; // Electron App，防止图标可被拖拽
}
```

`sass/components/_widgets.scss`

```scss
@use '../abstracts/variables' as *;
@use '../abstracts/mixins' as *;

// 可在内容区中自定义长度的竖线
.vertical_bar {
  float: right;
  border-right: .1rem solid $color-grey;
  height: 100%;
}

// 带箭头的提示框
.tooltip {
  position: relative;

  .tip {
    @include absoluteCenter;

    display: none;
    width: 12.5rem;
    padding: .5rem;
    background: black;
    color: white;

    &:before {
      content: '';
      display: block;
      width: 0;
      height: 0;
      border-left: .8rem solid transparent;
      border-right: .8rem solid transparent;
      border-bottom: .8rem solid black;
      transform: translateY(-1.5rem);

      @include absoluteCenter;
      top: 1rem;
    }
  }

  &:hover .tip {
    display: block;
  }
}
```

`sass/_main.scss`

```scss
@use './abstracts/helpers';

@use './base/base';

@use 'components/widgets';
```
