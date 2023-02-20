---
title: Ubuntu基本配置
date: 2022-12-01
---

# 一. 前言

## 1. 安装环境

系统：Ubuntu-22.04.1

主机：Lenovo XiaoXinAir-14ARE 2020

内存：16G

处理器：AMD® Ryzen 5 4600u with radeon graphics × 12

显卡：AMD® Renoir

硬盘：512 GB



## 2. 写在前面

本文仅作为物理机安装Ubuntu系统的参考，虚拟机大部分操作类似，但如果想安装虚拟机，请寻找更合适的文章。

另外，本文很大程度上是为了帮助我自己重装系统，所以很多地方的特殊性会比较强，可能不适合其他的电脑，希望大家自己判断。

最后，本文参考了很多的文章，但不打算列出，因为我基本上只从那些文章中抽取出了一小部分我需要的，然后组合成了这一篇文章（主要是因为参考的文章实在是太多了 -_-，然后在我安装的时候也没有记录那些文章，只记录了需要的操作，然后这篇文章又是后来写的，所以基本上很难去找了）



# 二. 安装Ubuntu

## 1. 下载ISO文件

首先我们选择一个镜像源，我选择的是北京外国语大学的镜像：https://mirrors.bfsu.edu.cn/

然后找到我们要下载的文件，是`/ubuntu-releases/22.04.1/`这个目录下的`ubuntu-22.04.1-desktop-amd64.iso`，下载即可



## 2. 制作启动U盘

首先我们需要一个制作启动U盘的软件，是软碟通，通过这个链接可以下载：https://cn.ultraiso.net/xiazai.html

具体的使用方法是：进入软件->打开下载的iso文件->点击“启动-写入硬盘映像”->选择用于制作启动盘的U盘->点击"写入"->等待完成即可



## 3. 启动BIOS设置U盘启动

各个电脑打开BIOS的操作可能不一样，总之这一步自己查询，进到BIOS里就行了。

然后设置BIOS启动即可，由于各个电脑的BIOS界面可能也不同，所以就不详细介绍了，记录一下我自己的操作：直接从`Boot Menu`中选择U盘启动即可。



## 4. 开始安装

安装的其他步骤都很简单，唯一需要介绍的一步是：分区操作

首先我是想安装一个和Windows的双系统，先简单介绍一下需要分配的分区：

