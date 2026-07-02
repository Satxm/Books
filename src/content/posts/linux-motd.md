---
title: Linux 系统登录信息 (MOTD) 定制指南
published: 2025-3-22 18:39:11
description: 'Linux 系统登录信息 (MOTD) 定制指南'
image: api
tags: []
category: 'Linux'
draft: false 
lang: ''
---

本指南将介绍如何修改 Linux 系统在用户通过 TTY 或 SSH 登录时显示的欢迎信息（Message of the Day, MOTD）。通过定制 MOTD，您可以展示系统信息、发布公告或进行安全提醒。

## 操作前重要提示

- 备份原文件：在修改任何系统文件前，强烈建议您先进行备份，以便在出现问题时能够快速恢复。

```bash
sudo cp /etc/motd /etc/motd.bak
```

- 系统差异：不同 Linux 发行版（如 Debian/Ubuntu 与 CentOS/RHEL）实现 MOTD 的机制可能不同。本指南主要基于 Debian/Ubuntu 系统，其使用 `pam_motd` 模块和 `/etc/update-motd.d/` 目录。

## 📜 静态 MOTD (`/etc/motd`)

这是最传统和简单的方法。`/etc/motd` 文件的内容会在用户成功登录后直接显示。

- 特点：内容为纯文本，无法执行脚本或显示动态信息（如实时内存使用率）。
- 适用场景：发布固定的公告、法律声明或简单的欢迎语。

修改方法
您可以直接使用文本编辑器修改该文件，或使用 `echo` 命令。

```bash 
echo "
Welcome to $(lsb_release -s -i) ($(uname -o)) Server!
" | sudo tee /etc/motd > /dev/null
```
注意：上述命令中的 $(lsb_release -s -i) 会在您执行命令的当下被解析并替换为文本（例如 "Debian"），然后写入文件。文件本身并不会在每次登录时都去执行这个命令。

输出结果（以 Debian 12 为示例）

```bash
Welcome to Debian (GNU/Linux) Server!
```

## ✨ 动态 MOTD (/etc/update-motd.d/)

这是现代 Debian/Ubuntu 系统推荐的方式，可以生成包含实时系统信息的欢迎界面。

- 特点：该目录下的所有可执行脚本会在用户登录时按文件名顺序自动运行，并将它们的输出组合成最终的 MOTD。
- 适用场景：显示内核版本、系统负载、内存使用、磁盘空间等动态信息。

核心机制
- 执行顺序：脚本按文件名的字典序执行。通常以两位数字开头（如 00-header, 10-sysinfo），数字越小，执行越早。
- 权限要求：脚本必须具有可执行权限 (chmod +x)。

配置示例
下面将创建两个脚本，分别用于显示系统头信息和系统状态。

### 1. 创建系统头信息脚本 (00-header)

此脚本用于显示操作系统和内核的基本信息。

```bash
sudo tee /etc/update-motd.d/00-header > /dev/null << 'EOF'
#!/bin/sh

# 获取发行版信息
if [ -r /etc/os-release ]; then
    . /etc/os-release
    DISTRO_NAME=$NAME
    DISTRO_VERSION=$VERSION_ID
elif [ -x /usr/bin/lsb_release ]; then
    # 备用方案
    DISTRO_NAME=$(lsb_release -s -i)
    DISTRO_VERSION=$(lsb_release -s -r)
fi

# 获取内核信息
KERNEL_INFO="$(uname -srm)"

printf "Welcome to \033[1;36m%s %s\033[0m (\033[1;33m%s\033[0m)\n\n" "$DISTRO_NAME" "$DISTRO_VERSION" "$KERNEL_INFO"
EOF

# 赋予执行权限
sudo chmod +x /etc/update-motd.d/00-header
```

输出结果（以 Debian 13 为示例）

```ansi
Welcome to [36mDebian GNU/Linux 13[0m ([33mLinux 6.12.94+deb13-amd64 x86_64[0m)

```

### 2. 创建系统状态信息脚本 (10-sysinfo)

此脚本用于显示内存和磁盘的实时使用情况。

```bash
sudo tee /etc/update-motd.d/10-sysinfo > /dev/null << 'EOF'
#!/bin/sh
# 内存使用情况
MEM_INFO=$(free -m | awk 'NR==2 {printf "\033[1;35m%.1fGB\033[0m/\033[1;35m%.1fGB\033[0m (\033[1;35m%.2f%%\033[0m used)", $3/1000, $2/1000, $3*100/$2}')

# 磁盘使用情况
DISK_INFO=$(df -h / | awk 'NR==2 {printf "\033[1;35m%.1fGB\033[0m/\033[1;35m%.1fGB\033[0m (\033[1;35m%s\033[0m used)", $3, $2, $5}')

# 输出内存磁盘使用情况
printf "Memory usage: %s\nDisk usage:   %s\n" "$MEM_INFO" "$DISK_INFO"
EOF

# 赋予执行权限
sudo chmod +x /etc/update-motd.d/10-sysinfo
```

```ansi
Memory: [35m0.5GB[0m/[35m15.6GB[0m ([35m3%[0m used)
Disk:   [35m1.2G[0m/[35m250G[0m ([35m1%[0m used)
```

## 两种方法对比

| 特性 | 静态 MOTD (`/etc/motd`) | 动态 MOTD (`/etc/update-motd.d/`) |
| :--- | :--- | :--- |
| 内容类型 | 纯文本 | 脚本输出 |
| 信息动态性 | 静态，修改文件后才会变 | 动态，每次登录时实时生成 |
| 配置复杂度 | 简单 | 中等 |
| 适用系统 | 所有 Linux 发行版 | 主要为 Debian/Ubuntu 系列 |

## 📝 显示登录后的信息（`/etc/profile.d/`）

`/etc/profile.d/` 的作用：该目录下的脚本用于设置环境变量和配置用户登录后的Shell 环境。

```bash
sudo tee /etc/profile.d/motd.sh > /dev/null << 'EOF'
#!/bin/sh

[ -r /etc/lsb-release ] && . /etc/lsb-release

if [ -x /usr/bin/lsb_release ]; then
        # Fall back to using the very slow lsb_release utility
        LSBI=$(lsb_release -s -i)
        LSBR=$(lsb_release -s -r)
fi

# 输出用户信息
printf "You have logged in as \033[1;36m%s\033[0m. Hostname of this device is \033[1;32m%s\033[0m.\n\n" "$(whoami)" "$(hostname -s)"
EOF

# 赋予执行权限
sudo chmod +x /etc/profile.d/motd.sh
```

输出结果（以 Debian 13 为示例）

```ansi
You have logged in as [36muser[0m. Hostname of this device is [32mDebian[0m.
```
