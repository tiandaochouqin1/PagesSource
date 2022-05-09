---
title: VS Code
tags:
  - [C]
  - [编程]
  - [工具]
categories: [编程]
date: 2020-08-28 22:13:52
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->

- [在线编辑器](#在线编辑器)
- [常用快捷键](#常用快捷键)
  - [建议自定义快捷键](#建议自定义快捷键)
  - [文本编辑](#文本编辑)
  - [行操作](#行操作)
  - [多行多列](#多行多列)
  - [缩进](#缩进)
  - [注释](#注释)
  - [搜索](#搜索)
  - [设置](#设置)
- [C环境](#c环境)
  - [Mingw w64](#mingw-w64)
  - [launch和task配置](#launch和task配置)
  - [gdb使用](#gdb使用)
    - [调试](#调试)
  - [未定义的标识符](#未定义的标识符)
- [Python](#python)

# 在线编辑器
https://vscode.dev/

可安装插件。无terminal和运行环境。

# 常用快捷键
## 建议自定义快捷键

## 文本编辑
查询快捷键：<kbd>Ctrl</kbd>+<kbd>K</kbd>+<kbd>Ctrl</kbd>+<kbd>S</kbd>


删除前一个单词：<kbd>Ctrl</kbd> + <kbd>BackSpace</kbd>
选择：<kbd>Shift</kbd>+<kbd>up/down/left/right</kbd>

## 行操作
剪切行：<kbd>Ctrl</kbd>+<kbd>X</kbd>
删除行：<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>K</kbd>
移动行：<kbd>Alt</kbd>+<kbd>up/down</kbd>
复制行：<kbd>Alt</kbd> + <kbd>Shift</kbd>+<kbd>up/down</kbd>
跳转行：<kbd>Ctrl</kbd>+<kbd>G</kbd>

## 多行多列
`"editor.multiCursorModifier": "alt"`
多行编辑光标：<kbd>Alt</kbd>+左键点选
多行内容选择：<kbd>Alt</kbd>+左键拖动
矩形内容/列选择：<kbd>Alt</kbd>+<kbd>Shift</kbd>+左键拖动 （方法2：中键拖动选择）


## 缩进
调整缩进：<kbd>Ctrl</kbd>+<kbd>[ / ]</kbd>
展开/折叠区域代码：<kbd>Ctrl</kbd>+<kbd>Shift</kbd>+<kbd>[ / ]</kbd>
展开/折叠区域所有代码：<kbd>Ctrl</kbd>+<kbd>K</kbd>+<kbd>[ / ]</kbd>
层级折叠文件所有代码：<kbd>Ctrl</kbd>+<kbd>K</kbd>+<kbd>Ctrl</kbd>+<kbd>0-9</kbd>
完全展开文件所有代码：<kbd>Ctrl</kbd>+<kbd>K</kbd>+<kbd>Ctrl</kbd>+<kbd>J</kbd>

## 注释
单行注释：<kbd>Ctrl</kbd>+<kbd>K</kbd>+<kbd>Ctrl</kbd>+<kbd>C</kbd>
取消单行注释：<kbd>Ctrl</kbd>+<kbd>K</kbd>+<kbd>Ctrl</kbd>+<kbd>U</kbd>
插入多行注释：<kbd>Alt</kbd> + <kbd>Shift</kbd>+<kbd>A</kbd>

## 搜索
以内容搜索文件：<kbd>Ctrl</kbd>+<kbd>T</kbd>
打开最近文件：<kbd>Ctrl</kbd> + <kbd>Shift</kbd>+<kbd>T</kbd>
搜索文件名：<kbd>Ctrl</kbd>+<kbd>P</kbd> (`@`为搜索本文件内符号；`>`为命令面板)

## 设置
终端栏：<kbd>Ctrl</kbd>+<kbd>J/`</kbd>
侧边栏：<kbd>Ctrl</kbd>+<kbd>B</kbd>
命令面板：<kbd>Ctrl</kbd> + <kbd>Shift</kbd>+<kbd>P</kbd>（输入扩展名可查询其操作）
切换窗口：<kbd>Ctrl</kbd>+<kbd>Tab</kbd>


> Bookmarks插件
> GitLens
> Hexdump


# C环境
看[官方的说明](https://code.visualstudio.com/docs/cpp/config-mingw)来配置环境即可。网上其它教程均不靠谱（版本更新快）。

VS Code插件只需要安装`C/C++`。
`launch.json`：主要用于调试，F5启动。`prelauchTask`可配置对应task，实现 `编译（task）+运行（launch）`。
`task.json`:用于配置其它名任务，如编译。
`c_cpp_properties.json`：compiler path and IntelliSense settings。

`Code Runner`插件可以辅助配置环境。

## Mingw w64
下载，解压到文件夹，并将`Mingw64/bin`加入系统环境变量。
GCC编译器：[Mingw -W64](https://sourceforge.net/projects/mingw-w64/files/mingw-w64/mingw-w64-release/)项目，有源码和win/linux程序包。两个版本。`seh`：性能更高，兼容稍差。`sjlj`：兼容强。


## launch和task配置
launch.json
```
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "g++.exe - Build and debug active file",
      "type": "cppdbg",
      "request": "launch",
      "program": "${fileDirname}\\${fileBasenameNoExtension}.exe",
      "args": [],
      "stopAtEntry": false,
      "cwd": "${workspaceFolder}",
      "environment": [],
      "externalConsole": false,
      "MIMode": "gdb",
      "miDebuggerPath": "C:\\Program Files\\mingw-w64\\x86_64-8.1.0-posix-seh-rt_v6-rev0\\mingw64\\bin\\gdb.exe",
      "setupCommands": [
        {
          "description": "Enable pretty-printing for gdb",
          "text": "-enable-pretty-printing",
          "ignoreFailures": true
        }
      ],
      "preLaunchTask": "C/C++: g++.exe build active file"
    }
  ]
}

```
task.json
```
{
  "version": "2.0.0",
  "tasks": [
    {
      "type": "shell",
      "label": "C/C++: g++.exe build active file",
      "command": "C:\\Program Files\\mingw-w64\\x86_64-8.1.0-posix-seh-rt_v6-rev0\\mingw64\\bin\\g++.exe",
      "args": ["-g", "${file}", "-o", "${fileDirname}\\${fileBasenameNoExtension}.exe"],
      "options": {
        "cwd": "${workspaceFolder}"
      },
      "problemMatcher": ["$gcc"],
      "group": {
        "kind": "build",
        "isDefault": true
      }
    },

    {
      "type": "shell",
      "label": "gcc Assembly",
      "command": "D:\\Program Files\\mingw64\\bin\\gcc.exe",
      "args": ["-S", "${file}", "-o", "${fileDirname}\\${fileBasenameNoExtension}.asm"],
      "options": {
        "cwd": "${workspaceFolder}"
      },
      "problemMatcher": ["$gcc"]
    }
  ]
}
```

## gdb使用
https://linuxtools-rst.readthedocs.io/zh_CN/latest/advance/02_program_debug.html
[gdb调试利器](https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/gdb.html#gdb)

### 调试
Step over：
Step in：
Step out：

监控：
断点：


## 未定义的标识符
typedef <error-type>
未找到解决方法，github vscode项目有对应的未解决issue，且未被scheduled。

可关闭所有错误提示：`vscode 设置`->`setting.json`->`"C_Cpp.errorSquiggles": "Disabled",`

# 代码阅读插件
cscope > gnu global

gnu global无法识别用宏类型定义的全局变量。

vscode的搜索功能严重依赖机器的磁盘性能。

## C/C++

1. 每次改动文件会重建数据库，耗时可能几小时（cscope和gtags只需要一两分钟）;
2. 工程文件数量达到几千时特别慢，且跳转也慢。

一下插件与C/C++冲突，务必禁用/卸载C/C++。
## scope4code
依赖cscope在.vscode/cscope生成的4个文件（cscope.files、cscope.in.ou、cscope.out、cscope.po.out）。

使用方法参考插件页面指导。使用的人少。

ctrl+shift+p ->build database

可在workspace配置正则exclude paths(配置了没起作用？)。

## C/C++ GNU Global 和C++ Intellisense
两个插件均fork自C/C++ Intellisense，原理和使用方式一致。

gtags生成+global查找。可跳过symbol link。

使用方法参考插件页面指导。

gtags使用 gtags.files指定包含的文件。

# Python
已安装python。安装`Python`插件，`F5`运行，即可生成默认的`launch.json`配置。
