---
layout: post
title: "使用 PyArmor 保护你的 Python 项目"
subtitle: "How to use PyArmor-8.5.8"
date: 2024-05-31
author: "Sandm"
header-img: "img/post-bg-2015.jpg"
tags: [Python, 加密, PyArmor]
---

> [!CAUTION]
>
> **注意**：本文使用的 PyArmor 版本为 **8.5.8** ，不同版本的命令可能有所不同。

------

## 写在开始前

### 0.1 为什么出此教程

我尝试搜索了一下，目前关于 PyArmor 的教程大部分都过时了，给出的命令根本无法使用，少数新版的教程也需要开 VIP 。什么？你问我用的什么平台:confused: ？ ~~CSDN~~ ，必须点名批评！:angry:

故特地出此教程。内容较为浅显，如有错误，还请多多包涵。

------

## 1. 正文

### 1.1 什么是PyArmor

[Pyarmor](https://pyarmor.dashingsoft.com/) 是一个用于加密和保护 <u>Python</u> 脚本的工具。它能够在运行时刻保护脚本代码不被泄露，设置加密后脚本的使用期限，绑定加密脚本到硬盘、网卡等硬件设备。

功能特点:

- **无缝替换**: 加密后的脚本依然是一个有效的 .py 文件，在大多数情况下可以直接替换原来的 .py 脚本，而不影响脚本的使用。
- **均衡加密**: 提供了丰富的加密选项来平衡安全性和性能，能够满足大多数应用对安全性和性能的要求。
- **不可逆加密**: 能够直接重命名源代码中的函数，类，方法，变量和参数。
- **转换成为 C 代码**: 能够把模块中部分函数转换成为 C 代码，然后使用高优化选项直接编译 C 代码为机器指令来保护 Python 函数
- **限制加密脚本的使用范围**: 可以绑定加密脚本到指定的设备或者设置加密脚本的有效期
- **Themida 保护**: 使用 Themida 保护加密脚本（仅 Windows 平台可用）



### 1.2 安装

[Pyarmor](https://pypi.python.org/pypi/pyarmor/) 是一个发布在 [PyPI](https://pypi.python.org/pypi/) 的 Python 包，最方便的方式就是直接使用命令 **pip** 进行安装。

在 **Windows** 下，使用 Win+R 弹出命令输入框，输入 **cmd** 回车打开控制台，在控制台输入安装命令:

```cmd
pip install pyarmor
```

在 **Linux** 或者 **Apple** 平台，直接打开终端，然后执行下面的命令:

```cmd
pip install pyarmor
```

安装完成之后，输入以下命令并回车执行。如果安装成功，会显示出 PyArmor 的版本信息：

```cmd
pyarmor --version
```

或者：

```cmd
pyarmor --v
```

若此时显示以下信息，则说明安装成功。

```cmd
Pyarmor 8.5.8 (trial), 000000, non-profits

License Type    : pyarmor-trial
License No.     : pyarmor-vax-000000
License To      :
License Product : non-profits

BCC Mode        : No
RFT Mode        : No

Notes
* Can't obfuscate big script and mix str
```

> [!IMPORTANT]
>
> 不是所有的平台都被 PyArmor 支持，所有支持的平台和架构请查看 [生成加密脚本的环境](https://pyarmor.readthedocs.io/zh/latest/reference/environments.html)



### 1.3 使用Web-UI可视化操作

如果你只是想使用 PyArmor 加密你的项目，而不是嵌入到你的程序中使用，这里非常推荐使用Web-UI进行配置。（UI界面如下）

![pyarmor-webui.png](https://img2.imgtp.com/2024/05/31/N9g8WR8H.png)

使用Web-UI之前，执行以下命令安装依赖：

```cmd
pip install pyarmor-webui
```

然后执行命令运行本地服务器：

```cmd
pyarmor-webui
```

此时应该会自动打开系统默认的浏览器，接下来的操作均在此进行。



### 1.4 加密脚本

> [!CAUTION]
>
> **注意**：请在加密前备份好项目代码，以避免造成不必要的损失。



在首页点击<img src="https://img2.imgtp.com/2024/05/31/y3TsIVDV.png" alt="加密脚本向导" style="zoom:40%;" />，进入配置页（如果你的页面是英文的，请在首页右上角将语言更改成简体中文）

![配置页面.png](https://img2.imgtp.com/2024/05/31/kuRHwkPu.png)

|   参数   |                             说明                             | 是否必填 |                     例子                      |
| :------: | :----------------------------------------------------------: | :------: | :-------------------------------------------: |
|  源路径  |  一般选择被加密项目的根目录，参数二、四即为此目录的相对路径  |    是    | C:/Users/nick/Desktop/Python Project/BlogToMD |
|  主脚本  |                       程序入口脚本文件                       |    否    |                    main.py                    |
| 搜索模式 | 在“递归所有 / 仅仅列举的这些(参数二) / 仅仅加密源路径下的(参数一，相当于只遍历根目录，不遍历子文件夹)”中选择 |    是    |          仅仅加密列出的这些脚本文件           |
| 排除路径 |  不希望加密的脚本路径(可以是脚本文件路径或者所在文件夹路径)  |    否    |                    __test                     |

点击下一步，进入高级配置页![高级配置.png](https://img2.imgtp.com/2024/05/31/Hx30Ssrz.png)

|             参数             |                          说明                          |            默认值            |
| :--------------------------: | :----------------------------------------------------: | :--------------------------: |
|           约束模式           |    默认 / 禁用 / 私有模式(不允许被调用) / 约束模式     | 加密脚本使用默认的约束和限制 |
|         启用函数保护         | 确保加密脚本的属性不能直接被 Python 解释器直接导入查看 |              关              |
|         启用模块保护         |       检查导入的模块，确保是没有被替换的加密模块       |              关              |
| 加密字符串(在试用版中不可用) |         加密脚本中除函数名和变量名以外的字符串         |              关              |

点击下一步，进入保存配置页

![保存配置页.png](https://img2.imgtp.com/2024/05/31/lGM7LuR3.png)

|   参数   |                          说明                           |              是否必填              |      例子      |
| :------: | :-----------------------------------------------------: | :--------------------------------: | :------------: |
| 输出路径 |     加密包(被加密脚本和运行时所需的模块)的保存路径      | 否(默认保存在源路径下的dist文件夹) |       -        |
|  包名称  | 加密包中被加密文件名称(开启后类似输出dist/ModelName.py) |                 否                 |       关       |
| 运行平台 |                 运行加密脚本的目标平台                  |                 否                 |       -        |
|  许可证  |            对运行的限制文件(稍后会进行介绍)             |                 是                 | 使用默认许可证 |

点击“加密”按钮执行加密，如果加密成功应该会提示已输出到目录（路径）。



### 1.5 创建许可证

许可证文件还可以对加密脚本的运行进行限制，例如限制在特定的机器、网络、用户或有效期内运行。

来到首页，在左边侧边栏中选择“我的许可证”，进入创建页面，这里会显示已创建的许可证文件列表。

点击“创建”按钮：

![创建许可证页面.png](https://img2.imgtp.com/2024/05/31/t1xjSYI1.png)

已有的提示很简明易懂，这里就不过多赘述了。



## 2.结语

至此，我们已经了解了 PyArmor 最基本的用法，如果你想学习一些更高级的，请阅读官方文档（有中文，放心食用:smiley:）

[[中文]Pyarmor 8.5 用户文档 — Pyarmor 8.5.9 文档](https://pyarmor.readthedocs.io/zh/latest/)

[[英文]Pyarmor 8.5 Documentation — Pyarmor 8.5.9 documentation](https://pyarmor.readthedocs.io/en/latest/)

欢迎各位大佬留言 :)
