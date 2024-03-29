---
title: Linux环境一般软件安装方法
tags:
  - Linux
  - 软件安装
categories:
  - Linux
  - 基本使用
top_img: /config/img/cover/material-1.webp
cover: /config/img/cover/material-1.webp
abbrlink: 9f09e5f1
date: 2023-08-29 14:37:25
updated: 2023-08-29 14:37:25
description: 主要介绍在Linux环境下的软件的一般安装方法，主要介绍包管理器以及使用包管理器的安装方法
---

## 前言

在刚开始学习使用 Linux 系统的时候，估计大家都在网上搜索过类似于如何在 Linux 上安装某某软件之类的问题，而且通常需要指明自己所使用的 Linux 发行版才能得到一个实际可行的解决方法。而且当你想要安装另一个不同的软件的时候，你又会像之前一样重新搜索一遍，你是否会发现实际上它们的安装方法都非常类似。

实际上，在 Linux 上安装某个软件，大概就是三种方法，最快速方便的方法就是使用**包管理器**进行安装；第二种是下载软件厂商已经为你编译好的**二进制文件**到系统进行安装；第三种是最耗时间也是出错率比较高的一种安装方法，即下载软件**源代码**，自行编译安装。

使用包管理器进行安装最为方便，但缺点其实也是最多的。对于初学者来说，由于本身对于包管理器没有一个清晰的认识，所以当你到另一个使用不同的包管理器的 Linux 系统上时就可能会无从下手，这也是为什么当你搜索一个软件安装教程的时候通常需要指定实际 Linux 的发行版名称才能得到一个比较可行的教程。另外，实际上包管理器就相当于你手机的应用商店，它的所有软件资源实际上是统一放在一个服务器中供包管理器使用的，由于这个服务器中的软件往往并不是和该软件官网发布的版本同步更新的，所以这也就导致你通过包管理器进行安装的软件要比该软件官网当前版本落后几个版本。

而使用二进制文件或者是使用源代码编译的方式进行安装，则不会有上述两个缺点，但相反要对软件的工作方式以及编译方法比较了解。

{% note info %}

本篇将着重介绍包管理器以及使用包管理器的软件安装方法，而使用二进制文件以及源代码编译的安装方式则简单介绍一下，特别是源代码编译的安装方式，由于各种软件编写方式不同，所以它们通过源代码编译的安装方式多多少少也有一点区别，对于某些软件我将会单独写一篇文章来介绍如何通过二进制文件或者源代码编译的方式进行安装，本篇中主要介绍通过包管理器安装软件的通用方法。

{% endnote %}



## 一、包管理器安装

包管理器一般有两部分，一个是包管理器本身，另一个是包管理器的前端。比如在 Ubuntu 中，包管理器就是 dpkg，而其前端通常是 apt。二者的区别大概就是：包管理器本身如 dpkg 只负责安装现有的安装包，而包管理器的前端通常是从远程服务器下载对应安装包到系统上，然后调用包管理器来对软件进行安装。我们通常都是使用前端在远程获取安装包，但有时当远程服务器没有这个软件的时候，此时如果该软件官网提供了对应包管理器的安装包，我们也可以直接下载这样的安装包，然后使用包管理器进行安装。

比较常见的包管理器及其前端以及它们的使用方法如下：

{% tabs package_managers %}

<!-- tab DPKG -->

dpkg 是 Debian Linux 家族的基础包管理系统，它用于安装、删除、存储和提供`.deb`包的信息。

