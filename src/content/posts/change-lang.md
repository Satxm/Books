---
title: Linux 系统语言修改指南
published: 2025-3-13 21:56:40
description: 'Linux 系统语言修改指南'
image: api
tags: []
category: 'Linux'
draft: false 
lang: ''
---

本文档整理了在主流 Linux 发行版（Debian、Ubuntu、Fedora）中修改系统语言（如切换为中文或英文）的常用方法。

## 📋 通用验证与生效方式

在执行具体命令前，建议先了解如何验证当前语言环境以及修改后如何生效。
- 查看当前语言环境：

```bash
locale
```

- 使修改生效：
修改配置后，通常需要重新登录或重启系统才能完全生效。若仅针对当前会话，可执行：

```bash
source /etc/default/locale
```

## 🟦 Debian 系统

在 Debian 中，可以通过直接编辑配置文件或使用交互式工具来修改语言。

### 1. 切换为中文 (zh_CN)

```bash title="zh_CN"
sed -i 's/# zh_CN.UTF-8 UTF-8/zh_CN.UTF-8 UTF-8/g' /etc/locale.gen
sudo locale-gen
echo 'LANG="zh_CN.UTF-8"
LANGUAGE="zh_CN:zh"
' | sudo tee /etc/default/locale
```

### 2. 切换为英文 (en_US)

```bash title="en_US"
sed -i 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/g' /etc/locale.gen
sudo locale-gen
echo 'LANG="en_US.UTF-8"
LANGUAGE="en_US:en"
' | sudo tee /etc/default/locale
```
### 3. 交互式界面进行配置

你也可以使用交互式界面进行配置，操作更直观：

```bash
sudo dpkg-reconfigure locales
```

## 🟧 Ubuntu 系统

Ubuntu 提供了专门的工具包来管理语言，操作相对简单。

### 1. 切换为中文 (zh_CN)

```bash title="zh_CN"
sudo apt update
sudo apt install language-pack-zh-hans
sudo update-locale LANG=zh_CN.UTF-8
```

### 2. 切换为英文 (en_US)

```bash title="en_US"
sudo apt update
sudo apt install language-pack-en
sudo update-locale LANG=en_US.UTF-8
```

## 🟥 Fedora 系统

Fedora 使用 `dnf` 包管理器和 `localectl` 工具来管理系统语言。

### 1. 切换为中文 (zh_CN)

```bash title="zh_CN"
sudo dnf install langpacks-zh_CN 
sudo localectl set-locale LANG=zh_CN.UTF-8
```

### 2. 切换为英文 (en_US)

```bash title="en_US"
sudo dnf install langpacks-en
sudo localectl set-locale LANG=en_US.UTF-8
```
