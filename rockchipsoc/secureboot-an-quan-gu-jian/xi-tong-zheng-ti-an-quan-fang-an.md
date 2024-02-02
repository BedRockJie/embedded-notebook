---
description: 介绍系统整体安全方案
---

# 🔐 系统整体安全方案

整体ARM系统安全所需要覆盖的点如下：

* 系统固件安全
* 安全启动
* 安全通信

系统固件安全及安全启动方案使用Rockchip Linux Secure boot

对系统软件采用签名认证的方式，在设备出厂前对设备操作系统的Image文件进行签名认证，并将公钥的Hash值写入芯片的一次性可编程模块。（OTP）

由于不同文件计算得到的Hash值不同，采用secure boot方案的设备每次启动时都会先校验系统的Hash值，即和芯片内的Hash值进行比较，然后对签名images的一级一级校验，实现从设备芯片到系统软件的链式校验过程，很好地避免设备出厂后没有得到客户签名认证的非授权操作，保护设备中原有的操作系统和软件版本。

Base Secure Boot提供基础的保障到Uboot(loader/trust/uboot)

**如果芯片平台使用的是FIT方案，可以跳过Base Secure Boot操作，FIT已经集成了Base Secure Boot，不再需要额外操作。**

