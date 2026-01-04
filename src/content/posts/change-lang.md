---
title: Linux 修改语言
published: 2025-3-13 21:56:40
description: 'Linux 修改语言'
image: api
tags: []
category: 'Linux'
draft: false 
lang: ''
---
# 修改语言

## Debian


```bash title="zh_CN"
sed -i 's/# zh_CN.UTF-8 UTF-8/zh_CN.UTF-8 UTF-8/g' /etc/locale.gen
sudo locale-gen
echo 'LANG="zh_CN.UTF-8"
LANGUAGE="zh_CN:zh"
' | sudo tee /etc/default/locale
```

```bash title="en_US"
sed -i 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/g' /etc/locale.gen
sudo locale-gen
echo 'LANG="en_US.UTF-8"
LANGUAGE="en_US:en"
' | sudo tee /etc/default/locale
```

或使用 `sudo dpkg-reconfigure locales` 进行重新配置

# Ubuntu


```bash title="zh_CN"
sudo apt install language-pack-zh-hans
sudo update-locale LANG=zh_CN.UTF-8
```

```bash title="en_US"
sudo apt install language-pack-en
sudo update-locale LANG=en_US.UTF-8
```

# Fedora


```bash title="zh_CN"
sudo dnf install langpacks-zh_CN 
sudo localectl set-locale LANG=zh_CN.UTF-8
```

```bash title="en_US"
sudo dnf install langpacks-en
sudo localectl set-locale LANG=en_US.UTF-8
```
