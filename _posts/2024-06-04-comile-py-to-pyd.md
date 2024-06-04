---
layout: post
title: "把 py 脚本编译为 pyd 二进制文件"
subtitle: "Comile to protect your code"
date: 2024-06-04
author: "Sandm"
header-img: "img/post-bg-2024.jpg"
tags: [Python, Cython, PyArmor, 加密]
---

## 1.1. pyd文件是什么

**pyd** 文件是 **Python** 的**动态链接库文件**，它是用 C 语言或者 Cython 等编写的 Python 扩展模块编译生成的**二进制文件**。在 **Windows** 上，它的扩展名通常是 .pyd，在类 Unix 系统上可能是 .so 文件。

[^说明]: 此教程仅针对  Windows 平台，因此对于这里的二进制文件仅介绍 .pyd

与Python的其他文件类型相比，**pyd** 文件有以下**特点**：

1. **动态链接库**：pyd 文件是 Python 的动态链接库，它包含了用 C 或 Cython 等语言编写的 Python 扩展模块的二进制代码。这些模块通常是用于提供 Python 与底层系统库或者其他语言库的接口。
2. **编译二进制**：与 .py 文件相比，pyd 文件是经过编译的二进制文件，因此执行效率更高。这使得在Python中调用这些模块时可以获得更好的性能。
3. **可导入**：与 .py 文件类似，pyd 文件也可以被 Python 的 import 语句导入，并且可以像其他 Python 模块一样被使用。

**.py  .pyc  .pyd** 之间的**区别**：

- **py文件**：是Python源代码文件，以 .py 为扩展名，可以通过 Python 解释器直接执行。它是 Python 程序的原始形式，可以在 Python 环境中运行。
- **pyc文件**：是 Python 编译文件，以 .pyc 为扩展名，是将 .py 文件编译成的字节码文件，加快了 Python 程序的执行速度。当 Python 脚本第一次执行时，解释器会将 .py 文件编译成 .pyc 文件，然后执行 .pyc 文件，如果 .py 文件没有修改过，解释器会直接加载 .pyc 文件而不再编译。
- **pyd文件**：是 Python 的动态链接库文件，以 .pyd 为扩展名，包含了用 C 或 Cython 等语言编写的 Python 扩展模块的二进制代码。通常用于扩展 Python 的功能，与 Python 的其他模块一样，可以被导入和使用。



## 1.2. 使用Cython进行编译

**Cython** 是一个用于编写 **C 扩展**的 **Python** 语言的编译器，它使得 Python 代码可以被**转换**成 C 代码，并且能够被编译成 Python 扩展模块，这些模块可以在 Python 中被导入和使用。Cython 结合了 Python 的简洁性和 C 语言的性能，使得开发者可以在 Python 中编写高效的扩展模块。

### 1.2.1. 安装依赖

对于 Windows 用户，可以直接使用 pip 进行安装

```bash
pip install cython
```

检查 Cython 的安装：键入以下代码，回车

```bash
cython --version
```

如果安装成功，此时应该会显示 Cython 的版本信息

```bash
>>>cython --version
Cython version 3.0.10
```



### 1.2.2. 进行编译

假设项目结构如下：

```bash
- app.py
- hello_world.py
```

其中，app.py 是主脚本，只负责调用，不参与编译；hello_world.py 是被调用的脚本(模块)，将进行编译

```python
# app.py
import hello_world


if __name__ == "__main__":
    hello_world.say_to('Sandm')
    
```

```python
# hello_world.py
def say_to(name: str) -> None:
    """Say 'hello World' to someone
    Args:
        name: (str) The name of someone who wants to say hello

    Returns:
        None
    """
    print('Hello World,', name)

```

创建 **setup.py** 进行编译配置

在编译前，请将原先的 **hello_world.py** 重命名为 **hello_world.pyx** ，这样做不会影响编译，但有利于 ide 的代码补全和编译后的调用。

[^注释]: 在调用模块时，Python 解释器会优先寻找 .py，未找到时才会寻找 .pyd



```python
# setup.py
from distutils.core import setup
from Cython.Build import cythonize


setup(
    ext_modules=cythonize("hello_world.pyx")
)

```

此时项目结构应该如下：

```bash
- app.py
- hello_world.pyx
- setup.py
```

使用 **cmd** 命令行 cd 到该目录下，输入以下命令并回车，开始编译

```bash
python setup.py build_ext --inplace
```

- `build_ext`：这是一个命令选项，表示执行扩展模块的构建操作。
- `--inplace`：这个参数表示将编译生成的扩展模块（通常是 `.so` 或 `.pyd` 文件）直接放在当前目录下，而不是安装到默认的 Python 库目录中。这样在当前环境中可以直接使用该扩展模块进行测试等操作。

输出：

```bash
Compiling hello_world.pyx because it changed.
[1/1] Cythonizing hello_world.pyx
...
hello_world.c
hello_world.obj : warning LNK4197: export 'PyInit_hello_world' specified multiple times; using first specification
   Creating library build\temp.win-amd64-cpython-311\Release\hello_world.cp311-win_amd64.lib and object build\temp.win-amd64-cpython-311\Release\hello_world.cp311-win_amd64.exp
Generating code
Finished generating code
```

