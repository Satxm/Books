---
title: 时区设置为本地时区
published: 2025-3-5 18:18:28
description: '时区设置为本地时区'
image: api
tags: []
category: 'Linux'
draft: false 
lang: ''
---

# 时区设置

## 设置时区为中国标准时间

```bash
sudo timedatectl set-timezone "Asia/Shanghai"
```
## 设置硬件时钟使用本地时区

```bash
sudo timedatectl set-local-rtc 1
```
