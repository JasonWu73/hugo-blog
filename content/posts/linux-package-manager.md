---
toc: true
categories:
  - Linux
tags:
  - Package Manager
title: Linux 包管理器（包含 MacOS）
date: 2021-06-22
description: Linux 包管理器
---

## CentOS/RHEL（Red Hat Enterprise Linux）包（RPM）管理器

YUM（Yellowdog Updater Modified）
:  基于 RPM（RedHat Package Manager）Linux 系统的包管理器。

### 查找软件包

`yum search <package>`
:  查找软件包。

`yum info <package>`
:  查看软件包信息。

`yum deplist <package>`
:  查看软件包依赖。

<!--more-->

### 查看安装文件

`yum list installed`
:  列出所有已安装的软件包。

`yum provides <file>`
:  查看文件所属的软件包。

`rpm -ql <package>`
:  列出软件包的安装文件。

### 安装软件包

`yum install -y <package>`
:  安装软件包。

`yum reinstall -y <package>`
:  重新安装软件包。

#### 手动安装（`.rpm`）

`rpm -i <package_file.rpm>`
:  安装软件包。

`yum deplist <package_file.rpm>`
:  列出软件包依赖。

> 像 `.rpm`、`.deb` 这类文件的目的是为了将下载和安装独立开来，但这也导致用户必须手动识别并安装软件包的相关依赖。
> 
> 下载软件包有两种方式：
> 
> - 从仓库下载：
>     - `yum install -y yum-utils`
>     - `yumdownloader <package>`
> - 从非仓库下载：
>     - `yum install -y wget`
>     - `wget https://some_website/package_file.rpm`

### 卸载软件包

`yum remove -y <package>`
:  卸载软件包。

### 更新软件包

`yum check-update`
:  检查所有可用更新。

`yum update [<package>]`
:  更新所有或指定软件包。

### 清理

`yum clean all`
:  删除所有缓存（`/var/cache/yum/`）。

`yum autoremove -y`
:  卸载自动下载的仅用于依赖，且目前不再需要的软件包。

## Ubuntu/Debian 包（deb）管理器

- `apt-get`：安装、卸载和更新软件包
- `apt-cache`：操作仓库索引文件，如查询软件包
- `add-apt-repository`：添加额外的仓库
- `dpkg`：更低级别的软件包操作

### 查找软件包

`apt-cache search <package>`
:  查找软件包。

`apt-cache show <package>`
:  查看软件包信息。

`apt-cache depends <package>`
:  查看软件包依赖。

### 查看安装文件

`dpkg -l`
:  列出所有已安装的软件包。

`dpkg -S <file>`
:  查看文件所属的软件包。

`dpkg -L <package>`
:  列出软件包的安装文件。

### 安装软件包

`apt-get install -y <package>`
:  安装软件包。

`apt-get install --reinstall <package>`
:  重新安装软件包。

#### 手动安装（`.deb`）

`dpkg -i <pakcage_file.deb>`
:  安装软件包。

`dpkg -I <package_file.deb>`
:  查看软件包信息（其中 `Depends` 列出了依赖）。

### 卸载软件包

`apt-get remove -y <package>`
:  卸载软件包，但保留配置文件。

`apt-get purge -y <package>`
:  卸载软件包，同时删除配置文件。

### 更新软件包

`apt-get update`
:  更新仓库索引文件。

`apt-get upgrade --just-print`
:  检查所有可用更新。

`apt-get install [--only-upgrade] -y <package>`
:  更新指定软件包。

`apt-get upgrade -y`
:  更新所有软件包。

### 清理

`apt-get clean`
:  删除所有缓存（`/var/cache/apt/archives/`）。

`apt-get autoclean`
:  仅删除不再被下载的缓存。

`apt-get autoremove`
:  卸载自动下载的仅用于依赖，且目前不再需要的软件包。

## MacOS 包管理器（Homebrew）

### 查找软件包

`brew search <package>`
:  查找软件包。

`brew info <package>`
:  查看软件包信息。

`brew deps --tree <package>`
:  查看软件包依赖树形关系。

### 查看安装文件

`brew list`
:  列出所有已安装的软件包。

`ls -l <file>`
:  查看文件所属的软件包。

`brew list [-v] <package>`
:  列出软件包的安装文件。

`brew deps --tree --installed [<package>]`
:  查看所有已安装或指定软件包的依赖树形关系。

### 安装软件包

`brew install [--cask] <package>`
:  安装软件包。

`brew reinstall <package>`
:  重新安装软件包。

### 卸载软件包

`brew uninstall <package>`
:  卸载软件包。

### 更新软件包

`brew update`
:  获取更新。

`brew outdated`
:  检查所有可用更新。

`brew upgrade [<package>]`
:  更新所有或指定软件包。

### 清理

`brew cleanup`
:  删除过期锁文件、下载和旧版本软件。

`brew autoremove`
:  卸载自动下载的仅用于依赖，且目前不再需要的软件包。
