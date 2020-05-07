---
title: Cookiecutter更好的项目模板工具（3）高级用法
author: Tacey Wong
summary: 转载Cookiecutter文章，详解了Cookiecutter的高级用法
date: 2020-4-24 10:30:00
categories:
    - 转载
tags:
    - python工具包
    - 后端文章转载
---

> 本文转载于：https://www.cnblogs.com/taceywong/p/10508314.html
>
> 文章作者：Tacey Wong

本文中的**Hook**就是**钩子**,**钩子**就是**Hook**

## 使用生成前/生成后钩子脚本

你可以在项目生成之前和/或之后运行Python或Shell脚本。

像这样把它们放进Hook里：

```text
cookiecutter-something/
├── {{cookiecutter.project_slug}}/
├── hooks
│   ├── pre_gen_project.py
│   └── post_gen_project.py
└── cookiecutter.json
```

Shell脚本工作方式类似：

```text
cookiecutter-something/
├── {{cookiecutter.project_slug}}/
├── hooks
│   ├── pre_gen_project.sh
│   └── post_gen_project.sh
└── cookiecutter.json
```

将Cookiecutter扩展到使用其他类型的脚本应该不会太难。你可以根据你系统中存在的脚本系统尝试一下。

为了可移植性，你应该为Hook使用Python脚本(扩展名为.py)，因为这些脚本可以在任何平台上运行。但是，如果您希望模板仅在单个平台上运行，则shell脚本（或Windows上的.bat文件）原则上都是可行的。

### 编写钩子

这里有一些关于如何编写生成前/生成后钩子脚本的细节。

**以适当的状态退出**

确保钩子脚本以一种健壮的方式工作。如果钩子脚本运行失败(也就是说它以非零退出状态结束)，项目生成将停止，生成的目录将被清除。

**当前工作目录**

运行钩子脚本时，它们的工作目录是生成的项目的根目录。这使得后生成挂钩可以使用相对路径轻松查找生成的文件。

** 模板变量在脚本中呈现**

就像你的项目模板一样，Cookiecutter也在你的脚本中呈现Jinja模板语法。这使您可以将Jinja模板变量合并到脚本中。例如，这行Python将`module_name`设置为`cookiecutter.module_name`模板变量的值：

```python
module_name = '{{ cookiecutter.module_name }}'
```

### 示例：验证模板变量

下面是一个脚本示例，它在生成项目之前验证模板变量,将其保存到`hooks/pre_gen_project.py:`中：

```python
import re
import sys


MODULE_REGEX = r'^[_a-zA-Z][_a-zA-Z0-9]+$'

module_name = '{{ cookiecutter.module_name }}'

if not re.match(MODULE_REGEX, module_name):
    print('ERROR: %s  不是有效的Python模块名称！' % module_name)
    # 以状态1退出，表示失败
    sys.exit(1)
```

## 用户配置

如果你经常使用Cookiecutter，你会发现有一个用户配置文件很有用。默认情况下，Cookiecutter尝试从主目录中的.cookiecutterrc文件中检索设置。从1.3.0版本开始，您还可以通过`--config-file`在命令行上指定配置文件。

```bash
$ cookiecutter --config-file /home/tacey/my-custom-config.yaml    cookiecutter-pypackage
```

或者你可以设置`COOKIECUTTER_CONFIG`环境变量：

```bash
export COOKIECUTTER_CONFIG=/home/tacey/my-custom-config.yaml 
```

如果您希望坚持使用内置配置而不加载任何用户配置文件，请使用cli选项`--default-config`。阻止Cookiecutter加载用户设置对于在隔离环境中编写集成测试至关重要。

用户配置示例：

```yaml
default_context:
    full_name: "Tacey Wong"
    email: "xinyong.wang@qq.com"
    github_username: "taceywong"
cookiecutters_dir: "/home/tacey/my-custom-cookiecutters-dir/"
replay_dir: "/home/tacey/my-custom-replay-dir/"
abbreviations:
    pp: https://github.com/audreyr/cookiecutter-pypackage.git
    gh: https://github.com/{0}.git
    bb: https://bitbucket.org/{0}
```

可用的设置是：

- default_context: 每当使用Cookiecutter生成项目时，您希望作为上下文注入的键/值对的列表。生成任何项目时，这些值将被视为cookiecutter.json中的默认值。
- cookiecutters_dir: 当你使用Cookiecutter和git repo参数时，你的Cookiecutter模板被克隆到的目录。
- replay_dir: Cookiecutter转储上下文数据的目录，稍后您可以在使用replay功能时获取该目录。
- abbreviations: cookiecutters的缩写列表。缩写可以是repo名称的简单别名，也可以用作`别名:后缀`形式的前缀。任何后缀都将使用标准的Python字符串格式插入到扩展中以代替文本{0}，使用上面的别名，你可以简单地通过`cookiecutter pp`或`或cookiecutter gh:audreyr/cookiecutter-pypackage`来使用cookiecutter -pypackage模板。上面显示的gh (github)、bb (bitbucket)和gl (gitlab)缩略语实际上是内置的，不需要自己定义就可以使用它们。

