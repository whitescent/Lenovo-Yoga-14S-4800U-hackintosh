# Lenovo-Yoga-14S-4800U-hackintosh

Yoga14s 4800U macOS ventura 版本的 EFI 文件

|硬件 | 状态|
|----|-----|
|CPU: AMD 4800 U| ✔ |
|内存| ✔ |
|显卡 核显 Vega 8| ✔ |
|网卡 ax200 | ✔ |
|硬盘 海力士 | ❌（需替换，或者加装新的硬盘另一个 m.2 接口） |

|功能 | 状态|
|----|-----|
|键盘|✔|
|触摸板|✔|
|USB + TypeC 接口|✔|
|WIFI|✔|
|核显|✔|
|蓝牙|✔|
|睡眠|❌|

## 安装步骤

### 1. 解锁 BIOS 高级菜单

首先，请先解锁 14s 的 BIOS 高级菜单，具体请参考[这篇文章](https://zhuanlan.zhihu.com/p/184982689)

> [!Important]
> 请注意 BIOS 驱动版本: DMCN32WW（最好使用此版本，虽然最新版本也能解锁 BIOS 高级菜单，但是无法调整显存大小！！设置完会自动重置显存）
> 如果你的 BIOS 已经是最新版本，请去官网重新[下载](https://newdriverdl.lenovo.com.cn/newlenovo/alldriversupload/73444/BIOS-DMCN32WW.exe)此版本的驱动
> 在回退 BIOS 版本之前，你需要先在 BIOS 中，将 bios back flash 启用，否则无法降级

### 2. 生成用于屏蔽系统盘的 NVME ACPI 文件（如果无法顺利启动到安装界面再尝试这个）

> [!Important]
> 如果你把笔记本自带的海力士硬盘替换了或者你的笔记本的硬盘是支持黑苹果的硬盘，不需要执行以下的步骤，并且去 EFI/OC/ACPI 删除我生成的 SSDT-NVME-DISABLE.aml 文件。

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

### 3. 生成 PI 信息

用 [OC auxiliary tools 工具](https://github.com/ic005k/OCAuxiliaryTools) 打开 config.plist

PI -> 在 SystemProductName 后面点击生成，生成自己的机器码

PI -> 在 ROM 后面点击生成

保存

### 4. 安装

重启笔记本按 F12 选择 U 盘引导即可。

## 其他

> [!Note]
> 此 EFI **可能**一开始无法使用触摸板！！！但是重启几次好像会莫名其妙好，暂时还不知道什么原因

安装完进系统使用的时候，可能会碰到做一些操作就突然卡着，这个可能是显存问题/ NootedRed 驱动问题，按照[这篇文章](https://zhuanlan.zhihu.com/p/184982689)修改显存大小可以暂时解决，建议改到 3G 左右

如果进入安装界面是其他语言，请在 OC 选择启动项的时候按下空格，选择 reset NVRAM，或者点苹果 Icon 往右数第二个 item，然后更换语言

推荐将显存设置为 3G 以上

如果你有更好的 EFI 文件,欢迎提交 PR 更新这个 repo
