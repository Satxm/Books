---
title: VMware 创建虚拟 USB 磁盘
published: 2026-5-13 15:07:52
description: '创建一个 .vmdk 文件，虚拟机将其识别为真实的 USB 存储设备，支持热插拔模拟和完整的USB协议交互。'
image: api
tags: [VMware,USB]
category: 'VMware'
draft: false 
lang: ''
---

# VMware 创建虚拟 USB 磁盘

## 步骤 1：使用 VMware Virtual Disk Manager 创建虚拟磁盘

创建虚拟 USB 磁盘（示例：16GB）：

```cmd
set "path=C:\Program Files (x86)\VMware\VMware Workstation;%path%"
vmware-vdiskmanager.exe -c -s 16GB -t 0 "virt-usb-key.vmdk"
```

```powershell
$env:Path += ;C:\Program Files (x86)\VMware\VMware Workstation;"
vmware-vdiskmanager.exe -c -s 16GB -t 0 "virt-usb-key.vmdk"
```

参数说明见附录 [#vmware-vdiskmanagerexe-参数说明](#vmware-vdiskmanagerexe-参数说明)

常见问题：创建过程中的警告信息
执行命令后，您可能会看到类似以下的警告：

```cmd
>vmware-vdiskmanager.exe -c -a lsilogic -s 64GB -t 0 "virt-usb-key.vmdk"
 
VixDiskLib: Failed to initialize PhoneHome library.
Creating disk 'virt-usb-key.vmdk'
DISKLIB-LIB_SIZE   : Unable to get file system ID for filename "R:\virt-usb-key.vmdk"
Virtual disk creation successful.
```
|警告信息|原因|处理方式|
| --- | --- | --- |
|VixDiskLib: Failed to initialize PhoneHome library|VMware 客户体验改进计划（CEIP）的遥测功能初始化失败|忽略 - 已知的无害警告|
|DISKLIB-LIB_SIZE: Unable to get file system ID|新创建的磁盘尚未格式化，无法获取文件系统 ID|正常 - 格式化后即可消除|

✅ 结论：只要出现 Virtual disk creation successful，就表示磁盘已成功创建，警告不影响功能。

## 步骤 2：编辑虚拟机配置文件（.vmx）

> [!IMPORTANT] ⚠️ 重要
> 确保目标虚拟机完全关闭（不是挂起状态）。

找到虚拟机目录下的 .vmx 文件（例如 Windows 10.vmx），用记事本打开并添加以下配置：

### 使用 USB 2.0（EHCI）
```ymal
# 启用 USB 2.0 控制器（EHCI）
ehci.present = "TRUE"
ehci.pciSlotNumber = "34"

# 添加虚拟 USB 磁盘
ehci:0.present = "TRUE"
ehci:0.deviceType = "disk"
ehci:0.fileName = "virt-usb-key.vmdk"
ehci:0.readonly = "FALSE"

# USB 设备权限（Workstation 16.2.1+ 需要）
usb.restrictions.defaultAllow = "TRUE"
```

### 使用 USB 3.0（xHCI）
如需USB 3.0支持，添加以下配置：
```
# 启用 xHCI（USB 3.0）控制器
usb_xhci.present = "TRUE"
usb_xhci.pciSlotNumber = "192"

# 将虚拟磁盘挂载到 xHCI
usb_xhci:0.present = "TRUE"
usb_xhci:0.deviceType = "disk"
usb_xhci:0.fileName = "VUSBDisk.vmdk"
usb_xhci:0.readonly = "FALSE"
usb_xhci:0.speed = "16"

# USB 设备权限（Workstation 16.2.1+ 需要）
usb.restrictions.defaultAllow = "TRUE"
```

配置项详解见附录[#vmx-文件配置说明](#vmx-文件配置说明)

## 步骤 3：启动虚拟机并初始化磁盘
启动虚拟机

进入磁盘管理（Windows 客户机）：

右键"此电脑" → "管理" → "磁盘管理"

或运行 diskmgmt.msc

初始化磁盘：

找到未分配的磁盘（显示为"未知"）

右键选择"初始化磁盘" → 选择 MBR 或 GPT

创建简单卷并格式化：

右键未分配空间 → "新建简单卷"

选择文件系统（NTFS/FAT32/exFAT）

完成格式化

> [!TIP] 💡提示
格式化完成后，虚拟USB盘即可像普通U盘一样使用，支持文件复制、软件安装、甚至制作启动盘。

## 进阶配置
添加多个虚拟USB设备
如需添加第二个虚拟U盘，修改配置：

### USB 2.0
```
# 第一个虚拟USB盘
ehci:0.present = "TRUE"
ehci:0.deviceType = "disk"
ehci:0.fileName = "virt-usb-key1.vmdk"
 
# 第二个虚拟USB盘
ehci:1.present = "TRUE"
ehci:1.deviceType = "disk"
ehci:1.fileName = "virt-usb-key2.vmdk"
```

### USB 3.0
```
# 第一个虚拟USB盘
usb_xhci:0.present = "TRUE"
usb_xhci:0.deviceType = "disk"
usb_xhci:0.fileName = "virt-usb-key1.vmdk"
 
# 第二个虚拟USB盘
usb_xhci:1.present = "TRUE"
usb_xhci:1.deviceType = "disk"
usb_xhci:1.fileName = "virt-usb-key2.vmdk"
```
## 故障排查
|问题|解决方案|
| --- | --- |
|虚拟机无法识别USB设备|检查 .vmx 文件路径是否正确，确认虚拟机已完全关闭后修改|
|磁盘显示为"脱机"|在磁盘管理中右键选择"联机"|
|无法格式化|以管理员身份运行磁盘管理，或尝试使用 diskpart 命令|
|Workstation 16.2+ 无法连接|确保添加 `usb.restrictions.defaultAllow = "TRUE"`|

## 附录

### `vmware-vdiskmanager.exe` 参数说明
| 参数 | 功能描述 | 命令格式示例 |
| :--- | :--- | :--- |
| -c | **创建磁盘**<br>创建一个新的空白虚拟磁盘文件。需配合 `-s` (大小), `-t` (类型) 等参数使用。 | `vmware-vdiskmanager.exe -c -s <磁盘容量> -a lsilogic -t 0 <磁盘文件路径>` |
| -d | **碎片整理**<br>整理虚拟磁盘文件的碎片，优化宿主机的读写性能。仅适用于动态增长型磁盘。 | `vmware-vdiskmanager.exe -d <磁盘文件路径>` |
| -k | **收缩磁盘**<br>释放虚拟磁盘中未使用的物理空间（减小宿主机上的文件大小）。需先在客户机内清理文件。 | `vmware-vdiskmanager.exe -k <磁盘文件路径>` |
| -n | **重命名**<br>修改虚拟磁盘文件的名称。需指定源文件和目标文件路径。 | `vmware-vdiskmanager.exe -n <原磁盘文件路径> <新磁盘文件路径>` |
| -p | **准备收缩**<br>为收缩操作做准备。需先通过 DiskMount 工具将虚拟磁盘挂载到宿主机，然后指定挂载点。 | `vmware-vdiskmanager.exe -p Z:` (Z为挂载的盘符) |
| -r | **转换磁盘**<br>更改磁盘类型（如将“预分配”转为“动态增长”，或合并/拆分文件）。 | `vmware-vdiskmanager.exe -r <原磁盘文件路径> -t 0 <新磁盘文件路径>` |
| -x | **扩容磁盘**<br>增大虚拟磁盘的最大容量。扩容后需在虚拟机系统内扩展分区。 | `vmware-vdiskmanager.exe -x 100GB <磁盘文件路径>` |
| -R | **修复检查**<br>检查稀疏虚拟磁盘的一致性并尝试修复错误。 | `vmware-vdiskmanager.exe -R <磁盘文件路径>` |
| -e | **链一致性检查**<br>检查磁盘链（包括快照文件）的一致性，确保没有断裂或损坏。 | `vmware-vdiskmanager.exe -e <磁盘文件路径>` |
| -D | **设为可删除**<br>解除磁盘的锁定状态，使其可被删除。通常用于从其他产品复制过来的磁盘。 | `vmware-vdiskmanager.exe -D <磁盘文件路径>` |
| -U | **删除链接**<br>删除/断开单个磁盘链接（通常用于处理快照链中的特定节点）。 | `vmware-vdiskmanager.exe -U <子磁盘文件路径>` （例如Snapshot-000001.vmdk）|

### 创建和转换的附加选项
| 参数 | 作用 | 适用场景 |
| :--- | :--- | :--- |
| -a | **定义虚拟磁盘的适配器类型**<br>`lsilogic`：适用于大多数现代 Windows (XP/7/10/11) 和 Linux 系统。<br>`buslogic`：适用于较老的 Windows (NT/2000) 系统。<br>`ide`：适用于非常古老的系统或作为从盘挂载。| 仅用于 创建 (`-c`) |
| -s | **设定虚拟磁盘的最大容量** | 仅用于 创建 (`-c`) |
| -t | **定义磁盘文件的存储方式**<br>0: 单个动态增长文件（默认）<br>1: 动态增长，拆分为多个 2GB 文件<br>2: 预分配空间，单个文件<br>3: 预分配空间，拆分为多个 2GB 文件 | 用于 创建 (`-c`) 和 转换 (`-r`)  
| -z | **设定压缩率（0-9）**，仅针对流媒体优化的磁盘。 | 仅用于 转换 (`-r`) 且 `-t 5` 时 |

### `.vmx` 文件配置说明
| 配置项 (Key) | 作用说明 | 典型值 / 备注 |
| :--- | :--- | :--- |
| `*.present` | **启用硬件**<br>告诉虚拟机是否存在某个硬件设备。 | `"TRUE"` 或 `"FALSE"`<br>例如：`usb_xhci.present = "TRUE"` (启用USB 3.0) |
| `*.pciSlotNumber` | **PCI 插槽定位**<br>指定设备在虚拟主板上的插槽位置，防止重启后硬件顺序变动。 | `"32"`, `"33"`, `"192"` 等<br>通常由系统自动分配，手动指定可固定位置。 |
| `*.deviceType` | **设备类型定义**<br>定义该接口连接的是什么类型的设备。 | `"disk"` (磁盘)<br>`"cdrom"` (光驱)<br>`"usbhub"` (集线器) |
| `*.fileName` | **文件路径映射**<br>指向实际的 `.vmdk` 磁盘文件或 ISO 镜像路径。 | `"Windows10.vmdk"` (相对路径)<br>`"D:\VMs\Disk.vmdk"` (绝对路径) |
| `*.parent` | **USB 拓扑父级**<br>定义设备的上游连接。 | `"-1"`：代表直接连在根控制器上（无中间集线器）。 |
| `*.port` | **USB 端口号**<br>定义设备插在父级设备的哪个物理端口上。 | `"0"`, `"1"`, `"2"`<br>用于固定设备连接位置。 |
| `*.speed` | **强制 USB 速度**<br>强制设备以特定速度模式运行（主要用于直通调试）。 | 见下方速度值对照表<br>例如：`"16"` 强制 USB 3.0。 |
| `usb.restrictions.defaultAllow` | **解除连接限制**<br>允许 USB 设备在未手动确认的情况下自动连接。 | `"TRUE"`<br>新版 VMware 解决 USB 设备“连不上”或“需手动点击”的必备参数。 |

### USB 速度值对照表
| 十进制值 | | 对应的 USB 标准 |
| :--- | :--- | :--- |
| 1 | 0x1 | USB 1.0/1.1 (1.5 Mbps) |
| 2 | 0x2 | USB 1.1 (12 Mbps) |
| 4 | 0x4 | USB 2.0 (480 Mbps) |
| 16 | 0x10 | USB 3.2 Gen 1 (5 Gbps) |
| 32 | 0x20 | USB 3.2 Gen 2 (10 Gbps) |
| 64 | 0x40 | USB 3.2 Gen 2x2 (20 Gbps) |


https://blog.csdn.net/weixin_43657424/article/details/160217204