---
title: Linux 移除旧内核
published: 2025-3-5 18:18:28
description: 'Linux 移除旧内核'
image: ''
tags: []
category: ''
draft: false 
lang: ''
---
# 移除旧内核

## 查看已安装内核

```bash
dpkg --get-selections | grep linux 
```

## 卸载旧内核

```bash
sudo apt remove <old-image>
```

## 卸载旧内核配置文件

```bash
sudo apt purge <old-image>
```

## 更新系统引导

```bash
sudo update-grub 
```