- `\boot`：由于我只有一个物理硬盘，所以从硬盘里直接分出想要的空间大小即可（这一部分空间应该显示为未分配）。如果是多个物理硬盘，那么一定要从Windows系统盘所在的硬盘中分出一部分留给`\boot`分区，这个分区是放Ubuntu的启动程序分区，由于计算机在启动时只会在一个盘里查找启动程序，如果不在同一个硬盘存放启动程序，那么启动电脑时就只会启动Windows。`\boot`分区留个300M-500M就行了。（我直接在这个盘里分了200个G用作所有的空间）
- `swap`：还有一个比较重要的分区是`swap`分区，它相当于是内存的缓冲，如果有固态硬盘，一般也用固态硬盘来分，主要是为了更快的速度。这一块的大小根据内存的大小决定，一般内存为8G及以下的，留内存的双倍即可；如果内存大于等于16G，那留与内存相同的空间即可。我留16G即可。
- `\home`：该分区是放置用户文件的分区，主要根据自己的使用习惯分配即可。我一般不把安装的软件放在这个目录下，所以我留了30个G左右
- `\`：根分区里放置了所有的文件，包括刚刚分配的`\home`分区，但是由于我们将`\home`分区单独挂载了，所以为根目录分配的空间将不包括`\home`的空间，但是逻辑上还是有`\home`分区的。我把剩下的150G左右都留给了根目录

然后简单来说一下分区的方法吧：

- 首先，我们在需要分区的安装步骤处，选择最后一项：自己分区
- 如果是新配的电脑，还没安装任何系统的话，那么这个页面应该只列出了相应的设备，即硬盘。我们选择需要分配内存的硬盘，选择新建分区表即可，然后按照下面的步骤继续
- 我们可以看到分区表里那一个或多个后面显示“空闲”的空间，我们可以通过前面的空间大小知道它是我们之前分出来用来作哪个分区的。我们选择相应的空间，点击左下角的`+`号，然后就会进入分配空间大小及分区挂载的界面

接下来，我们就来说一下各个分区的具体配置吧：

- `swap`：大小根据之前说的方法来选择；分区选择逻辑分区；挂载点里没有`swap`挂载点，这个在挂载点上面的“用于”的下拉框中选择“交换空间”即可
- `\boot`：大小分配300-500M；分区选择逻辑分区；类型为`ext4`；挂载点里选择`\boot`
- `\`：大小自行分配（我是150G）；分区选择逻辑分区；类型为`ext4`；挂载点里选择`\`
- `\home`：大小自行分配（我是30G）；分区选择主分区；类型为`ext4`；挂载点里选择`\home`

最后，也是最重要的一步：

首先，我们可以看到安装程序已经自动为我们选择的分区分配了设备名，我们找到刚刚`\boot`分区的设备名，然后**在“安装启动引导器的设备”中选择刚刚`\boot`分区的设备名即可**。

点击“现在安装”，等待安装完成即可。



# 三. 换源

由于Ubuntu官方的源下载速度很慢，所以需要换源。

首先，我们选择一个镜像源，我选择的还是北京外国语大学的镜像源：https://mirrors.bfsu.edu.cn/help/ubuntu/

打开这个页面就可以看到它提供的镜像源和替换方法，这里记录一下手动替换的操作：

- 首先将原本的镜像源备份：`sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup`
- 通过`sudo gedit /etc/apt/sources.list`打开并编辑镜像源文件
- 然后将该文件里原本的内容删除，然后将网页里的内容复制粘贴到该文件里，点击保存即可

然后我们就可以进行更新操作了，执行下面两步操作：

```bash
sudo apt update
sudo apt upgrade
```



# 四. 其他安装项

## 1. fastgithub

### 下载

下载地址：https://github.com/dotnetcore/FastGithub/releases/download/2.1.4/fastgithub_linux-x64.zip

如果下载速度慢，我们可以使用下面的网站提供的加速服务：https://ghproxy.com/

> 上面提供的下载地址为2.1.4版本，由于版本可能更新，可以通过下面的网址查看最新版本：https://github.com/dotnetcore/FastGithub/releases ，根据自己的系统和架构选择应该下载的软件包即可。



### 安装

- 我们将下载下来的软件包解压，然后将其移动到`/opt`目录下，然后自己移动到`fastgithub`文件夹下

- 首次运行一下该程序，生成证书

  ```bash
  sudo ./fastgithub
  ```

- 可以看到证书已经生成了，我们先不管，先设置一下代理：打开网络设置，点击网络代理后面的设置按键，选择自动，然后在下面的框中输入：http://127.0.0.1:38457 即可

- 我们将刚刚生成的证书文件移动到`/usr/local/share/ca-certificates`文件夹下

  ```bash
  sudo cp cacert/fastgithub.crt /usr/local/share/ca-certificates
  ```

  然后执行更新操作

  ```bash
  sudo update-ca-certificates
  ```

- 同时浏览器需要导入证书，具体操作步骤如下：打开浏览器->设置->隐私与安全->证书->查看证书->证书颁发机构，导入`cacert/fastgithub.crt`，勾选“信任由此证书颁发机构来标识网站“

- 然后添加一个 git 全局配置

  ```bash
  git config --global http.sslverify false
  ```

- 最后将程序添加软链用来快捷打开：

  ```bash
  sudo ln -s /opt/<fastgithub软件包>/fastgithub /usr/local/bin
  ```



## 2. git

### 安装

```bash
sudo apt install git
```



### 配置

- 添加SSH key

  1. 首先创建SSH密钥

     ```bash
     ssh-keygen -t rsa -C "<你的用户名>"
     ```

     接下来会有一些输出，主要是key的存放位置和密码设置，直接默认回车即可

     最后看到类似如下的输出，就表示成功生成SSH key了：

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

  2. 复制密钥

     密钥内容放在用户文件夹下的`.ssh/id_rsa.pub`文件中，可以打开文本编辑器进行复制；

     然后进入自己的github主页，依次点击：Settings->SSH and GPG keys->New SSH key按钮，Title随便输，然后在下面的文本框粘贴刚复制的内容，最后点击保存即可

  3. 测试SSH连接

     ```bash
     ssh -T git@github.com
     ```

     输出会首先询问是否确认连接，输入yes回车确认即可，最后会看到如下输出：

     ```
     Hi You! You've successfully authenticated, but GitHub does not provide shell access.
     ```

- 其他配置项

```bash
git config --global user.name "<你的用户名>"
git config --global user.email "<你的邮箱>"
git config --global core.quotepath false		/* 解决中文路径显示乱码的问题 */
```



### 克隆

之所以设置SSH key，就是为了这一步，SSH协议是git专用的协议，通过该协议克隆的仓库进行操作是不需要密码的。在克隆时可以在仓库首页的绿色按钮`CODE`的下拉框里找到对应的SSH的仓库地址，使用：

```bash
git clone git@github.com:UserName/Repository.git
```

即可使用SSH协议进行克隆



## 3. vscode

### 下载

直接在官网下载`.deb`文件：https://code.visualstudio.com/



### 安装

通过下面的命令安装即可：

```bash
sudo dpkg -i <packagename>
```

然后打开软件登陆同步配置即可



## 4. typora

### 下载

直接在官网下载`.deb`文件：https://typoraio.cn/#linux



### 安装

通过下面的命令安装即可：

```bash
sudo dpkg -i <packagename>
```

然后打开软件设置激活即可



## 5. gcc

直接通过下面的命令即可安装 gcc：

```bash
sudo apt install gcc
sudo apt install g++
```



## 6. node

### 安装

我们通过二进制文件安装：

首先，我们去官网下载二进制文件：http://nodejs.cn/download/ ，选择对应的系统和架构选择下载即可，下载下来是一个压缩包，我们首先解压缩：

```bash
tar xf <packagename>
```

然后将解压后的包移动到`/opt`目录下

首先运行一下程序：

```bash
./<node_packagename>/bin/node -v
```

我们可以看到以下输出：

```
v16.18.1
```

接下来我们设置软链即可：

```bash
ln -s /opt/<node_packagename>/bin/npm /usr/local/bin/
ln -s /opt/<node_packagename>/bin/node /usr/local/bin/
```

安装就完成了，测试一下：

```bash
node -v
npm -v
```

如果正常显示版本号就表明成功了



### npm换源

通过下面的命令即可换源：

```bash
npm config set registry https://registry.npm.taobao.org
```

然后通过下面的方法来验证是否成功：

```bash
npm config get registry
```



### npm安装位置权限问题

> **注意**：我使用的是修改默认全局安装路径的权限的方法，介意的可以使用其他方法

首先我们查看一下npm的默认安装位置：

```bash
npm config get prefix
```

对于大多数系统显示目录为：`/usr/local`，如果显示为`/usr`请使用其他方法

然后修改路径权限：

```bash
sudo chown -R $(whoami) $(npm config get prefix)
```

或者

```bash
sudo chown -R $(whoami) $(npm config get prefix)/{lib/node_modules,bin,share}
```

即可



### 测试

安装软件来测试一下权限问题是否得到解决：

```bash
npm install hexo-cli -g
npm install docsify-cli -g
```



## 7. JetBrains

### 下载

主要有两种下载安装方法，对于只需要 JB 中的某一个软件的用户来说，直接去相应软件的下载页面下载即可，这里不进行详述。

对于需要多个 JB 软件的用户来说，可以先下载一个 JetBrains ToolBox ，然后在这个软件里下载需要的软件，同时该软件还可以对项目进行管理，非常方便，接下来简单说一下下载方法。

首先直接前往软件的下载页面：[ToolBox App](https://www.jetbrains.com/zh-cn/toolbox-app/?_gl=1*1bx53n1*_ga*OTY4ODkxMTkxLjE2NzUwNzkwOTg.*_ga_9J976DJZ68*MTY3NTE2MjgxNy4yLjEuMTY3NTE2MjgzMy4wLjAuMA..&_ga=2.17468218.2027988100.1675079099-968891191.1675079098)

然后直接下载页面推荐的文件格式即可（我是 Ubuntu，所以选择 .tar.gz 格式的文件），点击下载即可

在等待下载的这一段时间里，我们可以点击下载按钮下方的 **系统要求**，来查看在安装之前需要做的准备：

![图片](https://user-images.githubusercontent.com/91216205/215746175-3041b6e1-7051-41e7-b88f-d0b927988074.png)

可以看到，在 Linux 环境下，我们还需要下载一些软件包才能正常使用 ToolBox

我们打开终端，输入以下命令即可：

```bash
sudo apt install libfuse2 libxi6 libxrender1 libxtst6 mesa-utils libfontconfig libgtk-3-bin
```



### 安装

下载好 ToolBox 的安装包文件之后，输入下面的命令解压缩即可：

```bash
tar -xvf <package-name>
```

然后运行一下解压之后的文件夹里的可执行文件即可：

```bash
./jetbrains-toolbox
```

如果能够看到下面的界面，就说明安装成功了：

![图片](https://user-images.githubusercontent.com/91216205/215747799-2ba979f0-2b76-4eca-bbe2-3a802a84c208.png)

然后就可以在 ToolBox 里安装需要的软件了。



## 8. Chrome

首先移动到安装 Chrome 的目录，然后执行下面的命令下载最新的`.deb`软件包：

```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
```

然后执行下面的命令安装即可：

```bash
sudo apt install ./google-chrome-stable_current_amd64.deb
```



## 9. Clash For Windows

安装完 Chrome 之后，同步书签是个问题，我尝试了很多办法，就这个办法最简单且好用。

首先我们需要一个可订阅的代理服务器，这里我选择的是 WestWorld，连接比较稳定快速，最重要的是经常可以白嫖，同时还有各个平台的客户端应用，哪怕买了套餐也不用担心只在 Linux 上可用。

WestWorld 的官网是： https://xbsj5632.website/

具体的使用方法就不多介绍了。

由于基本上这些软件都不会提供 Linux 的客户端，所以我们需要一个代理工具，用于将我们自己的电脑和 VPN 提供的服务器连接起来。

首先是下载页面： https://github.com/Fndroid/clash_for_windows_pkg/releases

![截图 2023-02-13 22-31-34](https://user-images.githubusercontent.com/91216205/218485889-98ea7548-81ee-4ecd-8eb9-7217b0281a14.png)

我们选择 `*-x64-linux.tar.gz` 版本下载即可。

下载完成后将该压缩包解压，直接执行解压后文件夹中的 `cfw` 程序，会打开一个窗口：

![截图 2023-02-13 22-34-42](https://user-images.githubusercontent.com/91216205/218486550-6266a321-bb97-4765-af33-d8638e2fd94b.png)

这个软件就安装成功了，后面我们需要用到这里的 Port 值 7890

接下来我们打开 WestWorld 官网，进入到 “客户端下载” 页面，然后选择 “Linux” 选项，可以看到最顶部有两个链接，一个 “Clash订阅链接”，一个 “Trojan订阅链接”，我们选择 “Clash订阅链接” 复制后面的链接。

然后我们打开 Clash for Windows 的这个页面：

![截图 2023-02-13 22-43-53](https://user-images.githubusercontent.com/91216205/218488821-2d222aaf-147a-44cf-968d-2f7be3c79536.png)

在上面的输入框输入我们刚保存的链接，然后 Download 即可。

接下来可以看到我们的结点变多了：

![截图 2023-02-13 22-45-38](https://user-images.githubusercontent.com/91216205/218489250-dce4fdba-d6f2-44f5-b068-13e4ca66c1d8.png)

直接选择合适的结点连接即可。

最后，需要修改系统的代理，首先打开网络设置，点击代理后面的按钮，像下面这样输入：

![截图 2023-02-13 22-48-27](https://user-images.githubusercontent.com/91216205/218490048-4858c138-1486-4365-b03c-0d67ac9aad60.png)

接下来就可以正常使用 google 了，需要注意的是，如果不需要代理了，那么在关闭了 cfw 之后，需要将网络代理调回 禁用，否则将无法正常联网。



# 五. 外观

## 1. 缩放与字体大小

和 Windows 的全局缩放相比，Ubuntu 的全局缩放基本上是用不了的，即使只是 125%，也看起来很模糊，所以对于小屏幕（如我的笔记本只有 14 英寸）来说，改变观感的方法只能从各个软件入手。以下设置完全以我的屏幕大小为参考：

**vscode**：在设置文件中添加如下代码即可：

```json
"window.zoomLevel": 1,
"settingsSync.ignoredSettings": [
    "-window.zoomLevel"
],
```

- 第一项设置窗口的缩放等级，原始大小为 0（可根据实际需要进行缩放）
- 第二项设置将上一项设置加入默认同步设置项中（第一项设置默认不同步，如果再次安装需要重新设置）
- 更换大屏显示器后将上述两项注释掉即可



**typora**：直接在 **偏好设置-外观** 设置中调整自己需要的字体大小即可，更换大屏显示器后将字体调回原本大小或者直接使用自动大小即可。

**火狐浏览器**：将默认缩放大小设置为 120% 或 133%（或者自己需要的大小）即可，更换大屏显示器后将设置调回 100%

**Chrome**：与 “火狐浏览器” 操作相同



## 2. 字体

一般的设置按照自己的喜好自行设置即可。这里主要介绍一个操作：添加字体。主要是因为我写代码比较喜欢 Consolas 字体，但是 Ubuntu 里没有这个字体。

首先，我们需要下载需要的字体文件，一般是个压缩文件，我们将其解压即可；

然后我们在下面的文件夹新建一个字体文件夹：`/usr/share/fonts`

然后将所有的`.ttf`文件移动到上一步自己新建的文件夹中即可。

然后修改字体权限：

```bash
sudo chmod 644 /usr/share/fonts/<你刚才新建的文件夹>/*.ttf
```

然后进入到字体目录：

```bash
cd /usr/share/fonts/<你刚才新建的文件夹>/
```

刷新并安装字体：

```bash
sudo mkfontscale && sudo mkfontdir && sudo fc-cache -fv
```



# 六. 其他配置

## 1. 禁用电脑自带键盘

由于为了节省空间，我常常需要把外接键盘放在笔记本上，但是很容易造成误触，所以需要将电脑自带键盘进行禁用，具体方法如下：

- 首先打开终端，输入以下命令：

  ```bash
  xinput list
  ```

  如果没有 xinput 的话，按照指示安装即可

- 在 Ubuntu 22 下，通常这个命令执行之后会有一条错误信息

  ```
  WARNING: running xinput against an Xwayland server. See the xinput man page for details.
  ```

  这是因为 Ubuntu 22 默认使用了 Wayland 模式，导致 /etc/X11/xorg.conf.d/40-libinput.conf 不起作用，在这种情况下我们是无法正常禁用设备的，而且它也并不会把电脑自带键盘列出来，我们需要修改一下 /etc/gdm3/custom.conf 这个文件：

  ```bash
  sudo vim /etc/gdm3/custom.conf
  ```

  可以先观察一下这个文件的内容，它的 WaylandEnable 这一项可能是 WaylandEnable=true 也可能直接注释掉了，如我的就是直接注释掉了，我们把该项前的注释符`#`删掉，然后确认一下 WaylandEnable 的值为 false 即可。

  最后重启一下电脑。

  **注意**：不会使用 vim 编辑器的可以尝试使用 gedit，将上述命令中的 vim 替换为 gedit 即可，没有该软件的根据终端的提示进行安装。

- 在排除了问题之后，我们再次打开终端输入 `xinput list` 命令，正常情况下，输出就应该像下面这样全部输入输出设备都列出来了：

  ```
  sn@Program:~/桌面$ xinput list
  ⎡ Virtual core pointer                    	id=2	[master pointer  (3)]
  ⎜   ↳ Virtual core XTEST pointer              	id=4	[slave  pointer  (2)]
  ⎜   ↳ MSFT0004:00 06CB:CD98 Mouse             	id=16	[slave  pointer  (2)]
  ⎜   ↳ MSFT0004:00 06CB:CD98 Touchpad          	id=17	[slave  pointer  (2)]
  ⎣ Virtual core keyboard                   	id=3	[master keyboard (2)]
      ↳ Virtual core XTEST keyboard             	id=5	[slave  keyboard (3)]
      ↳ Video Bus                               	id=6	[slave  keyboard (3)]
      ↳ Power Button                            	id=7	[slave  keyboard (3)]
      ↳ Integrated Camera: Integrated C         	id=12	[slave  keyboard (3)]
      ↳ Ideapad extra buttons                   	id=15	[slave  keyboard (3)]
      ↳ AT Translated Set 2 keyboard            	id=18	[slave  keyboard (3)]
  ```

- 我们首先找到电脑自带键盘，一般名称都是 “AT Translated Set 2 keyboard”，我们需要使用该项后面的 id 值，我这里是 18，然后输入下面的命令：

  ```bash
  xinput set-prop 18 "Device Enabled" 0
  ```

  即可禁用电脑自带键盘，命令中的 18 替换成你要禁用的设备在你自己的电脑上的 id 值，然后最后的值为 0 即为禁用，值为 1 即为启用，如，启用该设备的命令为：

  ```bash
  xinput set-prop 18 "Device Enabled" 1
  ```

另外，使用命令禁用键盘之后，只要重启电脑，又会自动启用。如果想永久禁用的话，就把命令写入到 bashrc：

```bash
sudo vim ~/.bashrc
```

添加高亮的两行即可：

![截图 2023-02-07 17-44-24](https://user-images.githubusercontent.com/91216205/217210311-c2390f90-635b-4f27-8993-a164180b449b.png)

具体内容是：

```bash
# 禁用电脑自带键盘
xinput set-prop "AT Translated Set 2 keyboard" "Device Enabled" 0
```

这里使用设备的名称而不是用 id 值是因为 id 值可能发生变化。

接下来重启一下试试吧。

