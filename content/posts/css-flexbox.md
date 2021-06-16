---
author: 吴仙杰
authorEmoji: 🧑🏻‍💻
categories:
- HTML & CSS
tags:
- layout
series:
- CSS Docs
title: CSS 布局：Flexbox
date: 2021-06-16T14:10:46+08:00
description: CSS 一维布局——伸缩布局盒。
---

> {{<reprint>}}

{{< param description >}}

<br>{{< img src="/images/posts/css-flexbox-overview.jpg" title="CSS Flexbox 概述图" caption="Flexbox 主轴与交叉轴" alt="Flexbox 主轴与交叉轴" position="center" >}}

## Flexbox Container

```css
flex-direction: row | row-reverse | column | column-reverse;

flex-wrap: nowrap | wrap | wrap-reverse;

justify-content: flex-start | flex-end | center | space-between | space-around | sapce-evenly;

align-items: stretch | flex-start | flex-end | center | baseline;

align-content: stretch | flex-start | flex-end | center | space-between | space-around;
```

## Flexbox Items

```css
align-self: auto | stretch | flex-start | flex-end | center | baseline;

order: 0 | <integer>;

flex: 0 1 auto | <int> <int> <len>;
flex-grow: 0 | <integer>;
flex-shrink: 1 | <integer>;
flex-basis: auto | <length>;
```

{{< notice info "Flexbox Gutter" >}}
Flexbox 借助 `margin` 来调整 Gutter。
{{< /notice >}}
