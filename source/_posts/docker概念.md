---
title: docker基本概念（翻译）
author: LY
date: 2019-11-01
summary: 详细的介绍docker的镜像、容器、仓库三个基本概念
categories:
    - 工具
tags:
    - docker
---

> 参考博客：
>
> [文档地址]( https://docker_practice.gitee.io/zh-cn/ ) 
>
> [大神博客]( https://juejin.im/post/5b260ec26fb9a00e8e4b031a ) 
>
> [通俗的讲解Docker]( https://www.cnblogs.com/JulyShine/p/10415758.html ) 
>
>  [很通俗的解释](https://www.cnblogs.com/misswangxing/p/10669444.html ) 
>
> https://blog.csdn.net/qq_29301417/article/details/75640893 
>
> https://blog.csdn.net/mythinker2/article/details/87701222 
>
> https://blog.csdn.net/qq_36330643/article/details/77160101 
>
> https://www.cnblogs.com/yangmingxianshen/p/10129262.html 
>
> https://www.jianshu.com/p/7fd1a9655213 

## 镜像(image)

### Docker镜像

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; Docker镜像就像是虚拟机里面的镜像，是一个**只读**的模板，我们都知道，操作系统分为内核和用户空间。对于 Linux 而言，内核启动后，会挂载 `root` 文件系统为其提供用户空间支持。而 Docker 镜像（Image），就相当于是一个 `root` 文件系统。比如官方镜像 `ubuntu:18.04` 就包含了完整的一套 Ubuntu 18.04 最小系统的 `root` 文件系统。Docker 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变 

### 分层存储

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;因为 镜像包含了操作系统完整的 `root` 文件系统，其体积还是很巨大的，所以引入了 `Unios FS` 技术，将其设计为分层存储的架构。所以镜像不是那种 `ISO` 打包文件，镜像是一个虚拟的概念，它是由一组文件系统构成，或者说多层文件系统联合构成。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;镜像在构建时，是一层层构建，上一层是下一层的基础，每一层构建结束就不会在被修改，后一层任何修改只会作用在当前层。例如删除上一层文件，不会真的删除该文件，而是会标记删除然后隐藏起来，最终运行容器的时候不会被看见，但是会一直跟随镜像。因此构建镜像时需要小心，不要添加不需要的东西，任何额外的东西都要在本层构建完之前清除。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;分层存储使镜像的定制和复用变得容易，可以用之前做好的镜像作为基础层，然后添加新的层，定制自己所需要的东西，构建新的镜像。

### 创建镜像

创建镜像有两种方法：

（1） 基于已有镜像的容器创建。主要是利用docker commit命令。

（2） 基于dockerfile创建。

首先按照dockerfile的格式，编写好dockerfile文件，之后通过docker build命令来创建镜像。docker build会读取制定的dockerfile，由docker服务器来创建镜像


## 容器

###  　概念

镜像（Image）和容器（Conatiner）的关系就像是面向对象的类和实例一样，镜像是静态的定义，容器是镜像运行的实体，容器可以被创建，启动，停止，删除，暂停等。容器的实质是进程，但是又和宿主的进程不同，容器有自己的独立命名空间，因此容器拥有自己 root 文件系统，自己的网络配置，自己的进程空间，甚至自己的用户 ID 空间。容器内的进程是运行在一个隔离的环境里，使用起来，就好像是在一个独立于宿主的系统下操作一样。所以好多初学者都会把容器和虚拟机搞混。容器同样和镜像一样使用分层存储，以镜像为基础层，在上面创建了一个容器存储层，是为了容器运行时读写准备的。注意，在容器被删除后，容器存储层保存的任何信息都不会保存，它和容器的生命周期一样，所以任何需要保存的数据 Docker 建议保存在数据卷（Volumes）或者宿主机目录中，这样数据不会随着容器的消失而消失。

### 总结

总的来说：

 可以把容器看做是一个简易版的 Linux 环境（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。 

 镜像自身是自读的，容器从镜像启动的时候，docker会在镜像的最上层创建一个可写文件层，镜像本身保持不变 

## 仓库

　　镜像构建完成后，可以很容易的在当前宿主机上运行，但是，如果需要在其它服务器上使用这个镜像，我们就需要一个集中的存储、分发镜像的服务，Docker Registry 就是这样的服务。

一个 Docker Registry 可以有多个 仓库（Repository），每个仓库可以含多个标签（Tag），每个标签都是一个镜像。

　　最初我没有搞懂 Docker Registry 和 仓库（Repository），简单来说就是 Docker Registry 是注册服务器，用于管理镜像仓库，起到服务器的作用，仓库（Repository）就是存储镜像，起到存储镜像的作用。默认的 Docker Registry 就是 Docker Hub，通过配置也可以选择华为云、阿里云、腾讯云等服务商，他们都拥有很多高质量的镜像。

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191102165834.png) 

这个如果你使用过git和github就很容易理解Docker的仓库概念。Docker 仓库的概念跟Git 类似，注册服务器可以理解为 GitHub 这样的托管服务。

Docker 仓库是用来包含镜像的位置，Docker提供一个注册服务器（Register）来保存多个仓库，每个仓库又可以包含多个具备不同tag的镜像。Docker运行中使用的默认仓库是 Docker Hub 公共仓库。

仓库支持的操作类似git，当用户创建了自己的镜像之后就可以使用 push 命令将它上传到公有或者私有仓库，这样下次在另外一台机器上使用这个镜像时候，只需要从仓库上 pull 下来就可以了。.