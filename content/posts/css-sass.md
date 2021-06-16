---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- HTML & CSS
tags:
- sass
series:
- CSS Samples
title: Sass 7-1 Pattern
date: 2021-06-03T15:01:57+08:00
description: Sass 目录结构设计与 CSS 通用样式代码。
---

> {{<reprint>}}

{{< param description >}}

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

## 实用 CSS

```scss:sass/abstracts/_variables.scss
$color-grey: #e3e3e3;
```

```scss:sass/abstracts/_mixins.scss
@mixin clearfix {
  &::after {
    content: '';
    display: table;
    clear: both;
  }
}

@mixin no-selection {
  user-select: none;
}

@mixin no-drag {
  -webkit-user-drag: none;
}

@mixin filter-white {
  filter: invert(100%);
}

@mixin absoluteCenter {
  position: absolute;
  left: 0;
  right: 0;
  margin: auto;
  z-index: 1;
}
```

```scss:sass/abstracts/_helpers.scss
.hidden {
  display: none !important;
}
```

```scss:sass/base/_base.scss
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

```scss:sass/components/_widgets.scss
@use '../abstracts/variables' as *;

.vertical_bar {
  float: right;
  border-right: .1rem solid $color-grey;
  height: 100%;
}
```

```scss:sass/_main.scss
@use './abstracts/helpers';

@use './base/base';

@use 'components/widgets';
```
