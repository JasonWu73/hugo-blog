---
toc: true
categories:
  - Linux
tags:
  - File System
title: Linux 文件系统
date: 2021-06-28
description: Linux 目录结构及访问
---

## 目录结构

| 目录名 | 描述 |
| :---: | :---: |
| `/boot` | 启动器程序所需文件，如 `grub.cfg` |
| `/root` | root 用户主目录 |
| `/dev` | 系统设备（如磁盘、光驱、扬声器、闪存盘、键盘等）
| `/usr` | 系统级软件安装目录，存放由操作系统或包管理器安装的软件 |
| `/usr/local` | 系统级软件安装目录，存放会被拆分为多个目录（如传统的编译安装 `./configure && make && sudo make install`），且对所有用户均可用的软件。单用户可用则存放于 `~/.local` |
| `/opt` | 系统级软件安装目录，存放完全独立的单目录软件。单用户可用则存放于 `~/.local/opt` |
| `/etc` | 存放系统级软件的配置文件 |
| `/bin` -> `/usr/bin` | 用户级命令 |
| `/sbin` -> `/usr/sbin` | 系统级命令 |
| `/proc` | 运行中的进程（仅存在于内存中） |
| `/lib` -> `/usr/lib` | 软件所需的 C 语言库文件（`strace -e open pwd`）|
| `/tmp` | 临时文件 |
| `/home` | 用户目录（非 root 用户）|
| `/var` | 系统日志 |
| `/run` | 系统守护进程，保存运行中的临时文件，如 PID 文件 |
| `/mnt` | 挂载外部文件系统（如 NFS）|
| `/media` | 挂载光驱（cdrom） |

<!--more-->

## 通配符

