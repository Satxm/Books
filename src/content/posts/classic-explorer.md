---
title: 恢复 Windows 11 经典资源管理器和右键菜单
published: 2025-7-1 13:09:56
description: '恢复 Windows 11 经典资源管理器和右键菜单'
image: api
tags: []
category: 'Windows'
draft: false 
lang: ''
---

本指南提供了两种修改 Windows 11 界面风格的方法：一种是仅针对当前登录用户的修改，另一种是适用于所有用户的全局修改。

## ⚠️ 操作前重要提示
备份注册表：修改注册表存在一定风险。在进行任何操作前，强烈建议您备份注册表或创建系统还原点，以便在出现问题时能够恢复。
重启资源管理器：所有修改完成后，都需要重启“Windows 资源管理器”（explorer.exe）才能生效。您可以使用任务管理器结束 explorer.exe 进程，然后通过“运行新任务”重新启动它，或者直接重启电脑。

## 👤 针对当前用户

```cmd title="恢复经典(Windows 10)"
:: 右键菜单
reg add "HKEY_CURRENT_USER\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InProcServer32" /ve /f
reg add "HKEY_CURRENT_USER\Software\Classes\WOW6432Node\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InProcServer32" /ve /f
:: 资源管理器搜索
reg add "HKEY_CURRENT_USER\Software\Classes\CLSID\{1d64637d-31e9-4b06-9124-e83fb178ac6e}\InProcServer32" /ve /f
reg add "HKEY_CURRENT_USER\Software\Classes\WOW6432Node\CLSID\{1d64637d-31e9-4b06-9124-e83fb178ac6e}\InProcServer32" /ve /f
:: 资源管理器
reg add "HKEY_CURRENT_USER\Software\Classes\CLSID\{6480100b-5a83-4d1e-9f69-8ae5a88e9a33}\InProcServer32" /ve /f
reg add "HKEY_CURRENT_USER\Software\Classes\WOW6432Node\CLSID\{6480100b-5a83-4d1e-9f69-8ae5a88e9a33}\InProcServer32" /ve /f
reg add "HKEY_CURRENT_USER\Software\Classes\CLSID\{2aa9162e-c906-4dd9-ad0b-3d24a8eef5a0}\InProcServer32" /ve /f
reg add "HKEY_CURRENT_USER\Software\Classes\WOW6432Node\CLSID\{2aa9162e-c906-4dd9-ad0b-3d24a8eef5a0}\InProcServer32" /ve /f
:: 重启资源管理器
taskkill /f /im explorer.exe && start explorer.exe
```

```cmd title="恢复新版(Windows 11)"
:: 右键菜单
reg delete "HKEY_CURRENT_USER\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}" /f
reg delete "HKEY_CURRENT_USER\Software\Classes\WOW6432Node\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}" /f
:: 资源管理器搜索
reg delete "HKEY_CURRENT_USER\Software\Classes\CLSID\{1d64637d-31e9-4b06-9124-e83fb178ac6e}" /f
reg delete "HKEY_CURRENT_USER\Software\Classes\WOW6432Node\CLSID\{1d64637d-31e9-4b06-9124-e83fb178ac6e}" /f
:: 资源管理器
reg delete "HKEY_CURRENT_USER\Software\Classes\CLSID\{6480100b-5a83-4d1e-9f69-8ae5a88e9a33}" /f
reg delete "HKEY_CURRENT_USER\Software\Classes\WOW6432Node\CLSID\{6480100b-5a83-4d1e-9f69-8ae5a88e9a33}" /f
reg delete "HKEY_CURRENT_USER\Software\Classes\CLSID\{2aa9162e-c906-4dd9-ad0b-3d24a8eef5a0}" /f
reg delete "HKEY_CURRENT_USER\Software\Classes\WOW6432Node\CLSID\{2aa9162e-c906-4dd9-ad0b-3d24a8eef5a0}" /f
:: 重启资源管理器
taskkill /f /im explorer.exe && start explorer.exe
```

## 👥 针对所有用户 （高级操作，需要 SYSTEM 权限）

