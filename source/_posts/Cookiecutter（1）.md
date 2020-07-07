---
title: Cookiecutter更好的项目模板工具（1）简介及可用资源汇总
author: Tacey Wong
summary: 转载Cookiecutter文章，详解了Cookiecutter的特点、如何使用、模板大全
date: 2020-4-24 10:25:00
categories:
    - 后端
tags:
    - python工具包
    - 后端文章转载
---

> 本文转载于：https://www.cnblogs.com/taceywong/p/10506032.html
>
> 文章作者：Tacey Wong

原文档地址：https://cookiecutter.readthedocs.io/en/latest/

本系列只介绍cookiecutter的基础使用，而且会删除与功能使用无关的部分。深度使用及了解更多详情请请参考原文档。

关于Cookiecutter的中文译名，我不知道别人怎么翻译的，我将其翻译为**饼干制造机**。本系列中将不对其作翻译，直接使用英文原名。

## Cookiecutter简介

![img](https://img2018.cnblogs.com/blog/673170/201903/673170-20190310154708274-19053537.png)

Cookiecutter 是一个通过项目模板创建项目的命令行工具。比如，通过Python Package模板来创建Python package项目。（通过Python代码调用Cookiecutter的API可以扩展为自动化创建服务和带有Web UI的服务程序）

### 功能特点

- 跨平台：官方支持Windows、Mac和Linux
- 可以在Python 2.7, 3.4, 3.5, 3.6, and PyPy.环境中正常使用（此处指的是Cookiecutter的运行环境，而不是你项目的运行环境。使用Cookiecutter不需要你懂得编写Python代码）
- 项目模板可以是任何编程语言或标记格式：Python, JavaScript, Ruby, CoffeeScript, RST, Markdown, CSS, HTML等任何你能叫出名字的。你也可以在同一个项目模板中使用多种语言。
- 命令行的简单使用：

```sh
# 从cookiecutter-pypackage.git模板创建项目
# 你将会被提示输入相应的值。
# 然后Cookiecutter将基于你的输入在当前工作目录中创建Python包
$ cookiecutter https://github.com/audreyr/cookiecutter-pypackage
# 为了简洁起见，repos在GitHub上可以只使用“gh”前缀
$ cookiecutter gh:audreyr/cookiecutter-pypackage
```

- 在命令行中使用本地模板：

```sh
# 从本地在当前目录创建项目
# cookiecutter-pypackage/ template
$ cookiecutter cookiecutter-pypackage/
```

- 目录名和文件名可以模板化。例如：`{{cookiecutter.repo_name}}/{{cookiecutter.repo_name}}/{{cookiecutter.repo_name}}.py`
- 支持无限层级的目录嵌套。
- 文件和目录名称100%使用Jinja2完成模板化。
- 只需在·cookiecutter.json·文件中定义模板变量即可。 例如：

```sh
{
    "full_name": "Tacey Wong",
    "email": "xinyong.wang@qq.com",
    "project_name": "Complexity",
    "repo_name": "complexity",
    "project_short_description": "令人耳目一新的简单的静态网站生成器.",
    "release_date": "2019-03-10",
    "year": "2019",
    "version": "0.0.1"
}
```

- 没有你用·--no-input·来禁止，系统会提示你输入：
  - `cookiecutter.json`中键/key对应的值/value。
  - `cookiecutter.json`中的缺省值
  - 提示符按顺序显示
- 跨平台支持`~/.cookiecutterrc`文件：

```sh
default_context:
    full_name: "Tacey Wong"
    email: "xinyong.wang@qq.com"
    github_username: "taceywong"
cookiecutters_dir: "~/.cookiecutters/"
```

- 克隆的Cookiecutter项目模板会缺省放置在`~/.cookiecutters/`目录下，如果特殊指定，会放置在`cookiecutters_dir`对应的目录下：

```sh
# 克隆cookiecutter-pypackage
$ cookiecutter gh:audreyr/cookiecutter-pypackage
# 现在你可以根据名字使用对应的模板
$ cookiecutter cookiecutter-pypackage
```

- 您可以使用你本地模板、远程Git repos
- 默认上下文:指定在生成项目时要用作默认值的键/值对
- 用命令行参数注入额外的上下文：

```bash
$ cookiecutter --no-input gh:msabramo/cookiecutter-supervisor program_name=foobar startsecs=10
```

- 直接访问Cookiecutter API允许注入额外的上下文。
- 预生成和后生成挂钩/hook:在生成项目之前或之后运行的Python或shell脚本。
- 本地项目的路径可以指定为绝对路径或相对路径。
- 项目始终生成到当前目录。

### 可用的Cookiecutter模板

现在在已经有很多开源的Cookiecutter 项目模板可供选择。

#### 特定的Cookiecutter模板

这些模板由cookiecutter官方团队进行维护：

- [cookiecutter-pypackage](https://github.com/audreyr/cookiecutter-pypackage):[@audreyr](https://github.com/audreyr)终极Python包项目模板。
- [cookiecutter-django](https://github.com/pydanny/cookiecutter-django): 一个具有Bootstrap 4、可定制用户应用程序、启动器模板、用户注册、celery设置等功能的Django项目模板。
- [cookiecutter-pytest-plugin](https://github.com/pytest-dev/cookiecutter-pytest-plugin): 编写pytest插件的最小Cookiecutter模板，帮助你编写更好的程序。

#### Cookiecutter模板分类

社区驱动的各类Cookiecutter模板（非Cookiecutter官方维护）

**Python**

- [cookiecutter-pypackage](https://github.com/audreyr/cookiecutter-pypackage): [@audreyr](https://github.com/audreyr)的终极Python包项目模板。
- [cookiecutter-pipproject](https://github.com/wdm0006/cookiecutter-pipproject): pip可安装项目的最小软件包
- [cookiecutter-pypackage-minimal](https://github.com/kragniz/cookiecutter-pypackage-minimal): 一个最小的Python包模板。
- [cookiecutter-lux-python](https://github.com/alexkey/cookiecutter-lux-python): 一个Python样板项目，旨在利用方便的Makefile工具和额外的辅助工具创建Python包。
- [cookiecutter-flask](https://github.com/sloria/cookiecutter-flask) :带有 Bootstrap 3、启动器模板和工作用户注册的Flask模板。
- [cookiecutter-flask-2](https://github.com/wdm0006/cookiecutter-flask): 一个更重的cookiecutter-flask，有更多的样板，包括忘记密码和Heroku集成功能
- [cookiecutter-flask-foundation](https://github.com/JackStouffer/cookiecutter-Flask-Foundation) : 带有缓存、表单、sqlalchemy和单元测试的Flask模板。
- [cookiecutter-flask-minimal](https://github.com/candidtim/cookiecutter-flask-minimal) : 最小但production就绪的Flask项目模板，除了Flask本身之外，没有其他依赖。
- [cookiecutter-flask-skeleton](https://github.com/realpython/cookiecutter-flask-skeleton) : Flask启动项目。
- [cookiecutter-bottle](https://github.com/avelino/cookiecutter-bottle) : 用于快速创建可重复使用的Bottle项目的cookiecutter模板。
- [cookiecutter-openstack](https://github.com/openstack-dev/cookiecutter): OpenStack项目的模板。
- [cookiecutter-docopt](https://github.com/sloria/cookiecutter-docopt): Python命令行脚本的模板，使用[docopt](http://docopt.org/)进行参数解析。
- [cookiecutter-quokka-module](https://github.com/pythonhub/cookiecutter-quokka-module): 用于创建Quokka Flask CMS蓝图模块的模板。
- [cookiecutter-kivy](https://github.com/hackebrot/cookiecutter-kivy):基于kivy python-framework构建的NUI应用程序模板。
- [cookiedozer](https://github.com/hackebrot/cookiedozer): Python Kivy应用程序的模板，已准备好使用Buildozer部署到Android设备。
- [cookiecutter-pylibrary](https://github.com/ionelmc/cookiecutter-pylibrary): 一个旨在快速开始进行良好的测试和打包的复杂模板，（Tox，Pytest，Travis-CI，Coveralls，AppVeyor，Sphinx docs，isort，转换，打包、检查等工作配置）。
- [cookiecutter-pyvanguard](https://github.com/robinandeer/cookiecutter-pyvanguard): 用于尖端Python开发的模板。[invoke](http://docs.pyinvoke.org/en/latest /)、pytest、bumpversion和Python 2/3兼容性。
- [Python-iOS-template](https://github.com/pybee/Python-iOS-template): 用于创建在iOS设备上运行的Python项目模板。
- [Python-Android-template](https://github.com/pybee/Python-Android-template): 用于创建在Android设备上运行的Python项目模板。
- [cookiecutter-tryton](https://bitbucket.org/tryton/cookiecutter-tryton)用于创建基本和外部Tryton模块的模板。
- [cookiecutter-tryton-fulfilio](https://github.com/fulfilio/cookiecutter-tryton): 用于创建tryton模块的模板。
- [cookiecutter-pytest-plugin](https://github.com/pytest-dev/cookiecutter-pytest-plugin): 用于创作[pytest](http://pytest.org/latest/)插件的最小Cookiecutter模板，可帮助您编写更好的程序。
- [cookiecutter-tox-plugin](https://github.com/tox-dev/cookiecutter-tox-plugin): 编写[tox](https://tox.readthedocs.io/) 插件的最小Cookiecutter模板，用于更改或扩展测试自动化的行为。
- [cookiecutter-tapioca](https://github.com/vintasoftware/cookiecutter-tapioca): 用于构建基于Web API包装器（客户端）的[tapioca-wrapper]（https://github.com/vintasoftware/tapioca-wrapper）的模板。
- [cookiecutter-muffin](https://github.com/drgarcia1986/cookiecutter-muffin): 带有Bootstrap 3，启动器模板和工作用户注册的Muffin模板。
- [cookiecutter-octoprint-plugin](https://github.com/OctoPrint/cookiecutter-octoprint-plugin): 用于构建[OctoPrint](https://github.com/foosel/OctoPrint).插件的模板
- [cookiecutter-funkload-friendly](https://github.com/tokibito/cookiecutter-funkload-friendly): [funkload-friendly](https://github.com/tokibito/funkload-friendly) 项目的Cookiecutter模板。
- [cookiecutter-python-app](https://github.com/mdklatt/cookiecutter-python-app): 用于创建具有子命令、日志记录、YAML配置、pytest测试和Virtualenv部署的Python CLI应用程序的模板。
- [morepath-cookiecutter](https://github.com/morepath/morepath-cookiecutter):超强web微框架Morepath的Cookiecutter模板，。
- [Springerle/hovercraft-slides](https://github.com/Springerle/hovercraft-slides): 新[Hovercraft!](https://hovercraft.readthedocs.io/) 展现项目模板（reStructuredText中嵌入`impress.js`幻灯片）
- [cookiecutter-snakemake-analysis-pipeline](https://github.com/xguse/cookiecutter-snakemake-analysis-pipeline): 一种轻松设置[Snakemake](https://bitbucket.org/snakemake/snakemake/wiki/Home)分析管道的方法。
- [cookiecutter-py3tkinter](https://github.com/ivanlyon/cookiecutter-py3tkinter): Python 3 Tkinter应用程序gui的模板。
- [cookiecutter-pyqt5](https://github.com/mandeepbhutani/cookiecutter-pyqt5): 预构建的PyQt4 GUI模板，具有日志记录支持、用于测试和分离ui和工作组件的结构。
- [cookiecutter-pyqt4](https://github.com/aeroaks/cookiecutter-pyqt4): A prebuilt PyQt4 GUI template with a logging support, structure for tests and separation of ui and worker components.
- [cookiecutter-xontrib](https://github.com/laerus/cookiecutter-xontrib): 用于构建xontribs的模板，a.k.a [xonsh](https://github.com/xonsh/xonsh)贡献
- [cookiecutter-conda-python](https://github.com/conda/cookiecutter-conda-python): 用于构建Conda Python包的模板
- [cookiecutter-pypackage-rust-cross-platform-publish](https://github.com/mckaymatt/cookiecutter-pypackage-rust-cross-platform-publish): 包含Rust二进制模块的Python wheel 模板，支持在Windows，OSX和Linux上发布。
- [cookiecutter-telegram-bot](https://github.com/Ars2014/cookiecutter-telegram-bot): 一个带有基于CherryPy webhook的Telegram机器人模板项目。
- [python-project-template](https://github.com/Kwpolska/python-project-template): 一个用于Python项目的模板，具有复杂的发布自动化功能。
- [cookiecutter-anyblok-project](https://github.com/AnyBlok/cookiecutter-anyblok-project): 基于Anyblok的项目模板。
- [cookiecutter-python-cli](https://github.com/xuanluong/cookiecutter-python-cli):用于使用“click”创建Python CLI应用程序的cookiecutter模板

**Python-Django**

- [cookiecutter-django](https://github.com/pydanny/cookiecutter-django): 带有Bootstrap 4的前沿Django项目模板，可自定义的用户应用程序，入门模板，用户注册，celery设置等等。
- [cookiecutter-django-rest](https://github.com/agconti/cookiecutter-django-rest): 用于为移动和Web应用程序创建REST api。
- [cookiecutter-simple-django](https://github.com/marcofucci/cookiecutter-simple-django): 快速创建可重用Django项目的cookiecutter 模板
- [django-docker-bootstrap](https://github.com/legios89/django-docker-bootstrap): Djangodocker开发/生产环境，并集成了 Postgres, NodeJS(React), Nginx, uWSGI.
- [cookiecutter-djangopackage](https://github.com/pydanny/cookiecutter-djangopackage):用于创建可重用的第三方PyPI友好Django应用程序的cookicutter模板。 用教程格式编写文档
- [cookiecutter-django-cms](https://github.com/palazzem/cookiecutter-django-cms): Django CMS的模板，带有简单的Bootstrap 3模板。 它具有快速启动和部署文档。
- [cookiecutter-django-crud](https://github.com/wildfish/cookiecutter-django-crud): 用于围绕工厂模式和测试的模型创建带有CRUD样板的Django应用程序。
- [cookiecutter-django-lborgav](https://github.com/lborgav/cookiecutter-django):Django项目的另一个cookiecutter模板，带有Bootstrap 3和FontAwesome 4
- [cookiecutter-django-paas](https://github.com/pbacterio/cookiecutter-django-paas): 准备在像Heroku，OpenShift等PAAS平台中使用的Django模板
- [cookiecutter-django-rest-framework](https://github.com/jpadilla/cookiecutter-django-rest-framework): 用于创建可重用Django REST框架包的模板。
- [cookiecutter-django-aws-eb](https://github.com/dolphinkiss/cookiecutter-django-aws-eb): 在AWS Elastic Beanstalk上启动并运行Django。
- [cookiecutter-wagtail](https://github.com/torchbox/cookiecutter-wagtail) : 基于[Wagtail](https://github.com/torchbox/wagtail) CMS的网站模板
- [wagtail-cookiecutter-foundation](https://github.com/chrisdev/wagtail-cookiecutter-foundation): Wagtail CMS项目的完整模板，带有 [Zurb Foundation](http://foundation.zurb.com/) 6,ansible资源调配和部署,使用bower进行前端相关性管理，模块化的应用程序，使您的网站启动和运行照片画廊，RSS流等。
- [django-starter](https://github.com/tkjone/django-starter): 包含vagrant和provisioning脚本的Django模板- 灵感来自12factor应用程序和cookiecutter-django。
- [cookiecutter-django-gulp](https://github.com/valerymelou/cookiecutter-django-gulp): 在Django项目中集成前端开发工具的Cookiecutter模板
- [wagtail-starter-kit](https://github.com/tkjone/wagtail-starter-kit): 配有wagtail，django布局，vagrant，配置脚本，前端构建系统等等的cookiecutter模板。
- [cookiecutter-django-herokuapp](https://github.com/dulaccc/cookiecutter-django-herokuapp): 一个为Heroku python3优化的Django 1.7+模板。
- [cookiecutter-simple-django-cn](https://github.com/shenyushun/cookiecutter-simple-django-cn):一个简单的Django中文模板。
- [cc_django_ember_app](https://bitbucket.org/levit_scs/cc_django_ember_app): 用于使用Django和EmberJS创建应用程序
- [cc_project_app_drf](https://bitbucket.org/levit_scs/cc_project_app_drf): 用于基于“project app”项目体系结构创建REST api
- [cc_project_app_full_with_hooks](https://bitbucket.org/levit_scs/cc_project_app_full_with_hooks): 用于基于“project app”项目体系结构创建Django项目
- [cc-automated-drf-template](https://github.com/TAMU-CPT/cc-automated-drf-template): 模板+脚本，根据模型文件作为输入自动创建包含序列化程序，视图，URL和管理文件的Django REST项目。
- [cookiecutter-django-foundation](https://github.com/Parbhat/cookiecutter-django-foundation): [cookiecutter-django](https://github.com/pydanny/cookiecutter-django)分支， 基于 [Zurb Foundation](http://foundation.zurb.com/) 6前端框架。
- [cookiecutter-django-ansible](https://github.com/HackSoftware/cookiecutter-django-ansible): 用于快速启动ansible项目，以便为您的* cookiecutter-django *应用程序提供服务器的框架
- [wemake-django-template](https://github.com/wemake-services/wemake-django-template): 前沿Django模板专注于代码质量和安全性。

**Python-Pyramid**

- [pyramid-cookiecutter-alchemy](https://github.com/Pylons/pyramid-cookiecutter-alchemy):Cookiecutter (项目模板)，使用SQLite创建Pyramid项目，SQLAlchemy用于ORM，URL调度用于路由，Jinja2用于模板。
- [pyramid-cookiecutter-starter](https://github.com/Pylons/pyramid-cookiecutter-starter): 使用URL调度进行路由分配，使用Jinjab 2、Chameleon或Mako模板创建Pyramid入门项目的Cookiecutter (项目模板)。
- [pyramid-cookiecutter-zodb](https://github.com/Pylons/pyramid-cookiecutter-zodb):Cookiecutter（项目模板），用于创建Pyramid项目，使用ZODB进行持久存储，遍历路由，使用Chameleon进行模板化。
- [substanced-cookiecutter](https://github.com/Pylons/substanced-cookiecutter): 用于创建Substance D初始项目的cookiecutter（项目模板）。 Substance D是基于Pyramid实现的。
- [cookiecutter-pyramid-talk-python-starter](https://github.com/mikeckennedy/cookiecutter-pyramid-talk-python-starter): 一个自以为是的Cookiecutter模板，用于创建PyramidWeb应用程序，从开发链的下游开始。这个cookiecutter模板将创建一个新的Pyramid Web应用程序，其中包含电子邮件，sqlalchemy，rollbar以及更多的集成。

**Cookiecutter (meta) **

用于生成Cookiecutter项目模板的元模板。

- [cookiecutter-template](https://github.com/eviweb/cookiecutter-template): 用于创建一个Cookiecutter模板

**Ansible**

- [cookiecutter-molecule](https://github.com/retr0h/cookiecutter-molecule): 按照最佳实践创建Create [Molecule](http://molecule.readthedocs.io/en/v2/)角色, 利用Test-kitchen，Docker和InSpec实现已经实施的测试基础架构。[Molecule](http://molecule.readthedocs.io/en/v2/), Docker and Testinfra.
- [cookiecutter-ansible-role](https://github.com/iknite/cookiecutter-ansible-role): 用于创建ansible角色的模板。 忘记文件创建，专注于操作。
- [cookiecutter-ansible-role-ci](https://github.com/ferrarimarco/cookiecutter-ansible-role): 按照最佳实践创建Ansible角色，利用Test-kitchen，Docker和InSpec实现已经实施的测试基础架构。

**Git**

- [cookiecutter-git](https://github.com/NathanUrwin/cookiecutter-git): Git repo项目模板

**C**

- [bootstrap.c](https://github.com/vincentbernat/bootstrap.c): 用C语言编写的带有autotools的简单项目模板。
- [cookiecutter-avr](https://github.com/solarnz/cookiecutter-avr): avr开发的模板。

**C++**

- [BoilerplatePP](https://github.com/Paspartout/BoilerplatePP): 一个简单的cmake模板，对用C ++编写的项目进行单元测试。
- [cookiecutter-dpf-effect](https://github.com/SpotlightKid/cookiecutter-dpf-effect): DISTRHO插件框架（DPF）的音频插件项目模板
- [cookiecutter-dpf-audiotk](https://github.com/SpotlightKid/cookiecutter-dpf-audiotk): 一个音频插件项目模板，用于发行版插件框架(DPF)和音频工具包(ATK) DSP库
- [cookiecutter-kata-gtest](https://github.com/13coders/cookiecutter-kata-gtest): 使用Google Test框架的C ++测试驱动开发katas模板。
- [cookiecutter-kata-cpputest](https://github.com/13coders/cookiecutter-kata-cpputest): 使用CppUTest框架的C ++测试驱动开发katas的模板。

**C#**

- [cookiecutter-csharp-objc-binding](https://github.com/SandyChapman/cookiecutter-csharp-objc-binding): 生成用于绑定Objective-C静态库的C＃绑定项目的cookircutter模板。

**Common Lisp**

- [cookiecutter-cl-project](https://github.com/svetlyak40wt/cookiecutter-cl-project): 带有bootstrap脚本和Slime集成的Common Lisp项目cookiecutter模板，

**Elm**

- [cookiecutter-elm](https://github.com/m-x-k/cookiecutter-elm.git): 基于Elm带有html样例的cookiecutter模板。

**Golang**

- [cookiecutter-golang](https://github.com/lacion/cookiecutter-golang): 根据最佳实践创建基golang新项目的模板

**Java**

- [cookiecutter-java](https://github.com/m-x-k/cookiecutter-java.git): 使用gradle的基本java应用程序设置的cookiecutter模板。
- [cookiecutter-spring-boot](https://github.com/m-x-k/cookiecutter-spring-boot.git): 标准java spring boot gradle应用程序的Cookiecutter模板。
- [cookiecutter-android](https://github.com/alexfu/cookiecutter-android):基于Gradle的Android项目Cookiecutter模板。

**JS**

- [cookiecutter-es6-boilerplate](https://github.com/agconti/cookiecutter-es6-boilerplate):S6中的前端项目cookiecutter模板
- [cookiecutter-webpack](https://github.com/hzdg/cookiecutter-webpack): Webpack 2项目的模板，包括热重新加载，babel es6模块和反应。
- [cookiecutter-jquery](https://github.com/audreyr/cookiecutter-jquery):一个基于jQuery Boilerplate的jQuery插件项目模板。
- [cookiecutter-jswidget](https://github.com/audreyr/cookiecutter-jswidget): 为多个JS打包系统创建一个通用的前端非jQuery JS小部件的项目模板。
- [cookiecutter-component](https://github.com/audreyr/cookiecutter-component): Component JS 包模板
- [cookiecutter-tampermonkey](https://github.com/christabor/cookiecutter-tampermonkey): TamperMonkey浏览器脚本的模板。
- [cookiecutter-es6-package](https://github.com/ratson/cookiecutter-es6-package): 使用ES6通过babel编写nodejs包的模板。
- [cookiecutter-angular2](https://github.com/matheuspoleza/cookiecutter-angular2): 带有typescript应用程序的模块化angular2模板。
- [CICADA](https://github.com/TAMU-CPT/CICADA): 自动为AngularJS前端创建列表/细节控制器和片段，以连接到DRF后端的模板和脚本。 与[cc-automated-drf-template](https://github.com/TAMU-CPT/cc-automated-drf-template)配合的很好。

**Kotlin**

- [cookiecutter-kotlin-gradle](https://github.com/thomaslee/cookiecutter-kotlin-gradle): 基于Gradle的Kotlin项目简单模板。

**LaTeX/XeTeX**

- [pandoc-talk](https://github.com/larsyencken/pandoc-talk):用于与pandoc和XeTeX进行对接的cookiecutter模板。
- [cookiecutter-latex-article](https://github.com/Kreger51/cookiecutter-latex-article): 针对学术界使用的LaTeX模板
- [cookiecutter-beamer](https://github.com/luismartingil/cookiecutter-beamer): LaTeX Beamer展示模板

**PHP**

- [cookiecutter-mediawiki-extension](https://github.com/JonasGroeger/cookiecutter-mediawiki-extension): MediaWiki扩展的模板。

**Sublime Text**

- [cookiecutter-sublime-text-3-plugin](https://github.com/kkujawinski/cookiecutter-sublime-text-3-plugin): Sublime Text 3 插件模板， 包括自定义设置、命令、键绑定和主菜单
- [sublime-snippet-package-template](https://github.com/agenoria/sublime-snippet-package-template): 包含片段型Sublime Text包的模板。

**Berkshelf-Vagrant**

- [slim-berkshelf-vagrant](https://github.com/mahmoudimus/cookiecutter-slim-berkshelf-vagrant): 与 vagrant/berkshelf cookbooks保持一致的cookbook简单模板

**HTML**

- [cookiecutter-complexity](https://github.com/audreyr/cookiecutter-complexity):一个使用Bootstrap 3的复杂静态站点cookiecutter模板。
- [cookiecutter-reveal.js](https://github.com/keimlink/cookiecutter-reveal.js): reveal.js演示文稿的cookiecutter模板。
- [cookiecutter-tumblr-theme](https://github.com/relekang/cookiecutter-tumblr-theme): 使用GruntJS作为连接工具的Tumblr主题项目的cookiecutter模板。
  **Scala**
- [cookiecutter-scala](https://github.com/Plippe/cookiecutter-scala):带有几个库的简单scala hello world应用程序cookiecutter模板
- [cookiecutter-scala-spark](https://github.com/jpzk/cookiecutter-scala-spark): 用Scala编写的Apache Spark应用程序的cookiecutter模板。
  **6502 Assembly**
- [cookiecutter-atari2600](https://github.com/joeyjoejoejr/cookiecutter-atari2600): Atari2600项目的cookiecutter模板。

**Data Science**

- [widget-cookiecutter](https://github.com/jupyter/widget-cookiecutter): 用于创建自定义Jupyter小部件项目的cookiecutter模板。
- [cookiecutter-data-science](https://github.com/drivendata/cookiecutter-data-science):为在Python中进行和共享数据科学工作的逻辑的、合理标准化的、灵活的项目结构。[此处](http://drivenndata.github.io/cookiegrate-data-science/)提供了的完整文档 。
- [cookiecutter-r-data-analysis](https://github.com/bdcaf/cookiecutter-r-data-analysis): 组织基于R的工作流->doc(通过pandoc)->pdf报告(通过latex)的模板
- [cookiecutter-docker-science](https://github.com/docker-science/cookiecutter-docker-science): Cookiecutter template for data scientists working in Docker containers.为使用Docker容器工作的数据科学家准备的Cookiecutter模板。

**Reproducible Science**

- [cookiecutter-reproducible-science](https://github.com/mkrapp/cookiecutter-reproducible-science): 用于开始一个可重复和透明的科学项目，包括数据，模型，分析和报告的cookiecutter模板 (比如你的科学论文)。与上面数据科学Cookiecutter模板的哲学非常一致

**数据驱动的Journalism**

- [cookiecutter-data-driven-journalism](https://github.com/jastark/cookiecutter-data-driven-journalism): 通过持续组织数据日志项目和一些预先填充的文件来促进数据日志的透明度的Cookiecutter模板(包括 .gitignore, README, AUTHORS)

**持续交付**

- [painless-continuous-delivery](https://github.com/painless-software/painless-continuous-delivery): 用于包含持续交付的软件开发设置cookiecutter模板。支持Python (Django, Flask), 实验性支持 PHP 。
- [cookiecutter-devenv](https://bitbucket.org/greenguavalabs/cookiecutter-devenv.git): 用于将开发和ci环境添加到现有项目的模板。

### 云工具

- [cookiecutter-tf-module](https://github.com/DualSpark/cookiecutter-tf-module): 用于构建一致Terraform模块的Cookiecutter模板。

### Tornado

- [cookiecutter-tornado](https://github.com/hkage/cookiecutter-tornado): 创建Tornado项目的Cookiecutter模板

**其他**

- [cookiecutter-awesome](https://github.com/Pawamoy/cookiecutter-awesome): 创建[awesome]（https://github.com/sindresorhus/awesome）列表的Cookiecutter模板。
- [cookiecutter_dotfile](https://github.com/bdcaf/cookiecutter_dotfile): 由stow管理的dotfiles文件夹的模板。
- [cookiecutter-raml](https://github.com/genzj/cookiecutter-raml): RAML v1.0 API文档的模板。

### 与Cookiecutter类似的项目

- [Paste](http://pythonpaste.org/script/#paster-create) 具有创建骨架项目的创建选项。
- [Diecutter](https://github.com/novagile/diecutter): 一个提供模板和变量的配置文件的API服务。
- [Django](https://docs.djangoproject.com/en/1.9/ref/django-admin/#cmdoption-startapp--template)的tartproject和startapp命令可以使用-template选项。
- [python-packager](https://github.com/fcurella/python-packager):使用自己的模板创建Python包，并提供可配置的选项。
- [Yeoman](https://github.com/yeoman/generator) 有一个受Rails启发的生成器系统，为应用程序提供脚手架。
- [Pyramid](http://docs.pylonsproject.org/projects/pyramid/en/latest/narr/scaffolding.html)的pcreate命令，用于从脚手架模板创建Pyramid 项目。
- [mr.bob](https://github.com/iElectric/mr.bob) 是一个文件系统模板渲染器，用于弃用paster和templer等工具
- [grunt-init](https://github.com/gruntjs/grunt-init) 曾经被内置到Grunt中，现在是一个独立的脚手架工具，可以自动创建项目。
- [scaffolt](https://github.com/paulmillr/scaffolt) 使用Handlebars 的JSON生成器。
- [init-skeleton](https://github.com/paulmillr/init-skeleton) 克隆或复制存储库，执行npm安装和bower安装，并删除.git目录。
- [Cog](https://bitbucket.org/ned/cog) 由Ned Batchelder开发的基于python的代码生成工具包
- [Skaffold](https://github.com/christabor/Skaffold) 基于python和json配置的django / MVC生成器，带有一些附加组件和集成。