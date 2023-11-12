---
description: 介绍Yocot构建流程，还有一些常用的故常排查手段
---

# 🖖 Page 2.5 Yocto概念介绍

## Yocto项目概述

上面带大家操作如何构建？体验了一整套的构建流程。还有最开始的介绍文档，其实帮大家进行了Yocto的入门。那么我们本质上对于Yocto的基础知识和概念其实还没有做过多的讲解。本个章节是为了补充大家需要了解的和学习到的一些基础知识。

### 1.Yocto的愿景和Yocto是为了做什么？

Yocto Project 是一个开源协作项目，可帮助开发人员为嵌入式产品和其他目标环境创建基于 Linux 的自定义系统，无论硬件架构如何。该项目提供了一套灵活的工具和一个空间，全球嵌入式开发人员可以在其中共享技术、软件堆栈、配置和最佳实践，这些技术、软件堆栈、配置和最佳实践可用于为嵌入式设备创建量身定制的 Linux 和 RTOS 映像。

该项目提供了一个标准来提供硬件支持和软件堆栈，允许软件配置和构建的互换。例如，这可以是硬件供应商和消费者之间的合作，也可以是允许在软件生态系统内部和周围进行协作。这些工具允许用户以可维护和可扩展的方式为多个硬件平台和软件堆栈构建和支持自定义。

从历史上看，该项目是从 OpenEmbedded 项目发展而来的，并与 OpenEmbedded 项目一起工作，OpenEmbedded 项目是项目使用和共享组件的构建系统。

Yocto 项目结合并维护了几个关键元素：

1. OpenEmbedded构建系统，与OpenEmbedded-Core和BitBake组成的OpenEmbedded项目共同维护。
2. 用于测试的参考/示例嵌入式 Linux 配置（称为 Poky）
3. 通过我们基于 Buildbot 的 autobuilder 提供广泛的测试基础设施
4. 集成工具，使嵌入式 Linux 工作成功：
   1. 用于自动化构建和测试的工具
   2. 板级支持定义和交换的流程和标准
   3. 工具安全分析和许可证合规性，SPDX 中的软件清单 （SBoM） 支持

Yocto Project 保护伞中有许多不同的开源组件和工具。

Poky，参考嵌入式操作系统实际上是一个有效的构建示例，它将使用包含的构建系统（BitBake，构建引擎和OpenEmbedded-Core，核心构建系统元数据）构建一个小型嵌入式操作系统。

构建系统是使用 Poky 构建指令“文件”下载的，称为配方和层（定义如下）。您可以更改、复制、扩展或使用 Poky 构建细节，以创建自定义嵌入式 Linux。

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

### 模型：层模型（meta）

Yocto Project 有一个用于创建嵌入式 Linux 的开发模型，这使其与其他简单的构建系统区分开来。它称为层模型。

层模型旨在同时支持协作和自定义。层是包含相关指令集的存储库，这些指令集告诉构建系统要做什么。用户可以协作、共享和重用图层。图层可以随时包含对先前说明或设置的更改。

这种强大的覆盖功能允许您自定义以前的协作层或社区提供的层，以满足您的产品要求。

使用不同的层在逻辑上分隔生成中的信息。例如，您可以有一个 BSP 层、一个 GUI 层、一个发行版配置、中间件或一个应用程序。将整个构建放在一个层中会限制未来的自定义和重用并使其复杂化。另一方面，将信息隔离到层中有助于简化将来的自定义和重用。尽可能使用芯片供应商提供的 BSP 层。

