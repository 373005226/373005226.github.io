---
title: docker学习笔记四
author: LY
date: 2020-5-06-15:37
summary: 介绍了Docker Compose
categories:
    - 运维
tags:
    - docker
password: d548c5b83fa61d8e3bd86ad42a7ffea9b7c86e3f9d8095c1577d3e1270bb9420
---

> 总结慕课网docker课程的学习总结，本篇总结了docker compose的理念和操作

## compose是什么

如果一个app，里面有多个容器组成，比如Django项目，就要部署mysql、redis等，一个docker image就要创建多个container，每个单独的配置还是不一样的，这样弄的话会使得docker变成十分的麻烦，docker compose就是一个批处理的工具，compose可以在一个文件里面定义一个文件，这个文件定义了这个app的所需要的container，然后再用一条命令定义所有容器的启动

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506154306.png)

这个yml文件有个默认的名字：docker-compose.yml，文件里面定义了三个重要的概念

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506154341.png)



docker compose是根据docker的更新而更新的，至我写这个博客的时间，已经有三个主要的版本了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506154735.png)

version2和version3都可以用，version3是主流使用的，version1已经被淘汰了

不同的docker compose版本对应着不同的docker版本，不同版本之间会有差异

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506154854.png)

version的版本只能用于单机（所有的容器都只能在同一台主机上面），version3可以用于多机（默认都是把所有的容器部署在同一台机器上面，而且可以把一个app的不同容器部署在不同的docker host上面，会用到swam）

services服务的概念如下：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506155337.png)

第一条的意思是docker contaer可以通过dockerfile build来创建，也可以通过从docker hub 拉去下来的容器run来创建

第二条的意思是在docker run可以指定的参数，都可以在docker compose体现出来（比如说定义volumes、networks等的参数，然后去引用）

比如下图所示：

![第一种](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506155728.png)

这一种是从docker hub去拉取的

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506155820.png" style="zoom:67%;" />

这一种呢的docker image不是从docker hub去拉取的，是从本地build出来的



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506160332.png)



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506160401.png)

这里把上一章的WordPress使用compose去运行的话

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506183210.png)

这里定义了两个services，一个叫WordPress，一个叫mysql，里面的networks指定的my-bridge就是引用最下面的

这个就是一个完整的docker compose

## 使用docker compose文件

首先是要安装docker compose，这个是docker的一个工具，如果是docker for mac或者是docker for window，默认是安装好的，而对于linux来说，安装docker都是单独安装的

![linux安装compose步骤](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506205024.png)

只需要如上图的两步即可把compose安装完毕

如图即是安装成功

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506205146.png)

使用`docker-compose up`默认寻找的就是`docker-compose.yml`

这个文件可以直接找官网的案例文件

![docker-compose官网案例文件](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507105103.png)

```
docker-compose up
```

如果不是这个名字的话也可以通过`-f`参数来指定文件

```
docker-compose -f docker-compose.yml up
```

如下所示即是用compose来启动WordPress和mysql容器

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506205713.png)

其余的命令在docker也可以查询到

主要的命令介绍如下

```
#停止并且删除掉compose所有的容器
docker-compose down
```

```
#compose在后台执行，不打印logs
docker-compose ip -d
```

```
#查询compose所有的image
docker-compose images
```

```
#compsoe的exec
docker-compose exec +compose的应用名字+bash
#比如docker-compose exec mysql bash
```

```
#docker-compose build
docker-compose up其实是如果本地没有image，那么就是先build再up，就相当于docker-compose build ->docker-compose up
如果是services的Dockerfile有了变化，那么如过要获取最新的images就要先build再up
```

## **如果是使用build的compose文件**

假如是不是直接使用镜像，而是通过build来创建一个镜像的话

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507115549.png)

`context：`就是`dockerfile`的位置，`dockerfile：`后面制定的是`dockerfile`的名字

## docker-compose的拓展

![文件结构](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507180503.png)

![app.py](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507180514.png)

![docker-compose.yml](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507180529.png)

![Dockerfile](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507180542.png)

此外，还可以对docker-compose进行拓展

先要把docke-compose里面的端口删掉

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507154443.png)

然后执行

```
docker-compose up --scale web=3 -d
```

 执行后会有三个启动的web容器，每个容器都是指向同一个redis数据库

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507154623.png)

本质上就类似于负载均衡，如下图所示

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507161945.png)

也就是可以把外界的访问量平均的分到多个web服务器上去

##  负载均衡模拟

![文件结构](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507162943.png)

![app.py](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507163001.png)

![docker-compose.yml](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507163015.png)

![Dockerfile](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507163033.png)

 然后把整个文件夹上传到服务器

执行命令

```
docker-compose up -d
```

然后输入服务器的ip地址+`:8000`查看内容

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507175140.png)

然后再创建三个web

```
docker-compose up --scale web=3 -d
```

![第一次输入地址](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507175255.png)

![第二次输入地址](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507175314.png)

![第三次输入地址](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507175334.png)

![第四次输入地址](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507175351.png)

![第五次输入地址](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507175405.png)

![第六次输入地址](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507175408.png)

这里把三个web测试了两遍，可以看到每三次访问的主机地址都是不同的，可以看到是进行分流操作

而且通过`scale`参数可以快速的改变`web`服务的数量，那么就可以很轻松的在高峰访问时间和平常访问时间的资源之间的切换，可以很方便的节省服务器的资源

但是如上都是在单机上运行的，负载均衡当然不可能只在单机上运行

##  实战：投票应用

整体架构如下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507181036.png)

voting app是投票的情况，restults app是实时显示投票的情况，这两个都是web应用

而voting app是一个访问量比较大的应用，所以用的是redis的缓存，java worker是取得redis的结果，并且把他写到数据库里面，results app是直接从数据库里面去获取最新的实时投票的结果的

![投票项目文件结构](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507181718.png)

restult-app是一个angular项目，voting是一个python项目，worker是一个java项目

里面包含了三个语言的项目，每个子项目文件夹里面都包括了一个Dockerfile

再看下docker-compose.yml文件

![docker-compose.yml](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507181839.png)

由代码看出是有5个services，voting和result两个services是要对外访问的，所以都有个front-tier，同时又要与worker、redis、db进行通信，所以又共用一个back-tier

然后直接docker-compose up进行部署即可

voting会把本地的80映射到5000，result会把80映射到5001，所以通过5000端口是投票，5001是看结果

哪怕是本地没有环境，那么直接用docker-compose也可以一次性启动多个service

docker-compose是一个本地开发的工具，不是生产环境的工具，只是为了本地开发，为了方便看部署的结果