| 符号 | 说明 |
| :---: | :---: |
| `*` | 代表零个或多个字符 |
| `?` | 代表一个字符 |
| `[]` | 代表某个范围内的字符 |
| `\` | 转义字符 |
| `^` | 行的开始 |
| `$` | 行的结尾 |

通配符使用示例：

- `touch {a..c}{1..3}`：创建9个文件
- `ls -ltr ab*`：仅列出文件名以 `ab` 开头的文件
- `ls -ltr *[a-c]*`：仅列出文件名包含 `a`、`b` 或 `c` 的文件
- `ls -ltr *[ac]*`：仅列出文件名包含 `a` 或 `c` 的文件
- `rm -rf *3`：删除所有以 `3` 结尾的文件

### 大括号扩展

| 符号 | 说明 |
| :---: | :---: |
| `{seq1,seq2}` | 固定序列 |
| `{0..3}` | 递增序列 |

大括号扩展用于生成字符串：

- `echo {1,2}`：`1 2`
- `echo {1..3}`：`1 2 3`

## 软链接与硬链接

![](/img/linux-soft-hard-link.jpg)

inode
:  文件在硬盘上的指针或编号。

可通过 `ls -ltri` 查看 inode，例如 `89 -rw-rw-r--. 1 wxj wxj 18 6月  21 16:09 test`，其中 `89` 即为 inode。

软链接（Soft Link）
: 创建与原文件所在位置关联的链接文件（相当于一个占位符的新文件），类似 Windows 中的快捷方式。

软链接特点：

- 软链接 inode 与原文件**不同**
- 文件类型为 `l`，且 `->` 会指向原文件
- 删除或重命名原文件，将导致软链接将不可用（因为 inode 不同）
- 在同一位置创建新的同名文件，会恢复软链接，即链接到该新同名文件

硬链接（Hard Link）
:  除了与原文件存在链接关系外（相当于指向同一硬盘位置的别名文件），其他和文件拷贝没有任何区别。

硬链接特点：

- 仅当原文件和硬链接在同一分区时才可创建
- 硬链接 inode 与原文件**相同**
- 文件类型为 `-`，且没有 `->` 指向
- 移动或重命名原文件都不会破坏链接关系，即硬连接依然可用（因为 inode 相同）
- 删除原文件不会影响硬连接，但在同一位置创建新的同名文件后，也不恢复硬链接的链接关系，即他们是不同的文件

`ln <original file>`
:  创建硬链接。

`ln -s <original file>`
:  创建软链接。

## `ls` 格式

`ls -ltrah`
:  按修改时间升序列出文件（list），即按修改时间从小到大显示。

- `-l`：列出文件长格式
- `-t`：按修改时间从大到小显示（降序）
- `-r`：颠倒顺序显示
- `-a`：显示所有文件（以 `.` 开头的是隐藏文件）
- `-h`：以人可读的方式显示文件大小，后缀有：
    - 无，默认：Bytes
    - `k`：Kilobytes
    - `M`：Megabytes
    - `G`：Gigabytes

| Type | # of Links | Owner | Group | Size | Month | Day | Time | Name |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| drwxr-xr-x. | 2 | root | root | 27 | May | 27 | 15:56 | grub |
| lrwxrwxrwx. | 1  | root | root | 7 | May | 27 | 15:56 | bin -> usr/bin |

其中 `drwxr-xr-x` 需分为四组查看：

1. `d`：文件类型
    - `-`：文件
    - `d`：目录（Directory）
    - `l`：链接（Link）
    - `c`：特殊文件或设备文件（如挂载的设备文件）
    - `s`：Socket 文件
        - `sudo yum install -y nc`
        1. `nc -lkU test.socket`：创建 UDS（Unix Domain Socket）服务端，绑定 `test.scoket` Socket 文件
        2. `nc -U test.socket`：创建 UDS 客户端，与监听 `test.socket` Socket 文件的服务端建立链接
    - `p`：命名管道（Named pipe），或 FIFO（First In, First Out）
        - `ls | grep x`：创建管道（Unnamed pipe）
        - `mkfifo pipe1`：创建命名管道（Named pipe），之后可进行如下交互：
            1. 会话1执行 `cat pipe1`
            2. 会话2执行 `ls -ltr > pipe1`
            3. 会话1显示会话2 `ls -ltr` 的输出结果
    - `b`：Block device
2. `rwx`：所有者（Owner）
    - `-`：普通文件
    - `r`：可读（Read，`4`）
    - `w`：可写（Write，`2`）
    - `x`：可执行（Execute，`1`）
3. `r-x`：用户组（Group）
4. `r-x`：其他（Public）

## 查找文件

`find . -iname <characters>`
:  在当前目录中查找精确文件名（默认）的文件。默认精确搜索，结合通配符可实现模糊搜索。

- `-i`：忽略大小写

---

`yum install -y mlocate`
:  安装提供 `locate` 命令的软件包。 

`sudo updatedb`
:  立即更新预制数据库。

`locate -i <characters>`
:  在预制数据库中查找文件名包含指定字符的文件。本身就是模糊搜索。

- `-i`：忽略大小写

---

`find` vs `locate`：

- `locate` 使用定期更新的预制数据库，而 `find` 遍历文件系统。因此 `locate` 要比 `find` 更快
- 因为 `locate` 是查缓存（预制数据库），故想要新建文件立即被参与查找，则要手动刷新缓存（`updatedb`）。虽然系统自动会刷新缓存，但如果在自动刷新缓存前执行查询，则可能会导致结果不准确

## 切换目录

文件系统路径：

- 绝对路径：永远以 `/` 开头，代表从 root 目录开始
- 相对路径：不以 `/` 开头，代表相对于当前位置开始定位
    - `.`：当前目录
    - `..`：上级目录

`cd <dir>`
:  切换目录（change directory）。

`cd ..`
:  回到上级目录。

`cd ~` 或 `cd`
:  切换至用户主目录。

`cd -`
:  来回切换之前与当前目录。

## 创建文件

- `touch <new_file_1> ... <new_file_N>`
- `cp <old_file> <new_file>`
- `vi <new_file>`
    - `:wq`

## 创建目录

- `mkdir <new_dir_1> ... <new_dir_N>`

## 切换用户

`whoami`
:  显示当前用户。

`su -` 或 `su - root`
:  切换至 root 用户，需输入当前用户的密码。

`^D` 或 `exit`
:  退出当前用户。

## 查看 IP

`ip addr`
:  查看网卡信息。

`ip -s link`
:  查看网络接口的统计数据。

`ifconfig -a`
:  查看网卡信息。CentOS 7 开始已被弃用。

安装 `ifconfig`：

- `yum provides ifconfig`
- `yum install -y net-tools`

## 修改密码

1. `passwd [userid]`
2. 输入原密码
3. 输入新密码
4. 确认新密码

## 常用命令

`[sudo] !!`
:  再次执行上次命令（可指定 `sudo` 及额外可选项）。

`pwd`
:  显示当前所在目录的绝对路径（print working directory）。

`hostname`
:  显示主机名。

`^L` 或 `clear`
:  清屏。

`^U`
:  清除行。

`type <command>` 或 `command -V <command>`
:  查看命令的二进制文件。

`type -a <command>`
:  列出命令的所有二进制文件。
