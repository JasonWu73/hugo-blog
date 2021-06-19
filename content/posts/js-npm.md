---
toc: true
categories:
  - JavaScript
tags:
  - npm
title: Npm 包管理器
date: 2021-06-19
description: Npm Package Manager
---

## 语义化版本号

Npm 的语义化版本号由三组数字组成，格式为 `v<主版本号>.<次版本号>.<补丁版本号>`：

- 主版本号：发生不兼容变化
- 次版本号：增加、优化功能
- 补丁版本号：修复 Bug

## Npm 版本管理

- `^1.0.0`，默认，跟踪次版本或补丁版本号
- `~1.0.0`，仅跟踪补丁版本号，更加保守和稳定的选择

<!--more-->

`npm outdated`
:  根据语义版本号显示可用的新版本。

`npm update <package>`
:  更新符合语义版本号的可用新版本

## 安装删除依赖

`npm init [-y]`
:  自动创建 `package.json`，这是执行安装依赖的前置条件。

`npm install [-D | -g] <package[@v1.0.0]>`
:  安装指定依赖至目录 `node_modules`。

> 依赖安装完成后，默认会创建 `package-lock.json`，用于锁定当前项目所使用的具体版本，以便保证开发时所使用版本的一致性。
> 故 `package.json` 和 `package-lock.json` 文件都需要加入版本控制。

`npm install`
:  根据 `package.json` 安装所有依赖至当前目录 `node_modules`。

`npm uninstall [-g] <package>`
:  从目录 `node_modules` 中删除指定依赖。

## 查看已安装依赖

`npm ls [-g]`
:  查看已安装的依赖。

` npm root -g`
:  查看全局安装目录 `node_modules`。
