---
title: 配置 SSH
published: 2025-3-6 10:20:02
description: '配置 SSH'
image: api
tags: [Linux, ssh]
category: 'Linux'
draft: false 
lang: ''
---

# 配置 SSH

修改 `sudo vim /etc/ssh/sshd_config` 文件，添加

```vim
PermitRootLogin yes # 允许 root 登录
PasswordAuthentication yes # 允许密码登录
PubkeyAuthentication yes # 允许证书登录
```
