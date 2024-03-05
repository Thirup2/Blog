---
title: C/C++链接库生成与使用：Windows 篇
tags:
  - C
  - C++
  - 链接库
  - 动态链接库
  - 静态链接库
categories:
  - C/C++
  - 链接库
top_img: /config/img/cover/material-9.webp
cover: /config/img/cover/material-9.webp
description: 介绍在 Windows 环境下生成与使用链接库的方法与注意事项
abbrlink: 5aa06c69
date: 2024-03-04 22:04:25
updated: 2024-03-04 22:04:25
---

## 简介

### 本文的组成

- 测试文件及目录结构
- 静态链接库的生成与使用
- 动态链接库的生成与使用

### 系列文章

- [C/C++ 链接库（准备工作）](/posts/6e4f8e4a)
- [C/C++ 链接库简介](/posts/16812f7c)
- **本文**：[C/C++ 链接库生成与使用：Windows 篇](#)
- [C/C++ 链接库生成与使用：Linux 篇](/posts/f8c82266)



## 一、测试文件及目录结构

在 Windows 环境下的测试文件比较复杂，根据不同情况会有不同的测试文件，故本文将测试文件放在具体的情况中进行介绍。

关于目录结构，主要如下图所示：

![01](01.png)

在根目录下有两个目录和一个 CMake 配置文件，其中`include`目录下放置的是所有的头文件，包括接口头文件在内。`src`目录下放置的是程序源码，包括可执行程序的源码和库的实现代码。`src`下有几个子目录，每个子目录都是一个库的实现代码。

以上就是本测试主要的目录结构。本测试项目是为了创建一个实现了`addTwo`的加法函数和`timeTwo`的乘法函数的库，库名为`SimpleWork`，然后在一个可执行程序中使用这两个函数。



## 二、静态链接库的生成与使用

### 1. 测试文件

在 Windows 环境下，静态链接库的测试文件比较简单：

{% tabs static-lib-files %}

<!-- tab include/SimpleWork-Static.h -->

这是这个静态链接库的实现头文件同时也是接口头文件，内容如下：

```cpp
#ifndef LIBRARYTEST_SIMPLEWORK_STATIC_H
#define LIBRARYTEST_SIMPLEWORK_STATIC_H

// 两数加法
double addTwo(double one, double two);

// 两数乘法
double timeTwo(double one, double two);

#endif //LIBRARYTEST_SIMPLEWORK_STATIC_H
```

<!-- endtab -->

<!-- tab src/SimpleWork-Static/SimpleWork-Static.cpp -->

这个文件是`SimpleWork`静态链接库版本的实现代码，需要先包含`include/SimpleWork-Static.h`头文件。

```cpp
#include "SimpleWork-Static.h"

// 两数加法
double addTwo(double one, double two)
{
    return one + two;
}

// 两数乘法
double timeTwo(double one, double two)
{
    return one * two;
}
```

这里包含头文件时并没有按照相对位置进行指定，具体原因是因为根目录下 CMake 配置文件的一项配置，后续将具体介绍。

<!-- endtab -->

<!-- tab src/SimpleWork-Static/CMakeLists.txt -->

这也是一个 CMake 的配置文件，和根目录下的配置文件不同，这个文件属于根项目的子项目，所以写法简略了很多步骤：

```cmake
project(SimpleWork-Static)

add_library(${PROJECT_NAME} STATIC SimpleWork-Static.cpp)
```

其中：

- `project(SimpleWork-Static)`定义了这个子项目的名称
- `add_library(${PROJECT_NAME} STATIC SimpleWork-Static.cpp)`表示本子项目执行的工作即创建一个链接库。其中第一个参数决定了生成的库文件的名字，这是一个宏变量，表示`project`配置中填写的参数。第二个参数`STATIC`表示生成的链接库是一个静态链接库。第三个参数声明了参与生成库文件的实现代码文件，注意此处是一个相对地址而非单纯的文件名，由于本配置文件和实现代码在同一个目录，所以只有一个文件名。

<!-- endtab -->

<!-- tab src/main.cpp -->

这个文件是用来测试库文件的程序的源代码，需要先包含库的接口头文件：

```cpp
#include <iostream>
#include "SimpleWork-Static.h"

int main()
{
    std::cout << "请输入两个数字，我将算出它们的和与乘积：";
    double one, two;
    std::cin >> one >> two;
    std::cout << one << " + " << two << " = " << addTwo(one, two) << std::endl;
    std::cout << one << " * " << two << " = " << timeTwo(one, two) << std::endl;

    return 0;
}
```

和上面一样，这里包含头文件也未按照相对路径给出，原因和上述相同，后续将详细解释。

<!-- endtab -->

<!-- tab CMakeLists.txt -->

根目录下的 CMake 配置文件：

```cmake
cmake_minimum_required(VERSION 3.22)
project(LibraryTest)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)

include_directories(${PROJECT_SOURCE_DIR}/include)
link_directories(${PROJECT_SOURCE_DIR}/lib)

add_subdirectory(${PROJECT_SOURCE_DIR}/src/SimpleWork-Static)

add_executable(${PROJECT_NAME} ${PROJECT_SOURCE_DIR}/src/main.cpp)
target_link_libraries(${PROJECT_NAME} PUBLIC SimpleWork-Static)
```

上方文件其中一些配置在 [C/C++ 链接库简介](/posts/16812f7c) 一文中已经介绍过了，此处介绍一些其他的配置项：

- `set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)`：这一项以及后两项用来设置生成的程序放置的目录。其中第一项设置的是可执行程序放置的位置，第二项设置的是静态链接库文件放置的位置，第三项是动态链接库文件放置的位置。其中`${PROJECT_SOURCE_DIR}`也是一个宏变量，表示项目根目录的位置。
- `include_directories(${PROJECT_SOURCE_DIR}/include)`用于添加头文件的查找路径，这条表示将根目录下的`include`目录添加到头文件查找路径中，将头文件放到这个目录中，这样在包含头文件时就可以直接输入头文件名字而不用使用路径了。
- `link_directories(${PROJECT_SOURCE_DIR}/lib)`用于添加库文件的查找路径，语法和`include_directories`类似
- `add_subdirectory(${PROJECT_SOURCE_DIR}/src/SimpleWork-Static)`：在生成测试程序的可执行程序之前，我们需要先生成库文件，而我们的库刚刚写了一个子项目的 CMake 配置文件，此处我们只需要在主配置文件中添加子项目（`add_subdirectory`），参数中填写子项目配置文件的目录地址即可。
- `target_link_libraries(${PROJECT_NAME} PUBLIC SimpleWork-Static)`：生成可执行文件时，需要指定生成可执行文件需要链接的库，即该指令。参数1指定可执行程序的名字，参数2可以忽视，参数3表示需要链接到该可执行程序的库名字。

<!-- endtab -->

{% endtabs %}



### 2. 生成链接库

按照下图所示点击构建按钮，先选择配置文件，此处我们选择生成库文件的配置文件而不是生成可执行程序的配置文件。然后点击构建按钮即可：

![02](02.png)

可以看到，已经生成好了静态库文件：

![03](03.png)



### 3. 使用链接库

这次我们切换配置文件到可执行程序的配置文件进行构建：

![04](04.png)

不出意外，将生成以下文件：

![05](05.png)

然后我们点击执行按钮，如果你的源文件编码是`UTF-8(without BOM)`，那么可能执行出来会乱码：

![06](06.png)

其原因可以查看我的另一篇文章：[编程中乱码出现的原因](/posts/c77f42f7)

此处采用的解决办法是向编译器添加一些选项，由于我们并非直接使用编译器，而是调用IDE的编译功能，所以我们往编译器添加选项的方法是修改根目录配置文件：

```cmake
cmake_minimum_required(VERSION 3.22)
project(LibraryTest)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)

include_directories(${PROJECT_SOURCE_DIR}/include)
link_directories(${PROJECT_SOURCE_DIR}/lib)

add_compile_options(/source-charset:utf-8)		# 添加这一行

add_subdirectory(${PROJECT_SOURCE_DIR}/src/SimpleWork-Static)

add_executable(${PROJECT_NAME} ${PROJECT_SOURCE_DIR}/src/main.cpp)
target_link_libraries(${PROJECT_NAME} PUBLIC SimpleWork-Static)
```

然后重新进行构建，再点击运行，得到运行结果如下：

![07](07.png)

可以看到程序正常运行。



### 4. 模拟使用第三方静态链接库

为了模拟第三方静态链接库，首先我们需要删除该库的实现部分，包括实现代码`SimpleWork-Static.cpp`和与它同目录下的子项目配置文件`CMakeLists.txt`，然后删去根目录下配置文件`CMakeLists.txt`中的`add_subdirectory`一行即可。

需要注意的是，我们要保留之前生成的`.lib`库文件和接口头文件，库文件仍然放在`lib`目录下，接口头文件仍然放在`include`目录下。

此时我们的项目就像是拿到了一个第三方的静态链接库，我们只有它的接口头文件和静态库文件，测试代码通过接口头文件导入库的两个函数，但我们并不知道它的具体实现。

我们再次构建并运行一下这个项目，可以得到正常输出：

![08](08.png)



## 三、动态链接库的生成与使用

### 1. 测试文件

和静态链接库的测试文件不同，在 Windows 环境下的动态链接库通常需要两套头文件。其中一套用于编写库实现代码时导入，一套用于用户使用动态链接库时使用。
这次我们需要6个文件，其中大部分和之前还是相同的内容：

{% tabs dynamic-lib-files %}

<!-- tab include/SimpleWork-Export.h -->

所需头文件之一，在生成库文件时使用：

```cpp
#ifndef LIBRARYTEST_SIMPLEWORK_EXPORT_H
#define LIBRARYTEST_SIMPLEWORK_EXPORT_H

// 两数加法
_declspec(dllexport) double addTwo(double one, double two);

// 两数乘法
_declspec(dllexport) double timeTwo(double one, double two);

#endif //LIBRARYTEST_SIMPLEWORK_EXPORT_H
```

**需要注意的是**在函数前我们添加了一个`_declspec(dllexport)`，这是 Windows 要求的语法，或者更严谨的说是 MSVC 编译器要求的语法，以声明这些函数是需要导出到库中的函数。

正常使用 MSVC 编译动态链接库的情况下是会生成一个`.lib`后缀的导入库以及一个`.dll`后缀的动态链接库，在使用这个库的时候，我们需要链接的是这个`.lib`后缀的导入库而不是`.dll`后缀的动态链接库。而如果不添加这个声明，在生成库文件的时候将无法生成`.lib`后缀的导入库，也就无法使用这个库。

<!-- endtab -->

<!-- tab include/SimpleWork-Import.h -->

所需头文件之一，作为接口头文件给出，使用者需要包含：

```cpp
#ifndef LIBRARYTEST_SIMPLEWORK_IMPORT_H
#define LIBRARYTEST_SIMPLEWORK_IMPORT_H

// 两数加法
_declspec(dllimport) double addTwo(double one, double two);

// 两数乘法
_declspec(dllimport) double timeTwo(double one, double two);

#endif //LIBRARYTEST_SIMPLEWORK_IMPORT_H
```

和前者类似，函数的前面都添加了一个`_declspec(dllimport)`，用于向 MSVC 编译器说明这些函数用来导入到程序中。

<!-- endtab -->

<!-- tab src/SimpleWork-Dynamic/SimpleWork-Dynamic.cpp -->

库的实现代码，需要包含导出用头文件：

```cpp
#include "SimpleWork-Export.h"

// 两数加法
_declspec(dllexport) double addTwo(double one, double two)
{
    return one + two;
}

// 两数乘法
_declspec(dllexport) double timeTwo(double one, double two)
{
    return one * two;
}
```

<!-- endtab -->

<!-- tab src/SimpleWork-Dynamic/CMakeLists.txt -->

用于生成动态链接库的子项目配置文件，与实现代码放在同一层目录：

```cmake
project(SimpleWork-Dynamic)

add_library(${PROJECT_NAME} SHARED SimpleWork-Dynamic.cpp)
```

注意`add_library`第二个参数变成了`SHARED`

<!-- endtab -->

<!-- tab src/main.cpp -->

测试程序的代码，需要包含导入用头文件：

```cpp
#include <iostream>
#include "SimpleWork-Import.h"

int main()
{
    std::cout << "请输入两个数字，我将算出它们的和与乘积：";
    double one, two;
    std::cin >> one >> two;
    std::cout << one << " + " << two << " = " << addTwo(one, two) << std::endl;
    std::cout << one << " * " << two << " = " << timeTwo(one, two) << std::endl;

    return 0;
}
```

<!-- endtab -->

<!-- tab CMakeLists.txt -->

根项目配置文件：

```cmake
cmake_minimum_required(VERSION 3.22)
project(LibraryTest)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)

include_directories(${PROJECT_SOURCE_DIR}/include)
link_directories(${PROJECT_SOURCE_DIR}/lib)

add_compile_options(/source-charset:utf-8)

add_subdirectory(${PROJECT_SOURCE_DIR}/src/SimpleWork-Dynamic)		# 有更改

add_executable(${PROJECT_NAME} ${PROJECT_SOURCE_DIR}/src/main.cpp)
target_link_libraries(${PROJECT_NAME} PUBLIC SimpleWork-Dynamic)	# 有更改
```

大部分和静态链接库的配置文件相同，在`add_subdirectory`处需要将子目录修改一下，具体根据你的目录名而定。

在`target_link_libraries`处，由于我们的库名变成了`SimpleWork-Dynamic`，所以将需要链接的库名也修改一下。

<!-- endtab -->

{% endtabs %}



### 2. 生成链接库

还是像生成静态链接库一样，选择生成库的配置，然后点击构建即可：

![09](09.png)

这次会生成以下文件：

![10](10.png)

其中有一些本篇未介绍过的后缀文件，它们都与我们的测试没有太大关系。



### 3. 使用链接库

选择测试项目配置文件，然后点击构建，正常情况下会生成以下文件：

![12](12.png)

其中有我们需要的可执行程序，点击执行按钮，正常情况下得到以下输出：

![13](13.png)



### 4. 模拟使用第三方动态链接库

首先，我们需要保留的文件有接口头文件`SimpleWork-Import.h`，动态链接库文件`SimpleWork-Dynamic.dll`，导入库文件`SimpleWork-Dynamic.lib`。

其中`.lib`还是放在`lib`目录下，`.dll`放在`bin`目录下，`.h`放在`include`目录下。

此时我们可以删除`lib`、`bin`及`include`目录下其他文件，同时库的实现部分也可以删掉了。

最后效果如图所示：

![14](14.png)

最后还需要修改一下`CMakeLists.txt`中的配置，将`add_subdirectory`一行删去即可。

然后我们选择测试项目，点击构建，会看到测试程序仍然正常生成，点击运行，得到如下输出：

![15](15.png)

> 为了更好地理解 Windows 下生成和使用动态链接库的原理，你还可以自行进行下面两个测试：
>
> - 把导入库文件`.lib`也删除掉，然后点击构建，看看结果如何。不出意外应该是无法成功构建。
> - 保留`.lib`，把`.dll`删除掉，点击构建，再点击运行，看看结果如何。正常情况下应该能够成功构建，但无法运行。



## 四、动态链接库测试代码优化

在 Windows 环境下我们进行动态链接库的测试一共使用了 2 个头文件。实际上，我们可以做到只用一个头文件用于所有情况，那就是使用 C/C++ 的**宏定义**。

在动态链接库中我们需要根据导入和导出的情况不同使用两套头文件（`SimpleWork-Import.h`、`SimpleWork-Export.h`），我们可以通过宏定义以及预处理器的条件语句来将这两个文件合成一个文件`SimpleWork-Dynamic.h`：

```cpp
#ifndef LIBRARYTEST_SIMPLEWORK_DYNAMIC_H
#define LIBRARYTEST_SIMPLEWORK_DYNAMIC_H

// 添加这一段
#ifdef EXPORT
#define DLL_API _declspec(dllexport)
#else
#define DLL_API _declspec(dllimport)
#endif

// 两数加法
DLL_API double addTwo(double one, double two);		// 将具体的声明符修改为宏变量 DLL_API

// 两数乘法
DLL_API double timeTwo(double one, double two);		// 同上

#endif //LIBRARYTEST_SIMPLEWORK_DYNAMIC_H
```

在使用的过程中，我们需要向实现代码中添加`#define EXPORT`语句以使用`_declspec(dllexport)`的值，再包含这个头文件。注意`#define`要放在`#include "SimpleWork-Dynamic"`之前。

而在用户使用这个库时，只需要包含这个头文件即可，无需进行其他改动。

这样在动态链接库的测试中，就可以只使用一个头文件了。

在此基础上，我们更新一下其他的文件：

{% tabs files %}

<!-- tab include/SimpleWork-Dynamic.h -->

```cpp
#ifndef LIBRARYTEST_SIMPLEWORK_DYNAMIC_H
#define LIBRARYTEST_SIMPLEWORK_DYNAMIC_H

#ifdef EXPORT
#define DLL_API _declspec(dllexport)
#else
#define DLL_API _declspec(dllimport)
#endif

// 两数加法
DLL_API double addTwo(double one, double two);

// 两数乘法
DLL_API double timeTwo(double one, double two);

#endif //LIBRARYTEST_SIMPLEWORK_DYNAMIC_H
```

<!-- endtab -->

<!-- tab src/SimpleWork-Dynamic/SimpleWork-Dynamic.cpp -->

```cpp
#define EXPORT		// 添加这一行
#include "SimpleWork-Dynamic.h"

// 两数加法
DLL_API double addTwo(double one, double two)
{
    return one + two;
}

// 两数乘法
DLL_API double timeTwo(double one, double two)
{
    return one * two;
}
```

需要在包含头文件前`#define EXPORT`，从而使用`_declspec(dllexport)`这一值代替`DLL_API`。

头文件包含放在宏定义之后，是因为我们需要包含的头文件中的函数声明前也用`_declspec(dllexport)`代替`DLL_API`。

<!-- endtab -->

<!-- tab src/SimpleWork-Dynamic/CMakeLists.txt -->

```cmake
project(SimpleWork)

add_library(${PROJECT_NAME} SHARED SimpleWork-Dynamic.cpp)
```

这个文件不需要修改。

<!-- endtab -->

<!-- tab src/main.cpp -->

```cpp
#include <iostream>
#include "SimpleWork-Dynamic.h"

int main()
{
    std::cout << "请输入两个数字，我将算出它们的和与乘积：";
    double one, two;
    std::cin >> one >> two;
    std::cout << one << " + " << two << " = " << addTwo(one, two) << std::endl;
    std::cout << one << " * " << two << " = " << timeTwo(one, two) << std::endl;

    return 0;
}
```

这个文件也不需要修改，我们只包含这个头文件而不需要定义任何宏变量，这样我们所包含的这个头文件中函数声明前使用的`DLL_API`的值就将是`_declspec(dllimport)`。

<!-- endtab -->

<!-- tab CMakeLists.txt -->

```cmake
cmake_minimum_required(VERSION 3.22)
project(LibraryTest)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/bin)

include_directories(${PROJECT_SOURCE_DIR}/include)
link_directories(${PROJECT_SOURCE_DIR}/lib)

add_compile_options(/source-charset:utf-8)

add_subdirectory(${PROJECT_SOURCE_DIR}/src/SimpleWork-Dynamic)

add_executable(${PROJECT_NAME} ${PROJECT_SOURCE_DIR}/src/main.cpp)
target_link_libraries(${PROJECT_NAME} PUBLIC SimpleWork-Dynamic)
```

这个文件同样不需要修改。

<!-- endtab -->

{% endtabs %}

以上测试文件将在 [C/C++链接库生成与使用：Linux篇](/posts/f8c82266) 继续优化。