> [!warning] 高级操作警告
> - 该操作需要 SYSTEM 权限，可以使用 [superUser](https://github.com/mspaintmsi/superUser)、[NSudo](https://github.com/M2TeamArchived/NSudo) 或 [MinSudo](https://github.com/M2Team/NanaRun?#minsudo) 等工具
> - 请谨慎操作，错误的修改可能导致系统不稳定。


```cmd title="恢复经典(Windows 10)"
:: 右键菜单
reg add "HKEY_LOCAL_MACHINE\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InProcServer32" /ve /f
reg add "HKEY_LOCAL_MACHINE\Software\Classes\WOW6432Node\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InProcServer32" /ve /f
:: 资源管理器搜索
reg add "HKEY_LOCAL_MACHINE\Software\Classes\CLSID\{1d64637d-31e9-4b06-9124-e83fb178ac6e}\InProcServer32" /ve /f
reg add "HKEY_LOCAL_MACHINE\Software\Classes\WOW6432Node\CLSID\{1d64637d-31e9-4b06-9124-e83fb178ac6e}\InProcServer32" /ve /f
:: 资源管理器
reg add "HKEY_LOCAL_MACHINE\Software\Classes\CLSID\{6480100b-5a83-4d1e-9f69-8ae5a88e9a33}\InProcServer32" /ve /f
reg add "HKEY_LOCAL_MACHINE\Software\Classes\WOW6432Node\CLSID\{6480100b-5a83-4d1e-9f69-8ae5a88e9a33}\InProcServer32" /ve /f
reg add "HKEY_LOCAL_MACHINE\Software\Classes\CLSID\{2aa9162e-c906-4dd9-ad0b-3d24a8eef5a0}\InProcServer32" /ve /f
reg add "HKEY_LOCAL_MACHINE\Software\Classes\WOW6432Node\CLSID\{2aa9162e-c906-4dd9-ad0b-3d24a8eef5a0}\InProcServer32" /ve /f
:: 重启资源管理器
taskkill /f /im explorer.exe && start explorer.exe
```

```cmd title="恢复新版(Windows 11)"
:: 右键菜单
reg add "HKEY_LOCAL_MACHINE\Software\Classes\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InProcServer32" /ve /t REG_SZ /d "C:\Windows\System32\Windows.UI.FileExplorer.dll" /f
reg add "HKEY_LOCAL_MACHINE\Software\Classes\WOW6432Node\CLSID\{86ca1aa0-34aa-4e8b-a509-50c905bae2a2}\InProcServer32" /ve /t REG_SZ /d "C:\Windows\SysWOW64\Windows.UI.FileExplorer.dll" /f
:: 资源管理器搜索
reg add "HKEY_LOCAL_MACHINE\Software\Classes\CLSID\{1d64637d-31e9-4b06-9124-e83fb178ac6e}\InProcServer32" /ve /t REG_SZ /d "C:\Windows\System32\Windows.UI.FileExplorer.dll" /f
reg add "HKEY_LOCAL_MACHINE\Software\Classes\WOW6432Node\CLSID\{1d64637d-31e9-4b06-9124-e83fb178ac6e}\InProcServer32" /ve /t REG_SZ /d "C:\Windows\SysWOW64\Windows.UI.FileExplorer.dll" /f
:: 资源管理器
reg add "HKEY_LOCAL_MACHINE\Software\Classes\CLSID\{6480100b-5a83-4d1e-9f69-8ae5a88e9a33}\InProcServer32" /ve /t REG_SZ /d "C:\Windows\System32\Windows.UI.FileExplorer.dll" /f
reg add "HKEY_LOCAL_MACHINE\Software\Classes\WOW6432Node\CLSID\{6480100b-5a83-4d1e-9f69-8ae5a88e9a33}\InProcServer32" /ve /t REG_SZ /d "C:\Windows\SysWOW64\Windows.UI.FileExplorer.dll" /f
reg add "HKEY_LOCAL_MACHINE\Software\Classes\CLSID\{2aa9162e-c906-4dd9-ad0b-3d24a8eef5a0}\InProcServer32" /ve /t REG_SZ /d "C:\Windows\System32\Windows.UI.FileExplorer.dll" /f
reg add "HKEY_LOCAL_MACHINE\Software\Classes\WOW6432Node\CLSID\{2aa9162e-c906-4dd9-ad0b-3d24a8eef5a0}\InProcServer32" /ve /t REG_SZ /d "C:\Windows\SysWOW64\Windows.UI.FileExplorer.dll" /f
:: 重启资源管理器
taskkill /f /im explorer.exe && start explorer.exe
```


