---
title: Python中的虚拟环境（Virtualenv）及其工作原理
author: 匿蟒
summary: 转载优质文章
date: 2020-4-24 16:10:00
categories:
    - 优质文章
tags:
    - python
    - 后端文章转载
---

> 本文转载于：https://note.qidong.name/2019/03/python-virtualenv/
>
> 文章作者：[匿蟒](http://weibo.com/u/2837672545)
>
> 作者博客：https://note.qidong.name/

## 简介

也许Python由于2和3的分裂，Python发展出了虚拟环境（[virtualenv](https://virtualenv.pypa.io/)）的技术。 还有一个原因——可能这才是主要原因——Python的包只能同时安装一个版本。 这种行为是学自系统包管理器。 对系统来说，一个包安装一个版本就够了。 但是对一个开发多个复杂项目的环境来说，只有像Java系的Maven库那样，多个版本同时保留，依赖检查延迟到打包时，才能确保并行开发。 否则，A项目需要S软件的1.0版本，B项目需要它的2.0，这就没法一起玩了。

[virtualenv](https://virtualenv.pypa.io/)的关键词是隔离（Isolation）。 它能创造一个包含特定版本的Python环境，并且确保Python软件包非常干净。 它创造性地使用了一些Shell和Python原有的机制，实现了虚拟环境的功能。

因此，开发每个Python项目时，都推荐创建对应的[virtualenv](https://virtualenv.pypa.io/)来隔离开发。 这样可以不受系统Python软件包的影响，安装任意包的任意版本，并且最终能通过`pip freeze > requirements.txt`获取依赖列表。 （当然，这个列表通常需要裁剪。）

### 安装virtualenv

使用`apt`、`yum`等包管理器安装的版本老旧，推荐使用`pip`安装。

```sh
pip3 install --user virtualenv
```

### 准备virtualenv

每个项目，都需要独立创建一个（或多个）虚拟环境，隔离开发。

```sh
virtualenv -p python3 venv
```

`-p`是显式指定Python版本，避免使用默认的`python`。 虚拟环境的常用名，可选择`env`、`venv`、`.env`、`.venv`。 `venv`是PyCharm的默认虚拟环境名称。

### 激活virtualenv

默认使用的是用户+系统环境，激活后才是虚拟环境。

```sh
source venv/bin/activate
```

激活虚拟环境后，可以看到只有三个Python包。 这个环境可以随意使用，所有安装都会在`./venv/`下，不会影响系统环境。 干净的环境，也能帮助开发人员确认依赖。

```shell
$ pip list
Package    Version
---------- -------
pip        19.0.3
setuptools 40.8.0
wheel      0.33.1
```

在这个虚拟环境中，`python`就是`python3`，而系统环境的`python`通常是`python2`。 在安装软件时，直接使用`pip`，即可安装到虚拟环境中。 而不像一般状态下，要么加`sudo`提权（[brew]或Windows环境下不用），要么安装时需要加`--user`，安装到用户目录下。

以下是系统、用户、虚拟环境三种方式安装，以及可执行文件`pylint`被安装的位置。

```shell
$ sudo pip install pylint
$ ls /usr/local/bin/pylint
/usr/local/bin/pylint

$ pip install --user pylint
$ ls ~/.local/bin/pylint
/home/user/.local/bin/pylint

$ source venv/bin/activate
$ pip install pylint
$ ls venv/bin/pylint
venv/bin/pylint
```

### 退出virtualenv

```sh
deactivate
```

退出后，回到用户+系统环境。

## 虚拟环境的原理

上面的实操指南，只要照做，就能把虚拟环境用起来。 用多了，自然能够理解它。 [virtualenv](https://virtualenv.pypa.io/)是如何创建一个隔离的Python虚拟环境？这个环境有什么特点？

这个环境的特点有二：

- Python版本固定。即使系统的Python升级了，虚拟环境中的仍然不受影响，保留开发状态。
- 所有Python软件包，都只在这个环境生效。一旦退出，则回到用户+系统的默认环境中。

这两个特点，由两个小手段实现。

- 改变当前Shell的`PATH`。
- 改变Python运行时的`sys.path`。

以下为`python:alpine`镜像中，以`root`用户演示的例子。

### 改变`PATH`

首先看一下它的目录结构：

```shell
# ls venv
bin      include  lib
# ls /usr/local
bin      include  lib      share
```

环境内所有的新内容，都在这个新生成目录下。 `bin`是可执行文件的位置，`include`是C/C++的头文件位置，`lib`是库文件位置。 它和`/usr/local`内的主要目录几乎相同，也和`~/.local`下类似。

魔法都在两个`PATH`中。

```shell
# echo $PATH
/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
# . venv/bin/activate
(venv) # echo $PATH
/root/venv/bin:/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

显然，这个`activate`，为当前`PATH`增加了`venv/bin`这个位置在最前方，因此虚拟环境中的可执行文件拥有最高优先级。 而`lib`与`include`，仅仅是`bin`下面的可执行文件做相对路径运算来寻找的位置。 所以，改变了`PATH`，就改变了很多事。

```shell
# ls -hl venv/bin/
total 88
-rw-r--r--    1 root     root        2.0K Mar 31 08:06 activate
-rw-r--r--    1 root     root        1.1K Mar 31 08:06 activate.csh
-rw-r--r--    1 root     root        3.0K Mar 31 08:06 activate.fish
-rw-r--r--    1 root     root        1.5K Mar 31 08:06 activate.ps1
-rw-r--r--    1 root     root         986 Mar 31 08:06 activate.xsh
-rw-r--r--    1 root     root        1.5K Mar 31 08:06 activate_this.py
-rwxr-xr-x    1 root     root         238 Mar 31 08:06 easy_install
-rwxr-xr-x    1 root     root         238 Mar 31 08:06 easy_install-3.7
-rwxr-xr-x    1 root     root         220 Mar 31 08:06 pip
-rwxr-xr-x    1 root     root         220 Mar 31 08:06 pip3
-rwxr-xr-x    1 root     root         220 Mar 31 08:06 pip3.7
-rwxr-xr-x    1 root     root       35.8K Mar 31 08:06 python
-rwxr-xr-x    1 root     root        2.3K Mar 31 08:06 python-config
lrwxrwxrwx    1 root     root           6 Mar 31 08:06 python3 -> python
lrwxrwxrwx    1 root     root           6 Mar 31 08:06 python3.7 -> python
-rwxr-xr-x    1 root     root         216 Mar 31 08:06 wheel
```

由于优先级最高，所以环境里的`python`、`pip`等，包括后来用`pip`安装的可执行文件，都使用的是`venv`下的。

### 改变`sys.path`

```shell
(venv) # python -m site
sys.path = [
    '/root',
    '/root/venv/lib/python37.zip',
    '/root/venv/lib/python3.7',
    '/root/venv/lib/python3.7/lib-dynload',
    '/usr/local/lib/python3.7',
    '/root/venv/lib/python3.7/site-packages',
]
USER_BASE: '/root/.local' (doesn't exist)
USER_SITE: '/root/.local/lib/python3.7/site-packages' (doesn't exist)
ENABLE_USER_SITE: False
(venv) # deactivate
# python -m site
sys.path = [
    '/root',
    '/usr/local/lib/python37.zip',
    '/usr/local/lib/python3.7',
    '/usr/local/lib/python3.7/lib-dynload',
    '/usr/local/lib/python3.7/site-packages',
]
USER_BASE: '/root/.local' (doesn't exist)
USER_SITE: '/root/.local/lib/python3.7/site-packages' (doesn't exist)
ENABLE_USER_SITE: True
```

可见，`sys.path`发生了翻天覆地的变化。 除了当前路径`/root`和标准库`/usr/local/lib/python3.7`被保留以外，其它位置都换成了`venv`下的。 这就是为什么`pip list`看不见什么软件包的原因，也是环境隔离的最大秘密。

## 标准库venv

从Python 3.3开始，标准库中就自带了一个[venv](https://docs.python.org/3/library/venv.html)模块，拥有[virtualenv](https://virtualenv.pypa.io/)的部分功能。 因此，也可以通过以下命令来创建虚拟环境。

```sh
python3 -m venv venv
```

但还是推荐使用[virtualenv](https://virtualenv.pypa.io/)。 [venv](https://docs.python.org/3/library/venv.html)只能创建当前版本的虚拟环境，不能创建其它Python 3.x的版本，以及Python 2的环境。

## 结语

虚拟环境技术，早已成为当今Python生态的一个核心内容。 除了广泛而频繁的使用以外，它也成为了其它技术的基石。

- [autoenv](https://github.com/kennethreitz/autoenv)是一个让你`cd`到某个目录下，就自动`source`进入虚拟环境的小工具，省去了手动`source`的操作。
- [pipenv](https://github.com/pypa/pipenv)基于[Pipfile](https://github.com/pypa/pipfile)，结合了`pip`和[virtualenv](https://virtualenv.pypa.io/)，让你可以有更好的依赖管理。
- [tox](https://tox.readthedocs.io/)是一个标准化、自动化的测试技术。 基于[virtualenv](https://virtualenv.pypa.io/)，它能同时在不同平台、类型与版本的Python下测试，确保软件包的兼容性。
- [conda](https://conda.io/)是专为数据科学而设计的[Anaconda](https://www.anaconda.com/)的包管理器。 它不仅是`pip`的另一个替代，而且更进一步，自建了完整生态。 它充分利用了[virtualenv](https://virtualenv.pypa.io/)的特点，把C/C++的包也纳入管理，改进了原先`pip`+`apt`才能实现的依赖管理。

当然，最近几年，由[Docker]兴起而导致的革命，改变了一切。 在生产环境，虚拟环境最终被容器所取代，仅在开发环境和部分测试环境据守。

## 参考

- [Virtualenv — virtualenv 16.4.4.dev0 documentation](https://virtualenv.pypa.io/)
- [venv — Creation of virtual environments — Python 3.7.3 documentation](https://docs.python.org/3/library/venv.html)
- [Pipenv & Virtual Environments — The Hitchhiker's Guide to Python](https://docs.python-guide.org/dev/virtualenvs/) Python由于2和3的分裂，发展出了虚拟环境（[virtualenv](https://virtualenv.pypa.io/)）的技术。