编译成功后目录结构应该如下：

```bash
- build
 - lib.win-amd64-cpython-311
  - hello_world.cp311-win_amd64.pyd
  
 - temp.win-amd64-cpython-311
  - Release
   - hello_world.cp311-win_amd64.exp
   - hello_world.cp311-win_amd64.lib
   - hello_world.obj

- app.py
- hello_world.pyx
- setup.py
- hello_world.c
- hello_world.cp311-win_amd64.pyd

```

类似地，**hello_world.cp311-win_amd64.pyd** 命名规则为：

**{编译的文件名}**.**{Python解释器版本}**.**{系统架构}**.pyd

因此，我们不难看出，生成的 pyd 文件只能在**相应的** Python 解释器和操作系统运行。



### 1.2.3. 调用编译后的pyd文件

编译后的 pyd 文件可以在 Python 程序中作为模块来调用。

虽然上节内容生成了一个 c 文件，两个 pyd 文件，以及其他乱七八糟的东东，但我们这里只关心根目录下的 pyd(hello_world.cp311-win_amd64.pyd)，我们调用的也是它。

事实上，您无需对原脚本进行修改，虽然 ide 可能会提示 `未解析的引用`，但是能正常运行的

```python
# app.py
import hello_world


if __name__ == "__main__":
    hello_world.say_to('Sandm')
    
```

由于被编译成二进制文件，您并不能直接查看被调用模块的内容，但可以使用`help()`方法查看模块具有的方法和用法。例如

```python
# xxx.py
import hello_world


help(hello_world)

```

运行后，应该会输出

```bash
Help on module hello_world:

NAME
    hello_world

FUNCTIONS
    say_to(name: 'str') -> 'None'
        Say 'hello World' to someone
        Args:
            name: (str) The name of someone who wants to say hello
        
        Returns:
            None

DATA
    __test__ = {}

FILE
    ...\hello_world.cp311-win_amd64.pyd

```



## 1.3. 搭配PyArmor加密编译

虽然编译后的 pyd 文件不能被直接查看，但仍然容易被逆向修改。因此，您可以选择使用 PyArmor 进行加密。

### 1.3.1. 安装依赖

对于 Windows 用户，可以直接使用 pip 进行安装

```bash
pip install pyarmor
```

检查 PyArmor 的安装：键入以下代码，回车

```bash
pyarmor --version
```

如果安装成功，此时应该会显示 PyArmor 的版本信息

```bash
>>>pyarmor --version
Pyarmor 8.5.9 (trial), 000000, non-profits

License Type    : pyarmor-trial
License No.     : pyarmor-vax-000000
License To      :
License Product : non-profits

BCC Mode        : No
RFT Mode        : No

Notes
* Can't obfuscate big script and mix str
```



### 1.3.2. 开始加密

依然以 `1.2.2`节开始的项目结构为例

```bash
- app.py
- hello_world.py
```

打开 **cmd** 命令行 cd 到项目目录下，键入以下命令生成配置文件

```bash
pyarmor cfg restrict_module=0
```

上述命令了禁用加密的约束模式，将加密脚本作为普通脚本一样使用 Cython 进行处理。如果不禁用约束模式，会导致校验错误 `RuntimeError: unauthorized use of script`

使用 **cmd** 命令行输入以下命令并回车，开始加密

```bash
pyarmor gen hello_world.py
```

如果加密成功，应该会输出

```bash
INFO     Python 3.11.9
INFO     Pyarmor 8.5.9 (trial), 000000, non-profits
INFO     Platform windows.x86_64
INFO     search inputs ...
INFO     find script hello_world.py
INFO     find 1 top resources
INFO     start to generate runtime files
INFO     target platforms {'windows.amd64'}
INFO     write dist\pyarmor_runtime_000000\pyarmor_runtime.pyd
INFO     generate runtime files OK
INFO     start to obfuscate scripts
INFO     process resource "hello_world"
INFO     obfuscating file hello_world.py
INFO     write dist\hello_world.py
INFO     obfuscate scripts OK
```

将`dist`目录下的所有文件(包括文件夹)移到项目根目录(可能会替换掉原文件，请提前备份好或者重命名)，此时项目结构应该为

```bash
- .pyarmor
 - config
 
- pyarmor_runtime_000000
 - __init__.py
 - pyarmor_runtime.pyd
 
- app.py
- hello_world.py
```

其中，`.pyarmor`为加密配置文件目录，`pyarmor_runtime_000000`为加密包(被加密文件需要它才能运行)，`hello_world.py`为加密后的文件(你可以把它当作普通的 py 脚本，只不过被加密了)

```python
# hello_world.py
# Pyarmor 8.5.9 (trial), 000000, non-profits, 2024-06-04T23:34:27.585289
from pyarmor_runtime_000000 import __pyarmor__
__pyarmor__(__name__, __file__, b'PY000000\x00\x03\x0b\x00\xa7\r\r\n\x80\x00\x01\x00\x08\x00\x00..."')

```

使用  Cython 编译成 pyd 文件的步骤与上节相同，这里就不赘述了。



---

*That's all. Thanks for your reading..*

> 任何一个傻瓜都会写能够让机器理解的代码，只有好的程序员才能写出人类可以理解的代码。
>
> ——Martin Fowler

