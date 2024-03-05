---
title: Windows环境编程乱码问题解决方案总结
tags:
  - Windows
  - 编码问题
  - C/C++
categories:
  - Windows
  - 问题探索
top_img: /config/img/cover/material-6.webp
cover: /config/img/cover/material-6.webp
description: 对 Windows 环境下编程所出现的乱码问题的解决方案的总结，包括前篇所提到的调控编译器选项的方法和其他一些方法
abbrlink: a15e7e9
date: 2023-10-25 00:40:16
updated: 2023-10-25 00:40:16
---

## 前言

本文主要是在 Windows 环境下编程所出现的乱码问题的解决方案的总结，其中也包括了前篇所说的调控编译器选项的乱码解决方案，同时还补充了其他的一些可行的方法。

另外，虽然本文又一次提到了调控编译器选项的相关内容，但本文并不是前篇内容的复制粘贴，也不会进行各种各样的测试，本文只想聊聊在实际开发过程中应该如何使用这样的方法，以及如何将其推广到不止一种字符集环境中。

### 系列文章

- [编程中乱码出现的原因](/posts/c77f42f7)
- [通过调控编译器选项的乱码解决方案测试](/posts/5e27120d)
- **本文**：[Windows 环境编程乱码问题解决方案总结](#)



## 一、调控编译器选项法

### 1. 重复命令

在我上一篇的测试中，每一组测试我都是重新写命令然后执行。

毫无疑问，这样不仅容易出错，而且效率还低，如果是在一个大项目里，这种方法是绝不可行的。

我们理所当然应该想到使用 Makefile 对程序进行编译。



### 2. Makefile

使用 Makefile 能有效地提高每一次编译的效率，也能降低命令出错的概率。

但还有一个弊端，我们仍然没有跳脱出来，那就是我们编写的命令毫无疑问只能在一种特定字符集的机器上运行，打个比方说如果在日本的 Windows 上编译此程序，那么 MSVC 所编译出的程序应该还是可行，但如果是使用 GCC 编译得到的程序就应该会出现错误了。因为 MSVC 能够自动识别用户字符集并且其`/execution-charset`选项默认就是用户字符集，而源文件只要保持`utf-8`不变，再将 MSVC 的`/source-charset`选项设定为`utf-8`，那么就基本上能够在任何环境的 Windows 下编译出正确运行的程序。而 GCC 则因为`-fexec-charset`默认为`utf-8`所以无法做到适应每一种用户字符集。

另外，对于纯手写 Makefile 的方法，在项目中也通常不是最常用的方法，人们会借助 Configure 工具来自动生成 Makefile，以达到检查编译工具是否齐全以及根据环境生成最适合该环境的 Makefile 的功能。



### 3. 自动生成

如果你喜欢使用 CLion IDE 作为日常的开发工具，那你一定对 CMake 不陌生，实际上 CMake 和 Configure 工具是同一层级的编译工具，通过更高层次的抽象，只需要在配置文件中描述一下项目的相关内容，就能自动根据这个配置文件生成具体的 Makefile 文件。

由于上面所说的 MSVC 在编译此类程序时几乎没有问题，所以后续我们都以 GCC 为例。

通过使用这种构建工具，我们或许能通过它来识别具体的用户字符集，然后来生成最适合本地环境的 Makefile 文件，最终生成可以正确运行的程序。

例如，如果我们使用 CMake 进行构建，可以在 CMakeLists.txt 文件中添加这样一段进行简单的配置：

```cmake
if(WIN32)
    if(MSVC)
        add_compile_options(/source-charset:utf-8)
    elseif(CMAKE_COMPILER_IS_GNUCXX)
        execute_process(COMMAND PowerShell "[System.Text.Encoding]::Default.BodyName" OUTPUT_VARIABLE USER_CHARSET OUTPUT_STRIP_TRAILING_WHITESPACE)
        add_compile_options(-fexec-charset=${USER_CHARSET})
    endif ()
endif ()
```

> 在这个例子中，只对 Windows 下的环境进行了判断，主要是由于 Linux 或其他环境下默认用户字符集都是`utf-8`，并且使用的基本上也是 GCC 或者和 GCC 类似的编译器，所以一般来说只需要特殊化 Windows 环境下的编译命令。我们用一个条件`WIN32`来区分 Windows 和其他环境
>
> 紧接着，根据一个条件分支，当编译器使用 MSVC 时，直接将编译器的输入文件编码的配置选项设置为`utf-8`即可实现所有 Windows 环境的编译工作（因为我们的文件字符集被规定为`utf-8`）。
>
> 然后在另一个分支中，当编译器使用 GCC 系列时，我们先是调用 PowerShell 并运行引号中的命令获取当前用户字符集，并将该结果的文本内容写到变量`USER_CHARSET`中，然后再添加编译器选项`-fexec-charset=${USER_CHARSET}`，这个地方的`${USER_CHARSET}`会自动替换为刚刚运行命令获取的用户字符集的内容

当然，你可以再精细化一下上面的配置，使它可以灵活处理任何编译环境，具体方法请参考 [CMake官方文档](https://cmake.org/cmake/help/latest/)。

使用本方法可以做到不费太大工夫就解决程序乱码问题，比起修改用户环境这些方法好很多。但同样有缺陷，就是如果我们做的是一个全球化的软件，那可能需要为每一个地区都发布一个安装包，具体的解决方法可以参考下面一节内容。

总的来说，如果你更倾向于让用户直接下载源文件并自行编译，那么这种方法毫无疑问是非常合适的，如果配置合理是肯定不会出现乱码问题的，唯一的问题可能是用户的字符集里没有收纳你的程序需要打印出来的字符，所以通常需要为每一个地区提供一个语言包。

如果你更倾向于直接发布安装包让用户下载，那最好的办法还是使用下一节所讲的内容，否则你需要为每一种用户环境都编译一次。



## 二、程序内转换编码法

### 1. 上述方案弊端分析

**弊端一**：不难发现，每到一个新机器上，程序都需要重新进行编译，当然不排除有一部分人就喜欢自行编译，但应该认识到大部分人需要的都是下载完成后能够直接安装使用的方案，而非先安装编译环境然后再编译源文件，最后才是安装使用的方案。

**弊端二**：在前面的程序中，不论是字符串字面值还是输入的字符串，我们都没有经过任何处理就直接输出了。但是更符合一般情形的却是无论是字符串字面值还是输入到程序中的字符串我们都会进行处理，而这就又将导致一个问题。如果只处理英文，那么通常单字节的`char`类型就足够了，但如果还涉及到正确处理中文字符的话，或许需要用到`utf-16`或`utf-32`，而源文件本身应该还是保持`utf-8`，同时还不改终端`gbk`或其他字符集，这个问题就再一次变得复杂了。



### 2. 弊端二的解决

我们先来解决弊端二的问题。

将源文件的字符集设置为`utf-8`，终端保持`gbk`，然后在程序内处理`utf-16`字符集的字符串。为此我们应该需要三个转换函数：

1. 用于将字符串字面值编码转换为可处理编码，即`u8_u16`
2. 用于将输入字符串编码转换为可处理编码的`gbk_u16`
3. 用于将标准处理字符串编码输出的`u16_gbk`。

另外，我们还需要明确一个问题，即此时编译器是否还影响乱码问题的出现。推测应该还是有影响，保险起见进行两组测试，一是将编译器的**设定1**（`/source-charset`或`-finput-charset`）和**设定2**（`/execution-charset`或`-fexec-charset`）统一的测试组，即中间不会进行任何编码的转换；二是将**设定1**和**设定2**设置为不同字符集，源文件中的字符串字面值编码应该会在编译器中发生一次转换。

**测试代码**如下：

{% tabs code %}

<!-- tab convert.h -->

```c++
#include <iostream>
#include <vector>
#include <string>
#include <Windows.h>

std::u16string u8_u16(const std::string& str_u8);
std::u16string gbk_u16(const std::string& str_gbk);
std::string u16_gbk(const std::u16string& str_u16);
```

<!-- endtab -->

<!-- tab test.cpp -->

```c++
#include "convert.h"
using std::string;
using std::u16string;

int main()
{
    // 定义字符串并获取输入
    string out_u8("你刚刚输入的是：");
    string in_gbk;
    std::cin >> in_gbk;

    // 将以上字符串转换为用于处理的编码
    u16string out_16 = u8_u16(out_u8);
    out_16 += gbk_u16(in_gbk);

    // 将输出字符串转换为 gbk
    string out_gbk = u16_gbk(out_16);
    // 对 utf-16 的字符串进行处理（返回其字符串长度）
    std::cout << out_16.length() << std::endl;
    // 输出 gbk 字符串
    std::cout << out_gbk << std::endl;

    return 0;
}
```

<!-- endtab -->

<!-- tab convert.cpp -->

```c++
#include "convert.h"

std::u16string u8_u16(const std::string &str_u8)
{
    std::u16string u16str;
    int i = 0;

    while (i < str_u8.length()) {
        // Read the first byte
        char u8char = str_u8[i++];

        if ((u8char & 0x80) == 0) {
            // Single-byte UTF-8 character
            u16str.push_back(u8char);
        } else if ((u8char & 0xE0) == 0xC0) {
            // Two-byte UTF-8 character
            char char2 = str_u8[i++];
            char16_t u16char = ((u8char & 0x1F) << 6) | (char2 & 0x3F);
            u16str.push_back(u16char);
        } else if ((u8char & 0xF0) == 0xE0) {
            // Three-byte UTF-8 character
            char char2 = str_u8[i++];
            char char3 = str_u8[i++];
            char16_t u16char = ((u8char & 0x0F) << 12) | ((char2 & 0x3F) << 6) | (char3 & 0x3F);
            u16str.push_back(u16char);
        } else {
            // Invalid UTF-8 sequence
            // Handle the error as needed
        }
    }

    return u16str;
}
std::u16string gbk_u16(const std::string &str_gbk)
{
    int utf16_length = MultiByteToWideChar(CP_ACP, 0, str_gbk.c_str(), -1, nullptr, 0);

    if (utf16_length == 0) {
        std::cerr << "MultiByteToWideChar failed" << std::endl;
        return std::u16string();
    }

    std::vector<wchar_t> utf16_buffer(utf16_length);

    if (MultiByteToWideChar(CP_ACP, 0, str_gbk.c_str(), -1, utf16_buffer.data(), utf16_length) == 0) {
        std::cerr << "MultiByteToWideChar failed" << std::endl;
        return std::u16string();
    }

    std::u16string utf16_string;

    for (int i = 0; i < utf16_length - 1; i++) {
        utf16_string.push_back(utf16_buffer[i]);
    }

    return utf16_string;
}
std::string u16_gbk(const std::u16string &str_u16)
{
    int gbk_length = WideCharToMultiByte(CP_ACP, 0, reinterpret_cast<LPCWCH>(str_u16.c_str()), str_u16.length(), nullptr, 0, nullptr, nullptr);

    if (gbk_length == 0) {
        std::cerr << "WideCharToMultiByte failed" << std::endl;
        return std::string();
    }

    std::vector<char> gbk_buffer(gbk_length);

    if (WideCharToMultiByte(CP_ACP, 0, reinterpret_cast<LPCWCH>(str_u16.c_str()), str_u16.length(), gbk_buffer.data(), gbk_length, nullptr, nullptr) == 0) {
        std::cerr << "WideCharToMultiByte failed" << std::endl;
        return std::string();
    }

    return std::string(gbk_buffer.begin(), gbk_buffer.end());
}
```

<!-- endtab -->

{% endtabs %}

**正确结果示例**：

```
$ ./test.exe 
你好啊
11
你刚刚输入的是：你好啊
```

可以看到在输出字符串长度时由于我们转换了编码，所以能够正确处理中文字符了。

**测试结果**：

1. 编译器**设定1**和**设定2**统一时：

   1. 将源文件字符集固定为`utf-8`，使用 MSVC 和 GCC 在全缺省的情况下都能正常编译，且结果正确；

   2. 如果将源文件字符集固定为`gbk`，则在输出时原本的字符串字面值以及输出字符串长度会输出错误，输入反馈仍然正常；

      > 原因应该是字符串字面值在文件中保存为了`gbk`编码，然后又执行了`u8_u16`的转换，故出错

   3. 当源文件字符集固定为`gbk`，并且在字符串字面值前添加`u8`前缀后，输出正确。

      > 说明上一点的原因推测正确

2. 编译器**设定1**和**设定2**不统一时：

   1. 当源文件设定为`gbk`，然后 MSVC 的设定1保持默认`gbk`，设定2设置为`utf-8`，输出结果正确。GCC 获得相同结果

   2. 当源文件设定为`utf-8`，然后 MSVC 设定1 设置为`utf-8`，设定2保持默认`gbk`，输出结果错误。GCC 获得相同结果

      > 根据以上两次测试，可以合理猜测程序处理过程中使用的数据是经过编译器转换后的编码

**结论**：当我们的源文件固定为`utf-8`时，且终端字符集固定为`gbk`时，使用该方法则无需调控编译器的相关选项，只需要保证编译器的设定1和设定2一致即可。与此同时，我们还可以在程序中对中文字符串进行正确的处理，故此方案可行。



### 3. 弊端一的解决

解决了弊端二，同时也带来了一个新的问题：我们无法再通过 Configure 或 CMake 来自动根据环境修改编译命令，而将编码处理放在了程序中，而上面的程序只解决了在`gbk`终端环境下的问题，如果要让程序能够自动处理多种环境的转换，则可能需要实现大量字符串编码转换函数，以及使用宏来调控。

如果解决了这个问题，那么弊端一也就解决了，而这个问题说到底就是编程问题，需要自己去添加转换函数并添加识别当前环境的代码并据此调控转换时使用的具体的函数。

而这个功能最好分离出来，形成一个可复用的功能。我也计划写一个第三方库来实现这样的功能，当然，编码转换实际上已经第三方库实现过了，所以实际上我想写的一个第三方库目标是一个使用更方便，更智能，转换追求无感化的一个第三方库。如果该项目具有可行性，则会在项目建立之后将链接贴在此处。



## 三、修改用户环境法（极其不推荐）

在标题中我写上了 “极其不推荐”，但还是放在了这篇文章当中，不是为了让大家觉得这种方法还是有一定的可行性，而是因为我第一次探索乱码问题时就走入了这样的一个误区，然后还花时间做了测试和总结，然而后来才发现这个方向几乎完全错误，最后我把那篇文章删掉了，但还是有点气不过写那篇文章所花费的大量的时间，所以在这添加了这样一节做一个纪念，呜呜呜。

修改用户环境有两种手段，一是让用户自行去修改，这种方案即使在那篇错误的文章中也是一个废案，因为一个程序应该做的绝不应该是要求用户先去修改日常使用的环境；第二种手段就是在程序中插入代码来修改用户环境，包括使用`system`函数调用系统命令的方法以及使用 Win32 API 和 C/C++ 标准库进行本地化的方法。

经过以前的测试，即使使用第二种方法修改用户环境也是完全不好的方案，如果你有兴趣去进行实验，那你会发现即使对于字符串字面值的输出能够正确显示，其对于输入的反馈也大概率会出错。

故不推荐。



## 四、总结

本文虽然一共写了三种方案，但实际可行的只有前两种，在这两种方案之中，第二种方案即在程序内转换编码的方案应该是最好的，因为我们几乎不可能不对字符串内容进行处理，而为了处理中文字符串，就几乎必然得用到 Unicode 字符集，也就是说几乎必然需要编码转换。在这样的情况下，显然使用第二种方案是最好的方案。
