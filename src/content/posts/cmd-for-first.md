---
title: CMD 获取返回值第一个结果
published: 2025-6-30 20:57:03
description: 'CMD 获取返回值第一个结果'
image: api
tags: []
category: 'Windows'
draft: false 
lang: ''
---

## 🚀 方法一：使用 `goto` 立即跳出

这种方法的核心在于一旦获取到第一个结果，立即终止循环。这避免了在处理大量数据时浪费时间去遍历剩余的无关行。

> [!IMPORTANT] 关键点
必须将逻辑封装在子程序（标签）中，否则 `goto` 会直接结束整个脚本。

```bash
call :GetFirstLine

:GetFirstLine
for /f "tokens=1" %%f in ('dir /b') do set m=%%f && goto:eof
goto:eof
```
- 优点：
速度快：只处理第一行，忽略后续所有行，处理大文件或大量输出时优势明显。

- 缺点：
需要使用 call 和标签，代码结构稍微复杂一点点。

## 🚀 方法二：使用 `if not defined` 条件判断

这种方法不强制终止循环，而是通过一个“锁”机制，确保变量只在第一次被赋值。

```bash
set "m="
for /f "tokens=1" %%f in ('dir /b') do if not defined m set m=%%f
```
- 优点：
逻辑直观：不需要理解子程序和跳转，代码写起来很顺手。

- 缺点：
效率较低：即使已经拿到了第一行，CMD 依然会在后台默默地把剩下的几百几千行都跑一遍（虽然不赋值），如果命令输出量巨大，会感到明显的卡顿。

## 💡 补充技巧：处理包含特殊字符的内容
如果你的命令输出包含特殊符号（如 ! 或 |），建议在脚本开头加上 setlocal enabledelayedexpansion 并使用延迟变量扩展，或者确保 for 循环内的变量处理得当。