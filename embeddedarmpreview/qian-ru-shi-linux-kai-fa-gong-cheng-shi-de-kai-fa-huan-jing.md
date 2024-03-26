# 😎 嵌入式Linux开发工程师的开发环境

## 背景

网络上关于开发工具和环境推荐的有一大把，但是在嵌入式领域，却不像其他领域是使用相对专一的开发环境：IDEA、Visio studio等，由于嵌入式的前辈很多、大多数都习惯并熟练使用VIM等代码编辑器。但是这类软件对于一个刚入行的新朋友来说，学习工具的成本会相对比较高。

我们使用工具就是为了解决我们自己的开发问题，所以结合多这么多年折腾工具的经验，结合我自己的工作流、给大家提供一些参考。

* 岗位：嵌入式开发工程师（Linux方向）
* 涉及语言：C/C++、Bash、Makefile、Cmake、Python、
* 涉及框架：Yocto、Linux Kernel、Uboot、Embedded Web（Websocketpp、Asio）、Linux Initrc
* 个人探索方向：Rust、Go

## 涉及工具及开发任务场景

开发使用到的高频工具：

代码编辑器：VScode、NotePad++、Clion、Pycharm、VI

代码调试工具：GDB

其他辅助开发工具：Beyond Compile、Wireshark

### 开发场景：

* Linux内核驱动修改及适配
* Yocto框架修改及适配
* Linux测试脚本
* Linux C/C++ 软件服务开发

## 具体开发场景及软件介绍

前提：嵌入式Linux开发工程师当然离不开一台性能“良好”的编译服务器。这个服务器的性能规格大家就根据自己的业务需求来衡量即可，整个开发的思路就是本地开发、远程编译、上板运行调试这个思路。

环境搭建过程将不在本文中展开，作为一个嵌入式开发工程师，就要有一个勇于折腾让自己更“舒服”的环境的态度。

基础环境需求：Linux服务器可以SSH、Linux开启 Smba共享盘

* Windows 拥有一个顺手的LInux终端连接器

VScode必备插件：

* C/C++： C/C++ 语法及索引支持插件。
* Devide Tree： 设备树语法支持插件
* C/C++ GNU Global：一个类似VIM CTAG的索引源码生成索引文件来进行代码阅读的插件（针对大型工程，如Linux Kernel）
* CMake： CMake 语法及工具支持插件
* Yocto Bitbake： Yocto Bitbake语法高亮支持插件
* Shell-format
* ShellCheck：编写Bash的辅助插件，可以高亮Bash语法，同时提供优秀的错误提示。

### 1. Linux内核驱动修改及内核适配开发

使用到软件Visio Studio Code

插件使用的就是上面所说的必备插件，没有新增使用插件的需求。

由于内核代码量相对比较大，且目录结构比较多，所以我们需要开启Vscode的目录及代码“黏附”功能，将目录结构、和函数声明，黏附在编辑器顶端。

<figure><img src="../.gitbook/assets/image (27) (1).png" alt=""><figcaption></figcaption></figure>

然后就是使用Global 插件进行内核源码的索引，之后就可以快乐进行内核代码阅读和开发了。

### 2. Yocto框架修改适配



### 3.  Linux自动化脚本开发



### 4. Linux C/C++ 应用软件及服务开发

