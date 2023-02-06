---
title: 在VSCode中添加组合快捷键
date: 2022-12-03
---
# 一. 前言

有些快捷键用过了就会发现特别好用，比如一个快捷键就能跳转到行尾添加分号或者是到行末添加大括号等等。所以我搜索了一下在 vscode 里实现的方法，主要如下。



# 二. 操作

## 1. 安装'macros'扩展

![屏幕截图 2022-02-27 204044](https://user-images.githubusercontent.com/91216205/155882737-7b01dfb6-e7b8-4052-8bf0-1e5b4efd6ea3.png)
这个扩展是用来提供快捷命令的，如将光标移动到行末。



## 2. 在settings.json里添加以下代码

```json
"macros": {
    "end_semicolon": [ // 末尾加分号
        "cursorLineEnd",
        {
            "command": "type",
            "args": {
                "text": ";\n"
            },
        },
    ],
    "end_colon": [ // 末尾加冒号
        "cursorLineEnd",
        {
            "command": "type",
            "args": {
                "text": ":\n"
            }
        },
    ],
}
```
这一步是在设置文件里添加你想要的快捷键命令，如上图中添加了两个命令，一个是移动到行末添加分号，一个是移动到行末添加冒号，两个命令的名称分别是：`end_semicolon`和`end_colon`



## 3. 在keybindings.json里添加以下代码

```json
{
    "key": "alt+;",
    "command": "macros.end_semicolon"
},
{
    "key": "alt+shift+;",
    "command": "macros.end_colon"
}
```
这一步将刚才添加好的命令绑定到快捷键上。



# 三. 实现更多功能

阅读扩展支持的命令，你还可以发现更多的命令。将这些基础命令组合，可以实现更多的功能。
比如我现在常用的快捷键和功能如下：

- keybindings.json
```json
{
    "key": "shift+enter",
    "command": "macros.end_semicolon"
},
{
    "key": "alt+shift+enter",
    "command": "macros.end_colon"
},
```
- settings.json
```json
"macros": {
    // 转到行末添加分号
    "end_semicolon": [
        "cursorLineEnd",
        {
            "command": "type",
            "args": {
                "text": ";"
            },
        },
    ],
    // 转到行末添加大括号并换行
    "end_colon": [
        "cursorLineEnd",
        { "command": "type",
         "args": {
             "text": "{\n"
         }
        },
    ],
},
```

第一个快捷键可以在行末添加分号，不过我将按键组合更改了一下，让它变得更方便了，并且能够在按下这个快捷键之后不用再转移到回车换行。
因为没有在行末添加冒号的需求，所以我将这个键改成了在行末添加大括号并换行，VSCode会将大括号自动补齐。