---
toc: true
categories:
  - "HTML & CSS"
tags:
  - "layout"
series:
  - "CSS Docs"
title: "CSS 一维布局：Flexbox"
date: "2021-06-16"
description: "CSS 一维布局 Flexbox"
---

![](/img/css-flexbox-overview.jpg)

## Flexbox Container

```css
flex-direction: row | row-reverse | column | column-reverse;

flex-wrap: nowrap | wrap | wrap-reverse;

justify-content: flex-start | flex-end | center | space-between | space-around | sapce-evenly;

align-items: stretch | flex-start | flex-end | center | baseline;

align-content: stretch | flex-start | flex-end | center | space-between | space-around;
```

## Flexbox Items

*Flexbox 借助 `margin` 来调整 Gutter。*

```css
align-self: auto | stretch | flex-start | flex-end | center | baseline;

order: 0 | <integer>;

flex: 0 1 auto | <int> <int> <len>;
flex-grow: 0 | <integer>;
flex-shrink: 1 | <integer>;
flex-basis: auto | <length>;
```
