---
description: 从零开始学习实操Yocto构建
---

# ✌ Page 2 Yocto构建流程

上文介绍了Yocto的背景及其为什么选择Yocto，本节我们就从零开始，基于Yocto官方的发行版。进行从零的构建。下面我们分步骤进行。 在此之前推荐大家先阅读一下我之前写的Linux系统的组件。了解一下嵌入式开发过程中常用的流程和嵌入式系统是由什么组成的。

> [https://zhuanlan.zhihu.com/p/650032014](https://zhuanlan.zhihu.com/p/650032014)

本文章参考官方文档：

> [https://docs.yoctoproject.org/index.html](https://docs.yoctoproject.org/index.html)

## 1. 准备工作

选择Poky版本：

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

在开始构建嵌入式系统之前，需要进行一些准备工作。这包括安装Yocto构建环境、下载所需的软件包和组件，以及配置构建环境的相关设置。

假设你已经拥有了一个Linux发行版，本文在书写时使用的为Windows自带虚拟机 Hyper-V。

### 环境搭建

```sh
sudo apt install gawk wget git diffstat unzip texinfo gcc build-essential chrpath socat cpio python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping python3-git python3-jinja2 libegl1-mesa libsdl1.2-dev python3-subunit mesa-common-dev zstd liblz4-tool file locales libacl1
sudo locale-gen en_US.UTF-8
```

## 2. 创建构建目录

Yocto提供了脚本oe-init-build-env来创建新的构建环境。该脚本设置构建环境的目录结构，并初始化配置文件。

```
source oe-init-build-env build
```

通过环境变量的声明，我们就创建好了我们我们的系统构建镜像文件及构建参数文件。

## 3. 配置和定制

其中的conf/local.conf文件，保存了定制你的嵌入式Linux的各个变量设置。对于我们的第一个Yocto项目，建议修改以下两个变量。

* DL\_DIR：软件包下载的目录，默认的设置是在构建环境目录下的downloads目录，建议修改到构建环境以外的目录，这样，当你新建一个构建环境时，不用重复下载这些软件包。（Yocto项目构建的大部分时间都浪费在下载这些软件包上）
* SSTATE\_DIR：共享的状态缓存文件目录，默认的设置是在构建环境目录下的sstate-cache目录，建议修改到构建环境以外的目录。因为在构建过程中，很多任务会产生大量的中间输出，而这些中间输出对将来其他的构建来说时可以重用的，这样可以大大加速构建过程。

我们修改如下配置参数：

```sh
MACHINE ??= "qemuarm64"
DL_DIR ?= "${TOPDIR}/downloads"
```

MACHINE对应的构建配方在Poky中可以找到：`meta/conf/machine/qemuarm64.conf`

对应我们构建的系统是运行在qemuarm64上的系统，所以当前我们的系统只能通过qemu来运行，并不需要真实的单板硬件环境就可以跑起来了。

## 4. 构建和编译

一旦配置和定制完成，就可以开始构建和编译嵌入式系统。Yocto使用BitBake工具来管理构建过程。BitBake根据配置文件和层的设置，自动化地下载、编译和构建所需的软件包和组件。这个过程可能需要一些时间，具体取决于系统的复杂性和硬件的性能。

```
bitbake core-image-minimal
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Yocto首先会下载自己构建所需要的环境并进行安装

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

编译结束后，我们的Linux镜像就生成了。

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

## 5. 镜像生成

我们生成的镜像在：yocto-poky/poky/build/tmp/deploy/images/qemuarm64 目录中，镜像文件如下：

```
文件系统镜像
core-image-minimal-qemuarm64-20231111024701.rootfs.ext4
内核镜像
Image--5.15.124+git0+f484a7f175_f0e7afd594-r0-qemuarm64-20231111024701.bin
```

使用Qemu启动只需要有内核和文件系统即可，真实的单板系统还需要有bootrom和Uboot等引导程序。

## 6. 测试和验证

我们可以启动qemu来验证我们的镜像程序：

<pre><code><strong>默认以Xserver来运行
</strong><strong>runqemu qemuarm64
</strong><strong>在当前终端中运行
</strong>runqemu qemuarm64 nographic
</code></pre>

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

键盘上的Ctrl + a，然后再按下x键。可以停止qemu。