## 在Python代码中调用Cookiecutter

你可以在Python代码中使用Cookiecutter

```python
from cookiecutter.main import cookiecutter

#根据cookiecutter-pypackage/ 模板创建项目
cookiecutter('cookiecutter-pypackage/')

# 根据cookiecutter-pypackage.git repo 模板创建项目
cookiecutter('https://github.com/audreyr/cookiecutter-pypackage.git')
```

如果您正在编写一个web框架，并且需要为开发人员提供一个类似于django-admin.py startproject或npm init的工具，这将非常有用。

### 抑制命令行提示

使用`no_input`抑制要求输入的提示。

**基本示例：使用默认设置**

如果与`no_input`一起使用，Cookiecutter将选择一个默认值：

```python
from cookiecutter.main import cookiecutter
cookiecutter(
    'cookiecutter-django',
    no_input=True,
)
```

在这种情况下，它将使用cookiecutter.json或.cookiecutterrc中定义的默认值。(来自cookiecutter.json的值将被.cookiecutterrc中的值覆盖)

**高级示例：默认+额外的上下文**
如果将`extra_context dict`与`no_input`参数组合在一起， 您可以使用设置的上下文参数列表以编程方式创建项目，而无需任何命令行提示：

```python
cookiecutter('cookiecutter-pypackage/',
             no_input=True,
             extra_context={'project_name': 'TheGreatest'})
```