> **常用命令**：
>
> 参考：[dpkg命令 – 管理软件安装包 – Linux命令大全(手册) (linuxcool.com)](https://www.linuxcool.com/dpkg)

**前端管理器**：APT

由于 APT 比较常用，所以此处仅介绍 APT。

这个是一个 dpkg 包管理系统的前端工具，它是一个非常受欢迎的、自由而强大的，有用的命令行包管理器系统。

Debian 及其衍生版，例如 Ubuntu 和 Linux Mint 的用户应该非常熟悉这个包管理工具。

> **常用命令**：
>
> 参考：[apt-get命令 – 管理服务软件 – Linux命令大全(手册) (linuxcool.com)](https://www.linuxcool.com/apt-get)

<!-- endtab -->

<!-- tab RPM -->

这个是红帽创建的 Linux 基本标准（LSB）打包格式和基础包管理系统。

> **常用命令**：
>
> [rpm命令 – RPM软件包管理器 – Linux命令大全(手册) (linuxcool.com)](https://www.linuxcool.com/rpm)

**前端管理器**：YUM

这个是一个开源、流行的命令行包管理器，它是用户使用 RPM 的界面（之一）。你可以把它和 Debian Linux 系统中的 APT 进行对比，它和 APT 拥有相同的功能。

> **常用命令**：
>
> [yum命令 – 基于RPM的软件包管理器 – Linux命令大全(手册) (linuxcool.com)](https://www.linuxcool.com/yum)

**前端管理器**：DNF

这个也是一个用于基于 RPM 的发行版的包管理器，Fedora 18 引入了它，它是下一代 YUM。

如果你用 Fedora 22 及更新版本，你肯定知道它是默认的包管理器。

> **常用命令**：
>
> [dnf命令 – 新一代的软件包管理器 – Linux命令大全(手册) (linuxcool.com)](https://www.linuxcool.com/dnf)

<!-- endtab -->

<!-- tab PACMAN -->

这个是一个流行的、强大而易用的包管理器，它用于 Arch Linux 和其他的一些小众发行版。它提供了一些其他包管理器提供的基本功能，包括安装、自动解决依赖关系、升级、卸载和降级软件。

> **常用命令**：
>
> [pacman命令 – 软件包管理器 – Linux命令大全(手册) (linuxcool.com)](https://www.linuxcool.com/pacman)

<!-- endtab -->

{% endtabs%}

{% note primary %}

**关于依赖**

Linux 和 Windows 的一个不同的地方就在于：Windows 中所安装的软件包会将自己所用到的所有的例如第三方库之类的依赖和自己的软件主体一起打包到一个安装包中，这方便了开发人员也方便了使用用户，但是却可能造成一个问题，就是有可能你某个软件里使用到了的依赖另一个软件中也用到了，但是它们在系统中却占有两份空间。而 Linux 则采用与之不同的方案，就是当你安装一个软件的时候，需要先安装好该软件的所有依赖才能正常运行，而依赖可能也有依赖，依次类推。这样的方式使软件的空间占有率得以下降，但相反的在安装时需要注意的地方就多了。

当你使用包管理器安装软件的时候，大多数的包管理器都可以自动处理依赖关系，当你安装软件时，包管理器会自动为你安装该软件的所有能找到的依赖，所以通常不用过多担心。实际上，即使没有自动为你安装依赖，在安装时包管理器也会提醒你缺哪些依赖，你再像安装一般软件一样安装依赖就可以了。

{% endnote %}



## 二、二进制文件安装

使用二进制文件安装软件是一个不错的选择，既可以安装到最新（或较新）的软件，过程也比较简单和通用。一般的二进制软件安装过程如下：

- 下载二进制软件，并将其解压，解压包通常放置在一个统一的目录方便管理（通常会选择在`/opt/`目录）
- 将解压包中的可执行程序添加软链到系统查找目录（如`/usr/local/bin/`、`/usr/bin/`等）
- 在任意目录测试该可执行程序是否可以正常执行



## 三、源代码编译安装

该方法的灵活性就比较高了，但也有基本操作步骤：

- 下载源代码
- 配置编译条件
- 编译、安装

从源代码的下载方式，到编译条件的配置方式以及最后编译以及安装的方式都各不相同，就不在此多废话，具体的软件的源代码编译安装方法就去找对应的软件安装教程即可，但还是要提一句，在 Linux 上最常见的软件还是使用 C/C++ 编写的，并且它们的配置以及编译安装的方式也都差不多，通常保证你的系统里有 gcc、g++、make 这几个软件就可以完成编译了。



## 推荐

{% note info %}

**通过下方链接可以前往本博客已经提供的使用二进制文件方法或者源代码编译方法的安装教程：**

- [Git](/posts/4446d03b/)
- [nodejs](/posts/358aad2/)

{% endnote %}
