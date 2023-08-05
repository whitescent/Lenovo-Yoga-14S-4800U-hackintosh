# Lenovo-Yoga-14S-4800U-hackintosh

Yoga14s 4800U macOS ventura 版本的 EFI 文件

|硬件 | 状态|
|----|-----|
|CPU: AMD 4800 U| ✔ |
|内存| ✔ |
|显卡 核显 Vega 8| ✔ |
|网卡 ax 200 | ✔ |
|硬盘 海力士 | ❌（需替换，或者加装新的硬盘另一个 m.2 接口） |


|功能 | 状态|
|----|-----|
|键盘|✔|
|触摸板|✔|
|WIFI|✔|
|核显|✔|
|蓝牙|❌|
|睡眠|❌|
|右侧第二个 USB 接口|❌|

> [!NOTE]
> 因为本机型需要禁用 XHCI 1 口才能正常进入安装系统，所以无法使用蓝牙、右侧第二个 USB 口。


## 安装步骤

### 1. 解锁 BIOS 高级菜单

首先，请先解锁 14s 的 BIOS 高级菜单，具体请参考[这篇文章](https://zhuanlan.zhihu.com/p/184982689)

### 2. 生成用于屏蔽系统盘的 NVME ACPI 文件

> [!Important]
> 如果你把笔记本自带的海力士硬盘替换了或者你的笔记本的硬盘是 SN730 的话，不需要执行以下的步骤，请去 EFI/OC/ACPI 删除我生成的 SSDT-NVME-DISABLE.aml 文件。

首先在笔记本上按 win + x 打开设备管理器。选择存储控制器，接着，找到标准 NVM Express 控制器的选项，右键属性 -> 详细信息，选择位置路径

复制第二行带有 ACPI 的字符串，假设我的笔记本是 `ACPI(_SB_)#ACPI(PC00)#ACPI(RP05)#ACPI(PXSX)`.

下载并且解压 [iasl.exe](https://www.intel.com/content/www/us/en/download/774881/acpi-component-architecture-downloads-windows-binary-tools.html)

下载用于屏蔽 NVME 的 [dsl 文件](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/decompiled/SSDT-GPU-DISABLE.dsl.zip) (屏蔽 NVME 和屏蔽 GPU 是一样的原理，所以直接下载这个文件即可)

用任意的文本编辑器打开下载好的 `SSDT-GPU-DISABLE.dsl` 文件（最好能改个名字），将我们之前获取到的 NVME 位置路径，按照这个规则改写这两行的代码

以我的 ACPI 路径为例：`ACPI(_SB_)#ACPI(PC00)#ACPI(RP05)#ACPI(PXSX)`，只需要组合 ACPI 括号内容的东西

`ACPI(_SB_)#ACPI(PC00)#ACPI(RP05)#ACPI(PXSX)` -> `_SB_.PC00.RP05.PXSX`（将这个把 dsl 文件中的这两行替换）

![image](https://github.com/whitescent/Lenovo-Yoga-14S-4800U-hackintosh/assets/31311826/e2286ef7-b9f6-4e7d-9d45-ddcb203f8d8d)
> 源文件

![image](https://github.com/whitescent/Lenovo-Yoga-14S-4800U-hackintosh/assets/31311826/28f76f4d-f05c-4086-89bc-42e7f5ad95f5)
> 替换后的

保存，然后将你下载好的 isal.exe 文件拖到终端，空格，再把改好的 dsl 文件也拖到终端，如下图：

![image](https://github.com/whitescent/Lenovo-Yoga-14S-4800U-hackintosh/assets/31311826/bc6599d2-130c-4efb-941c-3a148e29754b)

即可生成 acpi 所需要的 aml 文件，将这个拖到 EFI\OC\ACPI 下替换我生成过的文件，

![image](https://github.com/whitescent/Lenovo-Yoga-14S-4800U-hackintosh/assets/31311826/2e198e4e-0923-4689-a4fb-42d4841be4fa)

> [!Note]
> 一般如果加装新的硬盘，设备管理器 -> 存储控制器中会显示两个`标准 NVM Express 控制器`，请都按照这个方法生成一个 aml 文件，然后一个一个替换尝试进入安装界面。

### 3. 禁用 XHCI

重启笔记本，按住 F2 进入 BIOS，AMD CBS -> FCH Common Options -> USB Configuration Options.

将 XHCI1 Controller enable 从 Auto 改成 Dissabled.

保存，之后重启按 F12 选择 U 盘引导即可。
