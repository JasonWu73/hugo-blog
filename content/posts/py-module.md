---
toc: true
categories:
  - Python
tags:
  - Module
title: Python 模块
date: 2021-06-19
description: Python 导入模块，sys.path 及 __main__
---

Python 中一个 `.py` 文件就是一个模块。Python 支持多种模块导入语法，此外通过 `if __name__ == '__main__'` 可避免在导入模块时，直接执行被导入模块中的所有代码。

Python 之所以能找到并导入模块（文件）的原因在于 `sys.path`。

```py
import sys

print(sys.path)
```

其中 `sys.path` 主要由以下几部分组成：

1. 当前脚本所在目录
2. 环境变量 `PYTHONPATH`
3. Python 标准库所在目录
4. 第三方包 `site-packages`

<!--more-->

当导入的模块没有在 `sys.path` 中时，我们可以在导入模块**之前**手动添加所需路径：

```py
import sys

sys.path.append('/Users/jasonwu/Desktop')

from my_module import test

print(test)

print(sys.path)
```

此外 Python 还允许我们通过环境变量添加所需路径。比如 Linux 下，添加环境变量 `PYTHONPATH`：

- `vi ~/.bash_profile` 或 `vim ~/.zprofile`
- `. ~/.bash_profile` 或 `source ~/.zprofile`

```shell
export PYTHONPATH="/Users/jasonwu/Desktop"
```

## 导入模块

导入语法：

```py
# 导入整个模块
import module_name
import module_name as alias

# 导入特定方法和变量
from module_name import name1 as alias1, ..., nameN

# 导入所有方法和变量（极不推荐）
from module_name import *
```

## `if __name__ == '__main__'`

- 当将 `.py` 文件作为主文件（单独脚本）运行时，`__name__` 的值为 `__main__`
- 当将 `.py` 文件作为模块导入时，`__name__` 的值为被导入模块的文件名（不包含后 `.py`）

故 `if __name__ == '__main__':` 代表只在作为主文件运行时才会被执行的代码。

```py
def main():
  pass


if __name__ == '__main__':
  main()
```
