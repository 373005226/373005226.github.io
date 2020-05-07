---
title: Cookiecutter更好的项目模板工具（2）安装及基础使用
author: Tacey Wong
summary: 转载Cookiecutter文章，详解了Cookiecutter的安装及使用
date: 2020-4-24 10:28:00
categories:
    - 转载
tags:
    - python工具包
    - 后端文章转载
---

> 本文转载于：https://www.cnblogs.com/taceywong/p/10508130.html
>
> 文章作者：Tacey Wong

## 安装

**通过python包管理工具**
命令行输入

```bash
 $pip install  cookiecutter
```

或者

```bash
# mac os经常会禁止用户全局安装python包
$pip install --user cookiecutter
```

如果使用的是anaconda，想使用`conda`安装，可以

```bash
$conda config --add channels conda-forge
$conda install cookiecutter
```

***特别提醒：要保证Python在你的系统PATH路径下，同时保证`bin`目录在你的系统PATH路径下。特别提醒，anaconda安装的Python，其安装的python执行文件默认不在系统路径下，一般在·$ANACONDA/bin·,请将其放置在系统路径下。

------

**通过系统的包管理**

ubuntu

```bash
$sudo apt install cookiecutter
```

mac os

```bash
$brew install cookiecutter
```

## Cookiecutter模板基础使用

Cookiecutter模板的形式：

- 模板文件夹
- 模板文件夹的zip压缩文件
- 模板文件夹的git地址
- 模板文件夹的zip压缩文件url地址
- 模板文件夹在cookiecutter目录下

针对具体形式分别使用相同的命令形式：

```bash
# 模板文件夹
$ cookiecutter /path/to/template
# 模板文件夹zip压缩包文件
$ cookiecutter /path/to/template.zip
# 模板文件夹的git地址
$ cookiecutter git地址
$ cookiecutter https://github.com/audreyr/cookiecutter-pypackage.git --checkout develop
# 模板文件夹zip压缩文件URL
$  cookiecutter  https://example.com/path/to/template.zip
# 模板文件夹在cookiecutter目录（缺省会~/.cookiecutters/目录下）
$ cookiecutter    模板名字
```

特别针对有密码的zip文件,cookiecutter回给出提示符让你输入密码，如果想跳过密码输入，可以设置`COOKIECUTTER_REPO_PASSWORD`环境变量，在zip文件需要密码的时候会自动填入该环境变量的值。

整体需要注意的：

- 无论何时你根据一个模板生成了一个项目，生成的项目都会在你的当前路径下
- 克隆的cookiecutters默认存储在`〜/.cookiecutters/`目录中

## 创建Cookiecutter模板

Cookiecutter功能原理是获取模板的源目录树并将其复制到新项目中，将在模板用`{{`和`}}`标记中找到的所有名称替换为`cookiecutter.json`文件中找到的名称。

替换的名称可以是文件名、目录名和文件中的字符串。

使用Cookiecutter，你可以轻松地从一个标准表单引导一个新项目，这意味着在开始一个新项目时，你可以跳过所有常见的错误，拜托琐碎操作带来的时间消耗以快速开始有真正意义的工作。

以下以一个简单的“Hello World”来介绍Cookiecuter的创建过程：

首先，在您的计算机上创建一个目录。这个目录的名称将是您的Cookiecutter模板的名称，但是它并不限制任何其他内容——例如，生成的项目不需要使用模板名称。我们将项目名称设为`HelloWorld`

```bash
$ mkdir HelloWorld
$ cd HelloWorld
```

我们在此目录中创建要复制到生成项目中的目录树。我们想要为此目录生成一个名称，所以我们将目录名称放在模板标签中:

```bash
$ mkdir {{ cookircutter.directory_name }}
$ cd {{ cookircutter.directory_name }}
```

模板标记中的任何内容都可以放在名称空间中。`cookiecutter.directory_name`将被从`cookiecutter.json`文件中查找，因为该项目是由Cookiecutter生成的。

现 我们在将要被复制的目录树中。对于这个最简单的Cookiecutter模板，我们打算只包含一个文件。同样，我们希望从`cookiecutter.json`中查找文件名，因此我们将其命名为：

```bash
$ touch {{cookiecutter.file_name}}.py
```

(`touch`会创建一个空文本文件;你可以在编辑器中打开它）。 现在编辑文件，使其包含：

```bash
print("Hello, {{cookiecutter.greeting_recipient}}!")
```

最后，我们来创建`cookiecutter.json`文件，以便Cookiecutter可以查找我们所需要模板项。

```json
{
    "directory_name": "Hello",
    "file_name": "Howdy",
    "greeting_recipient": "Tacey"
}
```

现在我们可以运行Cookiecutter并从模板创建一个新项目。切换到要创建新项目的目录。然后运行Cookiecutter并将模板所在的目录传给它。

```bash
$ cookiecutter /the/path/of/HelloWorld
directory_name [Hello]:
file_name [Howdy]:
greeting_recipient [Tacey]:
```

Cookiecutter告诉我们每个项目的默认名称是什么，并为我们提供了用新名称替换该名称的选项。在这种情况下我只是简单的按回车接受所有的默认值~

现在我们生成了一个名为`Hello`的目录，其中包含一个文件`how .py`。当我们运行它：

```bash
$ python Howdy.py
Hello, Tacey!
```

Wow!这就完事儿了~

## 本章结语

到了这里，如果你在开始新项目时发现自己在重复工作，你就已经会知道如何使用cookiecutter消除这种无谓的重复了。对于一些常见的项目，不妨尝试选择使用[Cookiecutter: 更好的项目模板工具：（1）简介及可用资源汇总](https://www.cnblogs.com/taceywong/p/10506032.html)中的某个模板~~~~~