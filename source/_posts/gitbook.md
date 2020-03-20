---
title: 使用gitbook搭建github上的书城
date: 2019-09-21
summary: Gitbook可以把的内容写成一个电子书一样的网站，这里介绍gitbook的安装、使用和如何部署到Github上
author: LY
categories:
    - 工具
tags:
    - gitbook
    - github
---



## gitbook的安装和使用

安装Gitbook前应该先安装node.js，安装这里就不赘述了，直接进入node交互界面

```
$ npm install gitbook-cli -g
```


检验是否安装成功

```
$ gitbook -V
```


进入一个空文件夹

```
$ gitbook init
```


然后会创建 `README.md` 和 `SUMMARY.md` 这两个文件， `README.md` 是说明文件， `SUMMARY.md`是书的章节目录，然后输入

```
$ gitbook serve
```

就可以创建gitbook了，默认端口是4000，地址输入[http://localhost:4000](http://localhost:4000) 就可以进入页面，可以用一些markdown编辑器来写，然后就可以在网页上看到实时编辑的内容了。

目录结构如图

![](https://img-blog.csdnimg.cn/20190720225822792.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190720225836562.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)



gitbook的目录结构就是缩进，语法结构一看就懂



gitbook写完后可以输入命令

```
$ gitbook build
```


使用这个命令后在本地会生成一个_book的文件夹，里面是自动生成的html文件，这些htm文件在本地打开是不具备跳转效果的，要部署到网上才可以具备全部效果



## 部署gitbook到github上
根据上面的操作，通过 `$ gitbook build` 把

先在自己的github上创建一个新的仓库，然后把刚刚_book里面的文件上传到github上，这部分的方法是个程序员都应该掌握，这里不再赘述，上传完之后点击setting，拉倒最下面有个github page的，如图

![](https://img-blog.csdnimg.cn/20190720230934856.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)

上面绿色这个地址就是你的网上书籍地址了



如果要给你自己的gitbook设置一个域名，方法如我上一篇[博客](https://www.ly-blog.top/2019/09/05/hello-world/)的自定义域名部分



## gitbook 安装插件

单单使用gitbook来写，写出来的界面不是特别的好看，功能也没有那么多，gitbook是可以添加插件的，比如返回顶部、折叠、添加版权信息、分享等功能