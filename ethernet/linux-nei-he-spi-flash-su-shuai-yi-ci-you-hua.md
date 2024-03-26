# 😄 Linux内核SPI FLASH速率一次优化

## Linux 内核SPI FLAHS适配

MTD(memory technology device内存技术设备)是用于访问memory设备（RAM、ROM、flash）的Linux的子系统。MTD的主要目的是为了使新的memory设备的驱动更加简单，为此它在硬件和上层之间提供了一个抽象的接口。

闪存既不满足块设备描述也不满足字符设备的描述。它们表现的类似块设备，但又有所不同。比如，块设备不区分写和擦除操作。因此，一种符合闪存特性的特殊设备类型诞生了， 就是 MTD 设备。所以 MTD 既不是块设备，也不是字符设备。

MTD主要就是为NorFlash和NandFlash设计的，其余像接口映射、RAM、ROM等都是辅助功能。



### 适配SPI FLASH过程

Linux内核使用mtd 框架适配一个 FLASH 的步骤大致如下：





## 主要优化点

1、写时加速

2、关闭SPI控制器休眠功能

