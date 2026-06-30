---
title: PowerShell 实现文件与 Base64 字符串的相互转换
published: 2025-6-30 20:57:03
description: '用 PowerShell 实现文件与 Base64 字符串的相互转换'
image: api
tags: [Base64,Powershell,Convert]
category: 'Powershell'
draft: false 
lang: ''
---

在日常的系统管理或开发工作中，我们经常需要将二进制文件（如图片、可执行文件）转换为 Base64 字符串以便于传输或嵌入代码中，或者将 Base64 字符串还原为原始文件。

本文将介绍如何使用 PowerShell 快速实现这两个功能。

## 📜 脚本功能概览

该脚本包含两个核心函数：
ConvertTo-Base64：读取二进制文件，将其转换为 Base64 字符串并保存为文本文件。
ConvertTo-File：读取包含 Base64 字符串的文本文件，将其解码并还原为原始的二进制文件。

## 💻 完整 PowerShell 代码

您可以直接复制以下代码并在 PowerShell 环境中运行。

```powershell
<#
.SYNOPSIS
  文件与 Base64 字符串相互转换工具 (.NET 方法版)
.DESCRIPTION
  使用 .NET 的 [System.IO.File] 类进行高效的文件读写操作。
#>

function ConvertTo-Base64 {
  param (
    [Parameter(Mandatory=$true)]
    [string]$InputFile,
    
    [Parameter(Mandatory=$true)]
    [string]$OutputFile
  )

  try {
    # 1. 使用 .NET 方法高效读取所有字节
    $bytes = [System.IO.File]::ReadAllBytes($InputFile)
    
    # 2. 转换为 Base64 字符串
    $base64 = [Convert]::ToBase64String($bytes)
    
    # 3. 使用 .NET 方法写入字符串到文件
    [System.IO.File]::WriteAllText($OutputFile, $base64)
    
    Write-Host "✅ 成功：已将 '$InputFile' 转换为 Base64 并保存至 '$OutputFile'" -ForegroundColor Green
  }
  catch {
    Write-Error "❌ 转换失败: $_"
  }
}

function ConvertTo-File {
  param (
    [Parameter(Mandatory=$true)]
    [string]$InputFile,
    
    [Parameter(Mandatory=$true)]
    [string]$OutputFile
  )

  try {
    # 1. 使用 .NET 方法读取 Base64 字符串
    $base64 = [System.IO.File]::ReadAllText($InputFile)
    
    # 2. 转换为字节数组
    $bytes = [Convert]::FromBase64String($base64)
    
    # 3. 使用 .NET 方法写入字节到文件
    [System.IO.File]::WriteAllBytes($OutputFile, $bytes)
    
    Write-Host "✅ 成功：已将 '$InputFile' 还原为文件并保存至 '$OutputFile'" -ForegroundColor Green
  }
  catch {
    Write-Error "❌ 还原失败: $_"
  }
}

# ==========================================
# 使用示例 (Usage Examples)
# ==========================================

# 示例 1: 将文件转换为 Base64
ConvertTo-Base64 -InputFile 'C:\MyFolder\image.png' -OutputFile 'C:\MyFolder\image_base64.txt'

# 示例 2: 将 Base64 还原为文件
ConvertTo-File -InputFile 'C:\MyFolder\image_base64.txt' -OutputFile 'C:\MyFolder\restored_image.png'
```

## ✨ 主要改动与优势

1.  **核心方法替换**：
  *   **读文件**：使用 `[System.IO.File]::ReadAllBytes($path)` 直接读取二进制，使用 `[System.IO.File]::ReadAllText($path)` 读取文本。这比 `Get-Content` 更底层、更高效。
  *   **写文件**：使用 `[System.IO.File]::WriteAllText($path, $content)` 和 `[System.IO.File]::WriteAllBytes($path, $bytes)`。这比 `Set-Content` 更直接，且不用担心 PowerShell 版本间 `-Encoding` 参数的差异问题。
  *   若使用 Get-Content/Set-Content 需添加 `-Encoding Byte`，在 PS 7+ 时用 `-AsByteStream`

2.  **增加错误处理 (`try/catch`)**：
  *   新增了 `try...catch` 块。如果文件路径不存在、没有权限或 Base64 字符串损坏，脚本会捕获异常并打印友好的错误信息，而不是直接崩溃并输出一堆红色的报错堆栈。

3.  **增加执行反馈**：
  *   成功执行后会输出绿色的成功提示，方便用户确认操作结果。

## ⚠️ 注意事项

*   **内存占用**：`[System.IO.File]::ReadAllBytes` 会一次性将整个文件加载到内存中。虽然这种方法速度很快，但如果处理 **非常大（例如几个 GB）** 的文件，可能会导致内存溢出。对于常规配置文件、图片或小型安装包，这种方法是最优解。
