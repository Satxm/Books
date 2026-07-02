---
title: Debian 系统软件源更换指南
published: 2025-2-25 09:43:01
description: 'Debian 系统软件源更换指南'
image: api
tags: [Debian,Sources]
category: 'Linux'
draft: false 
lang: ''
---

本指南将帮助您将 Debian 系统的默认软件源更换为国内镜像源，以提升软件包下载速度。

## 使用说明

:::warning[警告]

**操作前请做好相应备份。**

- 备份源文件：在修改任何系统文件前，强烈建议您先进行备份，以便在出现问题时能够快速恢复。

```bash
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo cp -r /etc/apt/sources.list.d /etc/apt/sources.list.d.bak
```

- 确认系统版本：请确保您使用的源配置与您的 Debian 系统版本（代号）相匹配。您可以使用以下命令查看：

```bash
lsb_release -c
# 或
cat /etc/os-release
```
:::

## 自动化替换方法

一般情况下，将 `/etc/apt/sources.list` 或 `/etc/apt/sources.list.d/debian.sources` 文件中 Debian 默认的源地址 `http://deb.debian.org/` 替换为 `http://mirrors.ustc.edu.cn` 即可。

可以使用如下命令：

```bash title="sources.list 格式"
# 替换主源
sudo sed -i 's|http://deb.debian.org|http://mirrors.ustc.edu.cn|g' /etc/apt/sources.list

# 替换安全更新源
sudo sed -i -e 's|security.debian.org/\? |security.debian.org/debian-security |g' \
      -e 's|security.debian.org|mirrors.ustc.edu.cn|g' \
      -e 's|deb.debian.org/debian-security|mirrors.ustc.edu.cn/debian-security|g' \
      /etc/apt/sources.list
```

```bash title="DEB822 格式"
# 替换主源
sudo sed -i 's|http://deb.debian.org|http://mirrors.ustc.edu.cn|g' /etc/apt/sources.list.d/debian.sources

# 替换安全更新源
sudo sed -i -e 's|security.debian.org/\? |security.debian.org/debian-security |g' \
      -e 's|security.debian.org|mirrors.ustc.edu.cn|g' \
      -e 's|deb.debian.org/debian-security|mirrors.ustc.edu.cn/debian-security|g' \
      /etc/apt/sources.list.d/debian.sources
```

目前使用 DEB822 格式的 Debian 分发仅有容器镜像，且其安全更新源默认设置为 `http://deb.debian.org/debian-security`，因此以上命令会同时替换 Debian 官方源和安全更新源。

:::tip[提示]

[什么是 DEB822 (.sources) 文件格式？]

自新版本的 Debian 与 Ubuntu 起，例如：
- Debian 12 的容器镜像
- Ubuntu 24.04

默认预装的系统中 APT 的系统源配置文件不再是传统的 `/etc/apt/sources.list`。
传统格式（又被称为 One-Line-Style 格式）类似如下：

```vim
deb http://mirrors.ustc.edu.cn/debian/ bookworm main contrib
```

新的 DEB822 格式自 APT 1.1（2015 年发布）起支持，后缀为 `.sources`，存储在 `/etc/apt/sources.list.d/` 目录下，格式类似如下：

```yaml
Types: deb
URIs: https://mirrors.ustc.edu.cn/debian
Suites: bookworm
Components: main contrib
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg
```

在切换软件源时，**需要根据实际情况选择对应的格式进行修改**。

关于 DEB822 格式的设计考虑，可以参考[官方文档（英文）](https://repolib.readthedocs.io/en/latest/deb822-format.html)。

:::

当然也可以直接编辑 APT 源文件（需要使用 sudo）。以下是参考配置内容：

## Debian 12


```vim title="sources.list 格式"
# 默认注释了源码仓库，如有需要可自行取消注释
deb http://mirrors.ustc.edu.cn/debian bookworm main contrib non-free non-free-firmware
# deb-src http://mirrors.ustc.edu.cn/debian bookworm main contrib non-free non-free-firmware
deb http://mirrors.ustc.edu.cn/debian bookworm-updates main contrib non-free non-free-firmware
# deb-src http://mirrors.ustc.edu.cn/debian bookworm-updates main contrib non-free non-free-firmware

# backports 软件源，请按需启用
# deb http://mirrors.ustc.edu.cn/debian bookworm-backports main contrib non-free non-free-firmware
# deb-src http://mirrors.ustc.edu.cn/debian bookworm-backports main contrib non-free

# debian-security 软件源
deb http://mirrors.ustc.edu.cn/debian-security/ bookworm-security main contrib non-free non-free-firmware
# deb-src http://mirrors.ustc.edu.cn/debian-security/ bookworm-security main contrib non-free non-free-firmware
```

```yaml title="DEB822 格式"
Types: deb
URIs: http://mirrors.ustc.edu.cn/debian
Suites: bookworm bookworm-updates bookworm-backports
Components: main contrib non-free non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg

Types: deb
URIs: http://mirrors.ustc.edu.cn/debian-security
Suites: bookworm-security
Components: main contrib non-free non-free-firmware
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg
```

## Debian 11
```vim title="sources.list 格式"
# 默认注释了源码仓库，如有需要可自行取消注释
deb http://mirrors.ustc.edu.cn/debian bullseye main contrib non-free
# deb-src http://mirrors.ustc.edu.cn/debian bullseye main contrib non-free
deb http://mirrors.ustc.edu.cn/debian bullseye-updates main contrib non-free
# deb-src http://mirrors.ustc.edu.cn/debian bullseye-updates main contrib non-free

# backports 软件源，请按需启用
# deb http://mirrors.ustc.edu.cn/debian bullseye-backports main contrib non-free
# deb-src http://mirrors.ustc.edu.cn/debian bullseye-backports main contrib non-free

# debian-security 软件源
deb http://mirrors.ustc.edu.cn/debian-security/ bullseye-security main contrib non-free
# deb-src http://mirrors.ustc.edu.cn/debian-security/ bullseye-security main contrib non-free
```

```yaml title="DEB822 格式"
Types: deb
URIs: http://mirrors.ustc.edu.cn/debian
Suites: bullseye bullseye-updates bullseye-backports
Components: main contrib non-free
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg

Types: deb
URIs: http://mirrors.ustc.edu.cn/debian-security
Suites: bullseye-security
Components: main contrib non-free
Signed-By: /usr/share/keyrings/debian-archive-keyring.gpg
```