熟悉精选（经过测试）的 YOCTO PROJECT COMPATIBLE LAYER INDEX。还有 [OpenEmbedded 层索引](http://layers.openembedded.org/)，它包含更多层，但内容不太普遍验证。

### 关键模型数据

**Configuration Files 配置文件**：包含变量的全局定义、用户定义的变量和硬件配置信息的文件。它们告诉生成系统要生成什么内容并将其放入映像中以支持特定平台。

**Recipe 配方**：最常见的元数据形式。配方将包含用于构建软件包的设置和任务（说明）列表，然后用于构建二进制映像。配方描述了从何处获取源代码以及要应用哪些补丁。配方描述了库或其他配方的依赖关系，以及配置和编译选项。它们存储在图层中。

**Layer 图层**：相关配方的集合。层允许您合并相关元数据以自定义您的构建，并隔离多个架构构建的信息。层在覆盖先前规范的能力方面是分层的。您可以从 Yocto 项目中包含任意数量的可用图层，并通过在它们之后添加图层来自定义构建。图层索引可在 Yocto 项目中搜索图层。

**Metadata 元数据**：Yocto 项目的一个关键元素是用于构建 Linux 发行版的元数据，包含在构建系统在构建映像时解析的文件中。通常，元数据包括配方、配置文件和其他参考构建指令本身的信息，以及用于控制构建内容和影响构建方式的数据。元数据还包括用于指示使用哪些软件版本的命令和数据，以及从何处获取这些命令和数据，以及对软件本身（补丁或辅助文件）的更改或添加，这些更改或添加用于修复错误或定制软件以在特定情况下使用。OpenEmbedded Core 是一组经过验证的重要元数据。

**OpenEmbedded-Core：oe-core** 是由基础配方、类和相关文件组成的元数据，这些文件在许多不同的 OpenEmbedded 衍生系统（包括 Yocto 项目）中是通用的。它是 OpenEmbedded 社区开发的原始存储库的精选子集，该存储库已被精简为一组更小的、持续验证的核心配方，从而形成了一套严格控制且有质量保证的核心配方。

**Poky**：一个参考嵌入式发行版和一个参考测试配置，用于 1） 提供一个基本级别的功能发行版，可用于说明如何自定义发行版，2） 测试 Yocto Project 组件，Poky 用于验证 Yocto Project，以及 3） 作为用户下载 Yocto Project 的工具。Poky 不是一个产品级别的发行版，而是定制的一个很好的起点。Poky 是 oe-core 之上的集成层。

**Build System 构建系统 – “Bitbake”**：一个调度程序和执行引擎，用于解析指令（配方）和配置数据。然后，它创建一个依赖关系树来对编译进行排序，计划所包含代码的编译，最后执行指定的自定义 Linux 映像（发行版）的构建。BitBake 是一个类似 make 的构建工具。BitBake 配方指定了特定包的构建方式。它们包括所有包依赖项、源代码位置、配置、编译、构建、安装和删除说明。配方还将包的元数据存储在标准变量中。相关配方被合并到一个层中。在生成过程中，将跟踪依赖项，并执行包的本机或交叉编译。作为交叉构建设置的第一步，框架将尝试创建适合目标平台的交叉编译器工具链（可扩展 SDK）。

**Packages 包**：用于创建最终映像的生成系统的输出。

**Extensible Software Development Kit 可扩展软件开发工具包 （ESDK）：**面向应用程序开发人员的自定义 SDK，允许他们将库和编程更改合并回映像中，以使其代码可供其他应用程序开发人员使用。

**Image 镜像**：旨在加载到设备上的 Linux 发行版（操作系统）的二进制形式。

## 2. Yocto 的一些常用的开发规则

虽然所有人都说Yocto的难度和学习路线是比较陡峭的。但是我还是希望能通过我们本系列的文章，让你尽可能的上手它用它来去作为自己的开发工具。

很多时候我们会感觉它的构建过程像一个黑匣子。我们是否可以从通过过程文件来去追溯？找到我们想要的信息。下面是一些默认的大家都会遵守的一些规则。有了这些规则，我们就可以去寻找我们想要的信息。

1. **使用 Git，而不是 tarball 下载：**如果您使用 git，软件将自动更新错误更新 因为 git 是如何工作的。如果您下载压缩包，您将需要 负责您自己的更新。
2. **了解图层索引：**所有图层都可以在[图层索引](https://layers.openembedded.org/)中找到。层 已申请 Yocto 项目兼容状态（结构连续性 保证和测试）可以在 [Yocto Project Compatible 索引](https://www.yoctoproject.org/software-over/layer/)中找到。一般先检查兼容层索引， 如果找不到必要的图层，请检查常规图层索引。这 layer index 是 Open Embedded Project 的原始工件。因此， 该索引没有 Yocto Project 的策展和测试 在 Yocto Project Compatible 图层列表中提供，但后者的图层较少 条目。要知道，当您开始在图层索引中搜索时，并非所有 层具有相同的成熟度、验证或可用性级别。也没有 搜索会优先考虑显示的结果。没有简单的方法可以帮助你 通过选择最适合您需求的层的过程。 因此，它通常是反复试验，检查邮件列表，或者 通过协作室与其他开发人员合作，为您提供帮助 做出正确的选择。
3. **尽可能使用芯片供应商提供的现有 BSP 层：**英特尔、TI、恩智浦和其他公司都有关于使用哪些 BSP 层的信息 他们的硅。这些层的名称为“meta-intel”或“meta-ti”。尝试 不要从头开始构建图层。如果您有定制芯片，请使用以下方法之一 这些层作为指南或模板，并熟悉 [Yocto Project Board 支持包开发人员指南](https://docs.yoctoproject.org/4.3/bsp-guide/index.html)。
4. **不要把所有东西都放在一层中：**使用不同的层在逻辑上分隔生成中的信息。作为 例如，你可以有一个 BSP 层、一个 GUI 层、一个发行版配置、 中间件或应用程序（例如“元文件系统”、“元python”、 “元英特尔”，依此类推）。将整个构建置于一个层限制中 并使未来的定制和重用变得复杂。将信息隔离到 另一方面，层有助于简化未来的自定义和 再使用。
5. **切勿修改 POKY 图层。从不。曾。当您更新到下一个时 释放，您将失去所有工作。所有这一切。**
6.  **不要被文档搜索结果所迷惑：**Yocto Project 文档始终在更新。不幸的是，当你 使用 Google 搜索 Yocto Project 概念或术语，Google 始终如一 搜索并检索旧版本的 Yocto Project 手册。例如 使用 Google 搜索特定主题可能会导致 Yocto Project 手册已有多个版本。确保你是 使用最新的 Yocto Project 文档，使用下拉菜单 其任何页面的顶部。

    许多开发人员通过搜索整个页面来浏览[多合一的“超级”手册](https://docs.yoctoproject.org/singleindex.html)以查找概念或术语。本手册 是 Yocto Project 手册核心集的串联。因此，一个简单的 在本手册中使用 Ctrl-F 进行字符串搜索会生成 所需的术语或概念。一旦你找到你所在的区域 有兴趣，如果需要，您可以显示实际的手册。它也是 可以使用菜单或左侧导航窗格中的搜索栏。
7. **了解构建系统工作原理的基本概念：工作流：**了解 Yocto Project 工作流程很重要，因为它可以帮助你们俩 查明问题发生的位置以及构建是如何中断的。这 工作流分为以下步骤：
   1. Fetch – 获取源代码
   2. Extract 提取 – 解压缩源
   3. Patch 补丁 – 应用补丁以修复错误和新功能
   4. Configure 配置 – 设置环境规范
   5. Build 构建 – 编译和链接
   6. Install 安装 – 将文件复制到目标目录
   7. Package 软件包 – 用于安装的捆绑文件

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

1. **知道您可以生成依赖项图并了解如何执行此操作：**依赖关系图显示配方、任务和目标之间的依赖关系。 您可以在 BitBake 中使用“-g”选项来生成此图形。当你 开始构建，构建中断，您可能会看到您不知道的包 关于或知道为什么构建系统包含它们。这 依赖关系图可以澄清这种混淆。您可以详细了解 依赖关系图以及如何生成它们，请参见 BitBake 用户手册的[“生成依赖关系图](https://docs.yoctoproject.org/bitbake/2.6/bitbake-user-manual/bitbake-user-manual-intro.html#generating-dependency-graphs)”部分。
2.  **以下是在 tmp/work 中解码“magic”文件夹名称的方法：**生成系统提取、解压缩、预处理和生成。如果某事 出错时，构建系统会直接向您报告文件夹的路径 临时 （build/tmp） 文件和软件包所在的位置，由 建。有关此过程的详细示例，请参阅[示例](https://wiki.yoctoproject.org/wiki/Cookbook:Example:Adding\_packages\_to\_your\_OS\_image)。不幸的是，这个 示例位于 Yocto Project 的早期版本上。

    执行构建时，可以使用“-u”BitBake 命令行选项来执行 在依赖关系图中指定用户界面查看器（例如 knotty、 ncurses 或 taskexp），以帮助您更好地理解构建依赖项。
3. **您可以构建的不仅仅是映像：**您可以为特定包（包括 devshell），甚至是单个配方。当开发人员第一次开始使用 Yocto [Project，Yocto Project Quick Build](https://docs.yoctoproject.org/brief-yoctoprojectqs/index.html) 中的说明显示了如何创建映像 ，然后运行或刷新该映像。但是，您实际上可以只构建一个 单一配方。因此，如果某些依赖项或配方不起作用，您可以直接 说“Bitbake foo”，其中“foo”是特定食谱的名称。正如你 使用 Yocto 项目变得更加高级，如果构建失败，它会 对于确保提取本身按预期工作很有用。这里有一些 有价值的链接：[使用开发 Shell](https://docs.yoctoproject.org/dev-manual/development-shell.html#using-a-development-shell)，了解如何使用 devshell。此外，[SDK 手册还展示了如何构建特定配方](https://docs.yoctoproject.org/sdk-manual/extensible.html#use-devtool-modify-to-modify-the-source-of-an-existing-component)。
4.  **一个模棱两可的定义：包装与食谱：**配方包含构建系统用于创建的指令 包。Recipes 和 Packages 是前端和 生成过程的结果。

    如前所述，构建系统采用配方并从 食谱的说明。生成的包与一件事相关 配方正在构建，但是构建的不同部分（包） （即主包、doc 包、debug symbols 包、 单独的实用程序包，依此类推）。构建系统将 软件包，这样你就不需要安装你不需要的软件包，或者 需要，这是有利的，因为您正在为小型设备构建 针对嵌入式和物联网进行开发。
5. **您将希望了解并知道根文件系统中打包的内容。**
6. **创建您自己的映像配方：**有多种方法可以创建自己的图像配方。我们建议您 创建您自己的映像配方，而不是附加现有配方。它 是微不足道的，很容易写出一个形象配方。同样，不要尝试附加到 现有映像配方。创建自己的，并从一开始就做对。
7. **最后，这里列出了您作为一个系统所需的基本技能 开发 人员。您必须能够：**
   * 与公司代理打交道
   * 将包添加到映像
   * 了解配方和包装之间的区别
   * 自行构建一个包以及为什么它有用
   * 找出配方创建了哪些包
   * 找出包中有哪些文件
   * 找出图像中有哪些文件
   * 将 SSH 服务器添加到映像（允许将文件传输到目标）
   * 了解食谱的剖析
   * 知道如何创建和使用图层
   * 查找配方（使用 [OpenEmbedded Layer 索引](https://layers.openembedded.org/))
   * 了解计算机和发行版设置之间的差异
   * 查找并使用适合您硬件的 BSP（机器）
   * 查找发行版功能的示例并知道在哪里设置它们
   * 了解任务管道并执行单个任务
   * 了解 DevTool 及其如何简化工作流
   * 通过共享下载和共享状态缓存提高构建速度
   * 生成和理解依赖关系图
   * 生成并理解 BitBake 环境
   * 构建用于应用程序开发的可扩展 SDK