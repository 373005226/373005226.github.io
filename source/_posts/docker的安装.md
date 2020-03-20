---
title: docker的安装
author: LY
date: 2019-10-22
summary: 简洁的介绍docker的安装、部分配置
categories:
    - 工具
tags:
    - docker
---

##  docker是什么

> 参考文章地址： https://yeasy.gitbooks.io/docker_practice/introduction/what.html 
>
> docker下载地址：https://hub.docker.com/?overlay=onboarding

> 本篇博客是使用windows系统下的安装的，linux安装更为简单

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`docker`相当于一个容器，里面封装了你开发的环境和应用。打个比方来说，我写了一个`python`项目，但是如果我直接把这个项目放到`github`上，很多人可能下载下来部署十分的麻烦，甚至可能出现运行不了的情况，最好的方法就是使用我开发的环境去部署这个项目，这个方法是直接而且是最方便的，而`docker`就帮助了我们实现了这个步骤。我直接打包我的项目环境和应用，直接给你们，你们就可以直接去运行，而且`docker`是一个沙箱环境，每个不同的应用之间不会有任何的影响。这样就不会出现在我的电脑上可以运行怎么在你的电脑上就出问题的场景了。

&emsp;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;旧版本的`docker`是基于`linux`系统的[LXC](https://linuxcontainers.org/lxc/introduction/)来实现的，在1.1以后使用自行开发的 [libcontainer](https://github.com/docker/libcontainer) 开发的。`docker`虽然跟虚拟机的使用目的很相似，但是跟虚拟机有很大的不同。`docker`和虚拟机本质上都是在虚拟环境上运行，但是`docker`对比与`虚拟机`更为轻量。`docker`是一种操作系统级别的虚拟化技术，能更快的启动、占用更少的内存去运行，你可能要花五分钟去运行一个`虚拟机`，如果使用的是`docker`估计也就几秒就事，其他更多的优势随便百度搜索一下都有一大堆详细的说明，这里我就不再更多的赘述。

&emsp;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最重要的一点就是不会再出现这个代码在我的环境没问题，怎么到你的电脑就怎样怎样的，使用`docker`可以解决大部分开发的问题，总之....总之用他就对了，而且docker社区很丰富，就拿阿里云来说，很多人都把自己部署好的环境放到云平台上供人下载。

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191025115438.png)



## docker的安装

如果你的电脑是`win10`，那我不建议去下载`toolbox`版，因为`win10`自带了`Hyper-v`的虚拟化，而`toolbox`版本是通过`VirtualBox`来建立`linux`虚拟机，`docker`下载地址为[https://hub.docker.com/?overlay=onboarding](https://hub.docker.com/?overlay=onboarding)

我试过很多个下载链接地址，但是下载后要么是不能更改下载路径，要么就是一直显示登陆中，唯有这个是最正确的，所以推荐使用这个下载

下载之后的界面应该是这样的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191101160002.png)

接着最好就是修改存储的路径，不然到时候拉取镜像的时候C盘会炸

## 修改默认镜像存储路径

直接从docker setting里面修改路径即可

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191101160618.png)

直接修改disk image location就可以修改镜像存储路径

如果这个方法还是不管用，那么看以下的三个博客，有很详细的解释

>  [https://cloud.tencent.com/developer/article/1147265]( https://cloud.tencent.com/developer/article/1147265 ) 
>
>  [https://www.jianshu.com/p/f0738420a3ee]( https://www.jianshu.com/p/f0738420a3ee ) 
>
>  [https://www.cnblogs.com/brock0624/p/9792203.html]( https://www.cnblogs.com/brock0624/p/9792203.html ) 

## 镜像加速

docker默认的镜像拉取是从国外的网站上拉的，所以会很慢，我们可以使用国内的镜像去拉取，可以从阿里云去申请一个独有的镜像加速方式： [https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors) 

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191101161320.png)

然后把镜像地址填写到这个框框里面就行了

