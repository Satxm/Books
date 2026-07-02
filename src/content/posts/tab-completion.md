---
title: Linux 启用 Tab 自动补全
published: 2025-3-13 21:56:40
description: 'Linux 启用 Tab 自动补全'
image: api
tags: [Lunux, bash-completion]
category: 'Linux'
draft: false 
lang: ''
---
在 Linux 命令行中，Tab 键补全功能可以极大提升输入效率。如果你的环境没有自动补全，请按照以下步骤进行配置。

## 1、安装 `bash-completion` 软件包

```bash
sudo apt update
sudo apt install bash-completion
```

## 2. 修改配置文件

安装完成后，需要修改配置文件来启用它。你可以选择**全局启用**（对所有用户生效）或**仅当前用户启用**。

### 选项 A：全局启用（修改 `/etc/bash.bashrc`）

需要 root 权限，适用于所有用户。
请检查文件中是否存在以下代码块，如果被注释（前面有 #），请去掉注释符号；如果没有，请将其添加到文件末尾。

```bash title="/etc/bash.bashrc" del={2-8} ins={9-15}
# enable bash completion in interactive shells
# if ! shopt -oq posix; then
#   if [ -f /usr/share/bash-completion/bash_completion ]; then
#     . /usr/share/bash-completion/bash_completion
#   elif [ -f /etc/bash_completion ]; then
#     . /etc/bash_completion
#   fi
# fi
if ! shopt -oq posix; then
  if [ -f /usr/share/bash-completion/bash_completion ]; then
    . /usr/share/bash-completion/bash_completion
  elif [ -f /etc/bash_completion ]; then
    . /etc/bash_completion
  fi
fi
```
### 选项 B：仅当前用户启用（修改 ~/.bashrc）

仅对当前登录用户生效。
同样，找到或添加以下代码段到 ~/.bashrc 文件末尾。

```bash title="~/.bashrc" del={4-6} ins={7-9}
# enable programmable completion features (you don't need to enable
# this, if it's already enabled in /etc/bash.bashrc and /etc/profile
# sources /etc/bash.bashrc).
# if [ -f /etc/bash_completion ] && ! shopt -oq posix; then
#     . /etc/bash_completion
# fi
if [ -f /etc/bash_completion ] && ! shopt -oq posix; then
    . /etc/bash_completion
fi
```

## 3. 重载配置使其生效
修改保存文件后，不需要重启系统，只需执行 source 命令重载配置即可立即生效。
如果是修改了全局配置：

```bash
source /etc/bash.bashrc
```

如果是修改了用户配置：

```bash
source ~/.bashrc
```

配置生效后，尝试输入命令或路径的前几个字母并按下 Tab 键，系统应该会自动补全或列出可能的选项。