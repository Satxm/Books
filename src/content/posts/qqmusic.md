---
title: Linux 解决 QQ 音乐闪退问题
published: 2025-5-29 22:10:25
description: '解决 QQ 音乐闪退问题'
image: api
tags: [Linux,QQ音乐]
category: 'Linux'
draft: false 
lang: ''
---
# 解决 QQ 音乐闪退问题

安装完成QQ音乐后，无法正常打开，打开了就闪退，原因是QQ音乐通过沙盘模式打开导致的闪退。

## 直接打开 QQ 音乐

```bash
qqmusic --no-sandbox
```

## 修改快捷方式



```bash title="vim"
vim /usr/share/applications/qqmusic.desktop
```

```bash title="sed"
sudo sed -i -e 's/Name=qqmusic/Name=QQMusic\nName[zh_CN]=QQ音乐/' -e '/^Exec/s/$/ --no-sandbox/' /usr/share/applications/qqmusic.desktop

```


`qqmusic.desktop` 文件内容

```v title="/usr/share/applications/qqmusic.desktop" del={2-3} ins={4-7}
[Desktop Entry]
Name=qqmusic
Exec=/opt/qqmusic/qqmusic %U
Name=QQMusic
Name[zh_CN]=QQ音乐
Exec=/opt/qqmusic/qqmusic %U --no-sandbox
Terminal=false
```

:::tip[说明]
- `Name=QQ Music` 修改快捷方式显示名称
- `Name[zh_CN]=QQ音乐` 修改中文时快捷方式显示名称
:::
