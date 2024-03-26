---
description: 添加自己想添加的工具&软件包
---

# 🖖 Page 3 定制Yocto系统

## 为什么要定制？

上篇文章说到，我们已经成功构建了一个可以在Qemu上运行的嵌入式Linux系统，其实定制自己的系统，就是将当前系统中的关键特征修改为具有“标识”意义的特征关键词，细心的朋友已经发现了，当前我们构建的系统是基于Poky来构建的，所以系统中的特定标识是Poky：

<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

Yocto给我们提供了明确的修改思路，我们集成自Yocto已有的发行版信息，近修改我们需要的信息即可。

## 修改配置

修改配置之前需要各位略懂一些。Bash语法和Python的语法，两个组合在一起就是YP的Bitbake语法。

### 1. 新建自己的meta层

在创建新图层之前，您应该 确保有人尚未创建包含元数据的图层 你需要。您可以查看 [OpenEmbedded](https://layers.openembedded.org/) 社区中可用于 Yocto 项目。您可以找到一个相同或接近的图层 满足您的需求。

我们创建自己的meta层及配置文件：

```sh
# 使用工具
bitbake-layers create-layer [-h] [--layerid LAYERID] [--priority PRIORITY] [--example-recipe-name EXAMPLERECIPE] [--example-recipe-version VERSION] layerdir
# 创建自己的meta层，名字叫meta-rockembedded
bitbake-layers create-layer meta-rockembedded
```

当前层的配置文件：

```bash
# We have a conf and classes directory, add to BBPATH
BBPATH .= ":${LAYERDIR}"

# We have recipes-* directories, add to BBFILES
BBFILES += "${LAYERDIR}/recipes-*/*/*.bb \
            ${LAYERDIR}/recipes-*/*/*.bbappend"

BBFILE_COLLECTIONS += "meta-rockembedded"
BBFILE_PATTERN_meta-rockembedded = "^${LAYERDIR}/"
BBFILE_PRIORITY_meta-rockembedded = "6"

LAYERDEPENDS_meta-rockembedded = "core"
# 这个信息比较关键，表示当前meta对Poky的依赖版本
LAYERSERIES_COMPAT_meta-rockembedded = "kirkstone"
```

当前层的目录结构：

```
.
├── conf
│   └── layer.conf
├── COPYING.MIT
├── README
└── recipes-example
    └── example
        └── example_0.1.bb
```

当前层结构里面自己带了一个example,这个example是打印当前层的hello信息。

### 2. 添加层到项目中

通过自带的命令可以添加进来，也可以修改配置文件。

```
bitbake-layers add-layer ../meta-rockembedded
```

配置文件路径：build/conf/bblayers.conf

```
# POKY_BBLAYERS_CONF_VERSION is increased each time build/conf/bblayers.conf
# changes incompatibly
POKY_BBLAYERS_CONF_VERSION = "2"

BBPATH = "${TOPDIR}"
BBFILES ?= ""

BBLAYERS ?= " \
  ${TOPDIR}/../meta \
  ${TOPDIR}/../meta-poky \
  ${TOPDIR}/../meta-yocto-bsp \
  ${TOPDIR}/../meta-rockembedded \
  "
```

### 3. 验证是否添加成功

使用工具：`yocto-check-layer meta-rockembedded`

```
yocto-check-layer meta-rockembedded
```



<figure><img src="../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

可以看到当前能够识别我们的层，但是我们的存还存在很多问题，需要添加在自测试时需要支持的用例。

验证默认的example是否被成功智行：

```
bitbake example
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

## 修改发行版名称

我们现在的发行版名称上面已经说了是poky，现在我们需要在自己的层中声明自己的发型版。

发行版关键词：DISTRO 该变量在 build/conf/local.conf 中声明，当前是：

```bash
DISTRO ?= "poky"
```

这个文件的配置路径是：meta-poky/conf/distro/poky.conf  我们这里列一些重要的配置变量大家可以自行查看这个文件中的信息。

```bash
DISTRO = "poky"
DISTRO_NAME = "Poky (Yocto Project Reference Distro)"
#DISTRO_VERSION = "3.4+snapshot-${METADATA_REVISION}"
DISTRO_VERSION = "4.0.13"
DISTRO_CODENAME = "kirkstone"
SDK_VENDOR = "-pokysdk"
SDK_VERSION = "${@d.getVar('DISTRO_VERSION').replace('snapshot-${METADATA_REVISION}', 'snapshot')}"
SDK_VERSION[vardepvalue] = "${SDK_VERSION}"

MAINTAINER = "Poky <poky@lists.yoctoproject.org>"

TARGET_VENDOR = "-poky"
```

我们的修改方式是在我们自己层中，创建我们发现版的配置文件，并将这个文件引用过来修改我们需要修改的重要的配置信息。

创建文件并添加内容如下：`meta-rockembedded/conf/distro/rockembedded.conf`

```bitbake
require conf/distro/poky.conf

DISTRO = "rockembedded"
DISTRO_NAME = "rockembedded (Bedrock Build Linux by Yocto)"
#DISTRO_VERSION = "3.4+snapshot-${METADATA_REVISION}"
DISTRO_VERSION = "4.0.13"
DISTRO_CODENAME = "kirkstone"
SDK_VENDOR = "-rockembeddedsdk"
SDK_VERSION = "${@d.getVar('DISTRO_VERSION').replace('snapshot-${METADATA_REVISION}', 'snapshot')}"
SDK_VERSION[vardepvalue] = "${SDK_VERSION}"

MAINTAINER = "rockembedded <bedrock_jie@foxmail.com>"

TARGET_VENDOR = "-rockembedded"

```

修改构建工程的参考发行版文件：

```bitbake
DISTRO ?= "rockembedded"
```

### 构建并验证

重新构建并生成镜像文件。

```
bitbake example
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>

YP会自己删除原来已有的构建过程，并重新按照新的发行版开始构建。

<figure><img src="../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

新的启动信息如下，可以看到我们已经获得了属于自己的Linux发行版。

<figure><img src="../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>