有关这部分的更多详细信息，请参阅[API参考](https://cookiecutter.readthedocs.io/en/latest/index.html#apiref)。

## 上下文值中的模板

cookiecutter.json的值（但不是键！）也是Jinja2模板。用户提示符中的值会立即添加到上下文中，这样就可以从以前的值派生出一个上下文值。通过提供更合理的默认值，这种方法可以潜在地为用户节省许多键盘按键操作。

### 基础示例：上下文中的模板

Python包显示了其命名约定的一些模式：

- 一个人类可读的项目名称
- 小写、横岗组成的库名
- 一个可导入的，无破折号的包名称

这是一个带有此模式模板值的cookiecutter.json：

```json
{
  "project_name": "My New Project",
  "project_slug": "{{ cookiecutter.project_name|lower|replace(' ', '-') }}",
  "pkg_name": "{{ cookiecutter.project_slug|replace('-', '') }}"
}
```

如果用户采用默认值或使用`no_input`，则模板化值将为：

- my-new-project
- mynewproject

或者，如果用户提供Yet Another New Project，则值为：

- yet-another-new-project
- yetanothernewproject

## 复制而不渲染

可以在cookiecutter.json中使用_copy_without_render键来避免渲染cookiecutter的目录和文件。此键的值接受Unix shell样式通配符列表：

```json
{
    "project_slug": "sample",
    "_copy_without_render": [
        "*.html",
        "*not_rendered_dir",
        "rendered_dir/not_rendered_file.ini"
    ]
}
```

## 重播项目生成

在调用时，Cookiecutter将json文件转储到`〜/.cookiecutter_replay/`，这使你可以稍后重放。

换句话说，它会保留您对模板的输入，并在您再次运行相同模板时获取它。

重播文件的示例（通过`cookiecutter gh：hackebrot/cookiedoze`r创建）：

```json
{
    "cookiecutter": {
        "app_class_name": "FooBarApp",
        "app_title": "Foo Bar",
        "email": "raphael@example.com",
        "full_name": "Raphael Pierzina",
        "github_username": "hackebrot",
        "kivy_version": "1.8.0",
        "project_slug": "foobar",
        "short_description": "A sleek slideshow app that supports swipe gestures.",
        "version": "0.1.0",
        "year": "2015"
    }
}
```

要在不在命令行上提示的情况下获取此上下文数据，您可以使用以下任一方法。

在命令行上传递相应选项：

```bash
cookiecutter --replay gh:hackebrot/cookiedozer
```

或者使用Python API：

```python
from cookiecutter.main import cookiecutter
cookiecutter('gh:hackebrot/cookiedozer', replay=True)
```

如果要从更新的模板创建新项目，此功能就会派上用场。

## 命令行选项

`-V, --version`
显示版本并退出。

`--no-input`
不要提示参数，只使用cookiecutter.json文件内容

`-c, --checkout`
branch, tag or commit to checkout after git clone

`-v, --verbose`
打印调试信息

`--replay`
不要提示参数，只使用之前输入的信息

`-f, --overwrite-if-exists`
如果输出目录的内容已经存在，则覆盖它

`-o, --output-dir`
将生成的项目目录输出到哪里，即指定创建项目的目录。

`--config-file`
指定用户配置文件

`--default-config`
不要加载配置文件。使用默认值

`--debug-file`
指定用作DEBUG日志记录流文件

## 选择变量

选择变量在创建项目时提供不同的选择。 根据用户的选择，模板以不同的方式渲染内容。

### 基本用法

选择变量是常规键/值对，但值是字符串列表。

例如，如果在`cookiecutter.json`中提供以下选项变量：

```json
{
    "license": ["MIT", "BSD-3", "GNU GPL v3.0", "Apache Software License 2.0"]
}
```

运行Cookiecutter时你会得到以下选择：

```text
Select license:
1 - MIT
2 - BSD-3
3 - GNU GPL v3.0
4 - Apache Software License 2.0
Choose from 1, 2, 3, 4 [1]:
```

根据用户的选择，Cookiecutter将渲染不同的许可证。
上面的许可选择变量创建了`cookiecutter.license`，可以像下面这样使用：

```json
{%- if cookiecutter.license == "MIT" -%}
# 此处可能的许可内容

{%- elif cookiecutter.license == "BSD-3" -%}
# 这里有更多可能的许可内容
```

Cookiecutter使用[Jinja2 s if条件表达式](http://jinja.pocoo.org/docs/dev/templates/#if)来确定正确的许可证。

创建的选择变量仍然是常规的Cookiecutter变量，可以像这样使用：

```text
License
-------

Distributed under the terms of the `{{cookiecutter.license}}`_ license,
```

### 覆盖默认选择值

选择变量可以使用用户配置文件覆盖。

例如，可以使用列表作为值在`cookiecutter.json`中创建选择变量：

```json
{
    "license": ["MIT", "BSD-3", "GNU GPL v3.0", "Apache Software License 2.0"]
}
```

默认情况下，值列表中的第一个条目用作提示中的默认值。

将默认许可协议设置为Apache Software License 2.0可以在用户配置文件中使用以下方法完成：

```yaml
default_context:
    license: "Apache Software License 2.0"
```

提示会发生变化，如下所示:

```text
Select license:
1 - Apache Software License 2.0
2 - MIT
3 - BSD-3
4 - GNU GPL v3.0
Choose from 1, 2, 3, 4 [1]
```

**注意：**如你所见，选项的顺序从`1 - MIT`更改为`1 - Apache Software License 2.0`。 Cookiecutter将列表中的第一个值作为默认值。

## 字典变量

字典变量提供了一种在渲染模板时定义深层结构化信息的方法。

### 基础使用

顾名思义，字典变量是键值对的字典。 字典值本身可以是其他字典和列表 - 数据结构可以根据需要进行深入。

例如，您可以在`cookiecutter.json`中提供以下字典变量：

```json
{
    "project_slug": "new_project",
    "file_types": {
        "png": {
            "name": "Portable Network Graphic",
            "library": "libpng",
            "apps": [
                "GIMP"
            ]
        },
        "bmp": {
            "name": "Bitmap",
            "library": "libbmp",
            "apps": [
                "Paint",
                "GIMP"
            ]
        }
    }
}
```

上面的file_type字典变量创建了cookiecutter.file_types，可以像这样使用：

```html
{% for extension, details in cookiecutter.file_types|dictsort %}
<dl>
  <dt>Format name:</dt>
  <dd>{{ details.name }}</dd>

  <dt>Extension:</dt>
  <dd>{{ extension }}</dd>

  <dt>Applications:</dt>
  <dd>
      <ul>
      {% for app in details.apps -%}
          <li>{{ app }}</li>
      {% endfor -%}
      </ul>
  </dd>
</dl>
{% endfor %}
```

Cookiecutter使用[Jinja2 for表达式](http://jinja.pocoo.org/docs/dev/templates/#for)来迭代字典中的条目。

## 模板扩展

模板可以使用自定义[Jinja2扩展](http://jinja2.readthedocs.io/en/latest/extensions.html#extensions)扩展Cookiecutter环境，可以添加额外的过滤器，测试，全局变量甚至扩展解析器。

为此，模板作者必须在`cookiecutter.json`中指定所需的扩展名，如下所示：

```json
{
    "project_slug": "Foobar",
    "year": "{% now 'utc', '%Y' %}",
    "_extensions": ["jinja2_time.TimeExtension"]
}
```

在调用时，Cookiecutter尝试导入扩展并分别将它们添加到其环境中。

在上面的示例中，Cookiecutter在安装j[inja2_time.TimeExtension](https://www.cnblogs.com/taceywong/p/10508314.html)之后提供了附加标记[now](https://www.cnblogs.com/taceywong/p/10508314.html),并在`cookiecutter.json`中启用它。

请注意，Cookiecutter**不会**自行安装任何依赖项！ 作为用户，您需要确保已安装所有扩展，然后在需要自定义Jinja2扩展的模板上运行Cookiecutter。