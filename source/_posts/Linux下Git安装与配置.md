---
title: Linux下Git安装与配置
tags:
  - Linux
  - Git
  - 软件安装
  - 软件配置
categories:
  - Linux
  - 软件安装
top_img: /config/img/cover/material-2.webp
cover: /config/img/cover/material-2.webp
abbrlink: 4446d03b
date: 2023-08-29 16:00:47
updated: 2023-08-29 16:00:47
description: 介绍在 Linux 环境下 Git 软件的安装方法与基本配置方法，本教程不局限于任何 Linux 发行版
---

## 一、安装

{% tabs installgit %}

<!-- tab 包管理器安装 -->

本篇不会特别指出任何使用包管理器安装 Git 的方法，如果你对包管理器不了解或者说不知道自己的系统的包管理器如何使用，我强烈建议你移步我的另一篇文章：[Linux 环境一般软件安装方法](/posts/9f09e5f1/)，该篇文章详细说明了使用包管理器安装软件的一般方法，以后也就不用每次安装软件时都上网搜索了！

<!-- endtab -->

<!-- tab 源码编译安装 -->

1. **下载源码**

   你可以在 https://git-scm.com/downloads 看到官网上所标注的最新的源代码版本，然后通过 https://mirrors.edge.kernel.org/pub/software/scm/git/ 找到对应的版本下载即可。我所下载的文件是：`git-2.42.0.tar.gz`

   ```bash
   wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.42.0.tar.gz
   ```

   > **注意**：
   >
   > 1. 可以在任何目录使用该命令，但如果是一些访问受限的系统目录下，需要登录`root`用户执行或在命令前加上`sudo`，后续命令同理
   > 2. 关于`wget`工具的用法可以通过`wget --help`进行查看，此处不详细介绍

2. **解压压缩包**

   我们下载下来的文件是一个`.tar.gz`的压缩包，需要用`tar`工具进行解压，如果没有该工具，则通过包管理器下载即可，包名称就叫`tar`。以下是解压命令

   ```bash
   tar -zxvf git-2.42.0.tar.gz
   ```

   > **注意**：
   >
   > 1. 关于`tar`工具的用法可以通过`tar --help`进行查看，此处不详细介绍
   >
   > 2. 解压出来的目录名应该是`git-2.42.0`，考虑到本教程的通用性，我选择将其重命名为`git`：
   >
   >    ```bash
   >    mv git-2.42.0 git
   >    ```
   >
   >    后续也请读者以`git`为准

3. **依赖安装**

   Git 的依赖库包含：`autotools`、`curl`、`zlib`、`openssl`、`expat`和`libiconv`。它们的包名分别是：`dh-autoreconf`、`curl-devel`、`expat-devel`、`gettext-devel`、`openssl-devel`、`perl-devel`、`zlib-devel`，请使用包管理器自行安装以上依赖库，本篇不提供包管理器的使用方法。

   > **注意**：
   >
   > 使用 RHEL 和 RHEL 衍生版，如 CentOS 和 Scientific Linux 的用户可能需要开启 EPEL 库

4. **编译安装**

   依次执行以下命令：

   ```bash
   cd git				# 进入之前解压出来的 git 源代码目录下
   make configure			# 生成配置文件
   mkdir /opt/git			# 在 /opt 下创建一个新目录用于安装 git
   ./configure --prefix=/opt/git	# 修改配置中的安装位置到 /opt/git
   make				# 编译
   make install			# 安装
   ```

5. **将 git 安装路径添加到环境变量**

   ```bash
   echo "export PATH=$PATH:/opt/git/bin" >> /etc/profile
   source /etc/profile
   ```

6. **测试 git**

   ```bash
   git --version
   ```

<!-- endtab -->

{% endtabs %}



## 二、配置

### 1. 配置 SSH 连接

{% note info %}

如果你使用 git 是为了能够往远程仓库推送内容，我推荐你配置一下 SSH 连接。而如果仅仅是为了克隆仓库到本地而不进行任何推送，则完全可以不配置 SSH 连接。

首先 ssh 和 git 是两个完全分离的工具，不要将它们混到一起。ssh 是 git 支持的一种传输方式，要启用这种传输，首先需要配置 ssh 密钥，从而将本地机器和远程 Github 服务器连接起来。

{% endnote %}

- **创建 SSH Key**

  ```bash
  ssh-keygen -t rsa -C "<your name>"
  ```

  {% note warning %}

  如果以前创建过这个密钥，则可以跳过这一步，通常在`~/.ssh/`目录下就可以找到以前创建的密钥文件。

  {% endnote %}

  {% note info %}

  输入该命令之后会有一些输出，也会让你输入一些内容，比如创建密钥的位置，使用密钥的密码等等，通常保持默认即可，即一路回车。

  {% endnote %}

  创建成功后你可以看到如下输出：

  ```
  Your identification has been saved in /home/Username/.ssh/id_rsa.
  Your public key has been saved in /home/Username/.ssh/id_rsa.pub.
  The key fingerprint is:
  SHA256:RwvBINgH8CEt2KniltmykeyDsOseUYcwMzehFeyT86s emailnum@email.com
  The key's randomart image is:
  +---[RSA 2048]----+
  | o+%OO+o.        |
  |..=+%*+ ..       |
  | ..+o+o.. .      |
  |o.  o=.  o .     |
  |o oolalala S o      |
  | +.+.. . .       |
  |. .o    .        |
  |  . .  .         |
  |   . E.          |
  +----[SHA256]-----+
  ```

- **复制公钥**

  如果按照默认配置，则你的公钥文件`id_rsa.pub`应该放在`~/.ssh/`目录下，使用任何编辑器如 vim、gedit 打开该文件都可以，然后复制其中内容

- **配置 Github SSH 密钥**

  进入自己的 Github 主页，依次点击【Setting】->【SSH and GPG Keys】->【New SSH Key】按钮，Title 任意内容皆可，通常我比较喜欢和自己在创建 ssh 密钥时填写的名字一样。

  然后将刚刚复制的密钥内容粘贴到下方文本框中，保存即可。

- **测试 ssh 连接**

  ```bash
  ssh -T git@github.com
  ```

  第一次使用该密钥连接时会询问是否继续连接，输入`yes`回车确认即可，最后如果看到如下输出则说明配置成功了：

  ```
  Hi You! You've successfully authenticated, but GitHub does not provide shell access.
  ```



### 2. 其他配置

```bash
git config --global user.name "<你的用户名>"
git config --global user.email "<你的邮箱>"
git config --global core.quotepath false		# 解决中文路径显示乱码的问题
```
