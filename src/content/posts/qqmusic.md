---
title: Linux 解决 QQ 音乐闪退问题
published: 2025-5-29 22:10:25
description: '解决 QQ 音乐闪退问题'
image: ''
tags: []
category: ''
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

```v title="/usr/share/applications/qqmusic.desktop"
[Desktop Entry]
Name=qqmusic // [!code --]
Name=QQMusic // [!code ++]
Name[zh_CN]=QQ音乐 // [!code ++]
Exec=/opt/qqmusic/qqmusic %U // [!code --]
Exec=/opt/qqmusic/qqmusic %U --no-sandbox // [!code ++]
Terminal=false
```

:::tip[说明]
`Name=QQ Music` `Name[zh_CN]=QQ音乐` 修改快捷方式显示名称
:::
