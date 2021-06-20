---
toc: true
categories:
  - Python
tags:
  - Module
title: pip 包管理器及虚拟环境（venv）
date: 2021-06-20
description: Python pip 及 venv
---

## 版本管理

`pip list -o`
:  查看已过时（可更新）的软件包。

`pip install -U <package>`
:  更新软件包。

`pip freeze [-l] > requirements.txt`
:  导出软件包及其版本以 requirements 格式写入文件。

`pip install -r requirements.txt`
:  从 requirements 格式文件中安装软件包。

`pip list --outdated --format=freeze | grep -v '^\-e' | cut -d = -f 1  | xargs -n1 pip install -U`
:  更新所有已过时的软件包。

<!--more-->

## 安装删除软件包

`pip install <package>`
:  安装指定软件包。

`pip uninstall <package>`
:  卸载指定软件包。

`pip list`
:  查看当前已安装的软件包。

## 帮助

`pip help`
:  查看可用命令及通用选项。

`pip help install`
:  查看安装命令的可用选项。

## 虚拟环境 venv

`python3 -m venv --help`
:  查看可用参数。

`python3 -m venv venv`
:  使用 Python 内置模块 `venv` 创建一个虚拟环境，通常创建的虚拟环境就被命名为 `venv`（比如 PyCharm）。

`source venv/bin/activate`
:  激活虚拟环境。

`deactivate`
:  退出虚拟环境。

`rm -rf venv`
:  删除虚拟环境。

### 访问全局安装

`python3 -m venv venv --system-site-packages`
:  使虚拟环境可访问全局软件包。

`pip list -l` 或 `pip freeze -l`
:  仅列出在虚拟环境下安装的软件包。
