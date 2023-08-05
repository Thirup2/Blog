---
title: CMake入门
tags:
  - 教程
  - CMake
  - C/C++
categories:
  - C/C++
  - 工具
top_img: /img/cover/material-6.webp
cover: /img/cover/material-6.webp
description: 主要介绍 CMake 的基本使用方法，包括基础的变量和语法
abbrlink: 557a8896
date: 2023-07-27 21:13:08
updated: 2023-07-27 21:13:08
---

## 一、为什么需要 CMake

实际上这个问题在我的另一篇文章（[CLion入门](https://blog.syunn.cn/posts/872d1bb0/#%E4%B8%80%E3%80%81-CMake)）里也写到过，这里再简单说一下。

当我们需要编译的程序就只有几个甚至只有 1 个的时候，你肯定不会想去写 CMake 或是 Makefile，因为它们至少都要写几排，而使用 gcc 命令短短的一行就可以直接搞定了。

而当我们的代码逐渐多起来了之后，特别是当整个项目里又有几个子项目的那种，比如一个项目包含几个动态库模块，然后主项目需要使用到这几个动态库模块的情况。在这种情况下，我们如果还是使用 gcc 来编译项目，那么可能就得先依次使用好几个 gcc 命令来编译这整个项目。如果我们的项目还在测试阶段，那每次修改了代码，每次都得在终端输入这些命令，很麻烦而且易出错。解决这个问题的办法有两个，其中之一就是 Makefile。

这是一种脚本文件，大概的用法就是你在里面写上编译这个项目需要的命令，然后每次编译的时候，使用 make 工具执行一下该文件就可以了（当然，实际的 Makefile 文件需要按照规范写，不只是写上命令），这样我们是不是又回到了只用一行命令就可以解决的情况了呢。

然而，即便如此，依然还是存在问题，也就是跨平台的问题。首先如果你在 Linux 上编写 Makefile 文件并测试，或许这个 Makefile 并不适合在 Windows 上进行使用。比如我刚开始学习 Makefile 的时候就遇到过，我在 Linux 上写的一个 Makefile 的删除文件命令使用了 rm 指令，但是在 Windows 的 cmd 环境下，删除文件应该使用 del 命令，而且各种命令的参数还不一样。与之类似的区别还有编译器命令的区别等等。虽然 Makefile 也有办法可以做到跨平台，但是会需要做比较多的工作，而 CMake 就是简化这样一种工作的工具。



## 二、什么是 CMake

CMake 是 Makefile 这类工具的上层抽象，Makefile 文件里的内容就是用于描述编译该项目的具体过程的，而 CMake 的配置文件（叫做 CMakeLists.txt）里的内容就是描述该项目本身的，比如项目名字是什么，项目用到的 C/C++ 语言的标准是什么，你有哪些子项目，这些子项目分别叫什么名字，编译的先后顺序如何，编译需要的源文件、库文件等等。

通过这样的抽象，我们往往就只需要编写这样一个 CMake 配置文件就可以全平台通用了，因为我们可以通过它来生成我们需要的构建脚本。比如在 Linux 下，我们对这 CMake 说：”我需要你根据我在 CMakeLists.txt 里的描述生成一个可以编译该项目的 Makefile 文件“，然后它就会给你生成一个 Makefile 文件，你通过这个文件就可以正确编译整个项目。如果你在 Windows 下，也可以让它生成一个 Visual Studio 项目进行编译，而用于生成这些构建脚本的 CMakeLists.txt 文件都是相同的。

以上是从其用途解释了一下 CMake，下面是 CMake 官方的描述：

> CMake is an open-source, cross-platform family of tools designed to build, test and package software. CMake is used to control the software compilation process using simple platform and compiler independent configuration files, and generate native makefiles and workspaces that can be used in the compiler environment of your choice. The suite of CMake tools were created by Kitware in response to the need for a powerful, cross-platform build environment for open-source projects such as ITK and VTK.
>
> **ChatGPT 3.5 翻译**：CMake是一个开源的、跨平台的工具系列，旨在构建、测试和打包软件。CMake被用于通过简单的平台和编译器无关的配置文件控制软件编译过程，并生成本地的makefile和工作空间，以便在您选择的编译器环境中使用。CMake工具套件是由Kitware创建的，以满足开源项目（如ITK和VTK）对强大的跨平台构建环境的需求。



## 三、使用方法



****
