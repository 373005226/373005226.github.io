---
title: docker学习笔记一
author: LY
date: 2020-4-27-18:03
summary: 介绍了Docker 镜像与容器
categories:
    - 运维
tags:
    - docker
password: d548c5b83fa61d8e3bd86ad42a7ffea9b7c86e3f9d8095c1577d3e1270bb9420
---

> 总结慕课网docker课程的学习总结，本篇总结了docker镜像与容器的操作

## docker总体介绍

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427180512.png)

 

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427180559.png)



`docker`整体就是一个**c/s**的架构

![client和server的版本](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427180706.png)

而client和dockerd中间是通过rest api server之间去通信的，dockerd比如是镜像、容器之类的管理 

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427180953.png)

![docker整体架构](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427181012.png)

`client`可以跟`docker_host`在同一台机器上，可以在不同机器上通过网络去连接，因为`docker_host`有一个`restful api`供`client`去使用

`docker_host`主要有两个，一个是镜像一个是容器

最后那个`registry`可以理解为跟`github`一样的公共存储服务器，可以把`images`存储到上面去 

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427181849.png)

`docker`不是一个完全创新的技术，因为底层全部是依赖于`linux`存在于好多年的技术，比如说`namespaces`本来就是`linux`的一个隔离技术，有独立的用户空间、网络空间、进程等，然后通过`control groups`做资源的限制

## docker image

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427183012.png)

`linux`分为内核空间和用户控件

最底层那个`linux kernel`就是内核空间

用户空间就是在`bootfs`之上做的各种linux的发行版：`ununtu、centos、debian`等

`docker`里面的`image`是分层的，每一层都可以添加和删除文件，并且可以组成为一个新的`image`

这些发行版质保函`base image`，不包含`linux kernel`，也就是可以共享主机的`linux kernel`

然后就可以在`base image`上建立新的`image`，比如安装`apach、Nginx`等，然后就会产生新的一层`image`



这里看一看image的centos大小

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427184239.png)

为什么会这么小呢， 一般的发行版都要十几个G，这里因为这里的base image只包含rootfs



### 方式一：build image

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427201310.png)

第一行是一个基础的image，也就是base image

第二行是基本的标识，标识由谁制造

第三行是命令

第四行是暴露的端口

第五行是起点程序的入口，即图中的启动redis的入口

右图中的五步就是对应着左边的每一行

### 方式二：pull image

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427201715.png)

这个其实就跟github很像了，拉取别人的镜像

## docker hub

`docker hub`是`docker`的一个相当于`github`的一个仓库

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427202912.png)

具体分为两类，一类是官方的，一类是第三方的



直接拉去镜像的话默认是最新版本

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427203030.png)

如果是要指定版本的话比如说`ubuntu:14.04`

如果是第三方的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427203314.png)

第三方的会有个**用户名+/image**的名字

## 创建一个自己的image

可以先拉去一个hello-world的image

```
docker pull hello-world
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427210730.png)

然后执行run命令

```
docker run hello-world
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427210840.png)

 这里先安装好gcc和vim的环境

```shell
 yum -y install gcc gcc-c++ kernel-devel
 yum install -y vim
 yum install glibc-static
 yum install glibc
```

然后编辑一个C语言的hello world

```c
#include<stdio.h>

int main()
{
        printf("hello world\n");
}
```

然后编译成可执行的c文件

```shell
gcc -static hello.c -o hello
```

读取试一试

```shell
./hello
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427212050.png)

然后创建一个Dockerfile文件

因为是在原本的base image之上安装一些新的软件，来形成一个新的image

因为这里要写的就是一个base image，所以这里不能从一个base image去安装，所以第一行要写成`From scratch`

```
From scratch
ADD hello /
CMD ["/hello"]
```

这里的`ADD`就是添加`hello`这个文件

`CMD`就是执行这个可执行文件的命令

然后执行`build`命令

```
docker build -t 373005226/hello-world .
```

前面的是`docker hub`的`id`，后面是名字 ， `.`是指当前目录

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427213227.png)

这里还可以查看`image`的分层

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427213454.png)

第一层是添加了一个文件，这里要注意，本来第一层是`From scratch`的，但是这里显示的是ADD file，`From scratch`的意思是没有base任何的image，也就是不算是一层，所以写了三行代码只有两层

第二层是运行了一个命令

这里就可以执行命令了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200427213744.png)

成功了也就意味着`hello world` 是可以变成一个`container`去执行的

`docker image`就存一个可执行的文件，运行的时候其实是共享了宿主机`centos7`的`kernel`，在`kernel`之上执行了一个hello world，虽然十分小，但是这个是docker的基础架构

## docker container

### 查看容器

**总结：docker container ls是查看运行中的容器，docker container ls -a是查看所有的容器**

`container`是通过`image`去创建的，`image`本身是只读的，但是如果是要去运行程序的话，就需要一个可写的空间

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428094829.png)

就比如，`image`是一个类，`container`是类的实例

`image`负责app的存储和分发的，`container`负责运行app

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428101637.png)

这里没有显示`hello world`是因为这个不是一个常驻内存的进程，打印完`hello world`就退出了

这条命令就可以显示所有的容器，包括运行的和退出的

```shell
docker container ls -a
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428101754.png)

`command`就是执行`hello`可执行文件的命令，即相当于`./hello`

这里执行以下常驻进程的容器

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428102127.png)

这里会报错，因为找不到`latest`这个版本，因为我只有`14.04`版本的，以下这样子才对

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428102258.png)

这里看一下`container ls`其实还是没有的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428102352.png)

因为这里的`command`是`/bin/bash`，这个命令不会常驻内存

这里如果是要交互式的运行呢

```
docker run -it ubuntu:14.04
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428102801.png)

这里会看到已经进入了一个`linux`环境

这里再打开一个`ssh`连接界面，然后再去看看`container ls`，这里用另一个`ssh`就可以看到有容器正在运行

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428103039.png)

什么是交互式运行呢，来看一下`help`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428105215.png)

即进入一个交互式的标准输出里面

`container`其实就是在`image`上多了一个可写的层

这里对容器内的`linux`系统进行`yum`一些东西或者创建文件，再`exit`退出

然后就可以看到

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428112535.png)

`container ls`肯定是没有东西的，因为只显示运行中的容器，`container ls -a`就可以看到刚刚编辑的容器

当然如果把修改了的容器删掉了，那么数据就不会保存

### 演示启动、进入容器

比如我刚刚进入了一个容器中，并且下载了vim（已经下载好了的）

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200429162838.png)

我先进入第一个容器，因为已经存在这个容器了，我直接启动再进入就好了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200429163100.png)

```
docker ps 									#确定容器没有开启
docker ps -a 								#查看容器的id
docker start be22658b1be3					#启动需要的容器
docker exec -it be22658b1be3 /bin/bash		#进入这个启动了的容器，或者使用docker attach be22658b1be3也行
```



## docker常用简写

### id简写

如果是要删除`container`的话，可以选择删除id或者是选择名字

如果是选择id来删除的话，那么可以直接选择id就行

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428114316.png)

当然也可以不写全，直接选择能区分开来的部分也行，比如下面图中的2bd，是对于当前的数字是比较独特的，就可以直接简写

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428114515.png)

`docker`有很多简写的

比如`docker container ls -a`，可以写成`docker ps -a`

效果都是一样的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428114925.png)

比如说`rm`删除容器，删除命令为`docker contaniner rm +标识`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428122331.png)

### 查看容器简写

`docker image ls`可以简写为`docker images`

![image-20200428122449814](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428122450.png)

### 删除简写

`docker image rm +标识`  是删除镜像，`docker rm`默认是删除容器，`docker rmi`是删除镜像

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428122728.png)

### 删除多个容器

假如`run`多个镜像

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428123014.png)

就可以看到启动了很多个`container`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428123106.png)

这个时候如果要清除掉所有的话，可能就要一个个`rm`去清理

这时候可以一键删除所有的容器

先打印出第一列的内容

```
docker container ls -aq
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428144115.png)

这个其实相当于

```
docker container ls -a | awk {'print$1'}
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428144155.png)

除了这个表头之外没有其他的不同

此时就可以通过显示的表头去一键删除

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428144404.png)

此时已经全部删除了，如果有更复杂的情况

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428144521.png)

容器有退出的有启动的，此时就可以把退出的容器筛选出来再删掉

先列举退出状态的容器

```
docker container ls -f "status=exited"
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428144722.png)

然后列举出来id

```
docker container ls -f "status=exited" -q
```

最后再按照之前的方法

```
docker rm $(docker container ls -f "status=exited" -q)
```

然后就可以一键删除掉了

## 构建自己的镜像

### 使用container建立镜像

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428145320.png)

这里要介绍在基于`image`之上安装了某些软件来发布自己的镜像

也就是在基于某个`image`创建一个`container`，然后再`container`里面做一些变化（安装软件等），然后把`container` `commit`成一个新的`image`

这个过程命令为

```
docker container commit
```

因为只有container可以commit，所以也可以简写为

```
docker commit
```

这里先进入一个`image`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428145858.png)

然后再里面做一些变化

里面一开始就是没有`vim`命令的，因为我把之前的`container`删掉了

安装完之后退出查看容器，可以看到我刚刚是退出状态的`container`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428150044.png)

这里就是想把这个`container commit`成一个`image`，这个`image`呢是基于干净的`centos7`之外还安装好了`vim`

这里可以查看就接受的参数

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428150244.png)

`CONTAINER`就是`ls -a`最后那列的`name` ，然后后面接的是`docker`的`id+/`新的`image`的名字

所以全部命令为

```
docker commit +这个容器的名字 +需要生成的镜像名字
```

比如下面的`lucid_franklink`就是容器名字，后面的`373005226/centos-vim`是我需要生成镜像的名字

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428150607.png)

生成后会给出一个唯一的sha256值

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428150657.png)

`tag`如果不指定，默认就是`latest`的

新的那个镜像跟`centos`会共享很多的层，因为我的新镜像就是基于`centos`去开发的，可在下图看出基于`centos`之上安装了`vim`的镜像多了一层119MB的层

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428150902.png)

可以看到有三层都是一样的

```
5e35e350aded        5 months ago        /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B                  
<missing>           5 months ago        /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B                  
<missing>           5 months ago        /bin/sh -c #(nop) ADD file:45a381049c52b5664…   203MB 
```

因为我只安装了`vim`工具，所以只添加了一层

这个的话其实是不建立这么做的，因为别人拿到我的`image`，就不知道我的`image`是怎么产生的，如果有不安全的东西放到`image`里面，就麻烦了。

当然最推荐的方式还是通过`dockerfile`去创建，通过`dockerfile` 去`build`成一个新的`image`，别人看`dockerfile`就可以知道我执行了什么样的命令，而不用担心我在里面安装了什么的病毒

### 推荐方法：使用dockerfile建立镜像

先创建一个`docker-centos-vim`目录，然后进去

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428154220.png)

这里要思考一下`docker image`是只读的，在只读的`image`里面怎么去安装一个`vim`呢

这里先介绍命令

建立镜像为

```
docker image build
```

因为只有image可以build，所以也可以简写为

```
docker build
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428145653.png)

也就是把`dockerfile build`成一个新的`image`

全部命令为

```
docker build -t +镜像的名字 .
```

最后面的那个`.`意思是说`dockerfile`是在当前目录下的

如上`dockerfile`写好之后，再`build`一下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428155117.png)

这里可以看到图中的这一层

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428155055.png)

其实这个是在`build`的过程中生成了一个新的临时的`container`，这个就是临时`container`的`id`，然后再这个`container`里面去安装`vim`

 最后会显示这几行代码

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428154954.png)

也就是说最后还会remove删掉这个临时`container`的`id`

虽然说两个大致是一样的，但是还是建议去使用`dockerfile`去生成`image`

## Dockerfile语法

dockerfile具体看[官网文档](https://docs.docker.com/engine/reference/builder/)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428161344.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428161451.png)

因为这个安全，其次就是label

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428161617.png)

即标注image的作者、版本、描述

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428161655.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428161752.png)

需要注意的是，每一次run，对于image都会生成新的一层临时的container，所以对于image来说

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428161939.png)

worddir其实就是设定工作目录的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428162159.png)

workdir /test就是在根目录找到test文件夹，如果没有呢就创建一个，

这里其实就是进入test目录，如果没有test就创建一个然后进去，然后进入demo目录，如果没有就创建一个再进去

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428163026.png)

绝对目录的话绝对是更清晰一些，不容易出错

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428163109.png)

`ADD`这个是把本地的文件添加到`image`里面，也就是之前的`hello`这个可执行文件添加进去

然后再`RUN`执行

`ADD`和`COPY`的区别就是`ADD`文件到目录后可以解压，`COPY`是单纯的复制这个文件

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428163524.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428163632.png)

`ENV`就是设置常量

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428163711.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428163821.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428163841.png)



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428170743.png)

`run`每执行一次都会创建一次`image layer`新的一层

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428170939.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428172629.png)

这两个`dockerfile`就可以看出区别

在第一个中是可以直接打印`hello Docker`

在第二个中是打印出`hello $name`

因为在第一个中是可以`shell`去执行命令，也就是在`bash`里面执行，所可以可以识别`$name`是一个变量

但是如果是`exec`格式呢，执行的是`echo`的这个命令，而不是在`shell`里面执行`echo`，所以这里是需要改变一下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428173058.png)

这里就要把后面的命令都改成一个命令去执行才可以，如果是分开的话是为空的



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428173413.png)

如果是第一个`docker run [image]`会打印出`hello Docker`

如果是第二个，`docker run`指定了其他的命令，那么就不会执行`CMD`

即如果指定了命令，那么就不会执行`CMD`，此外`CMD`还可以拿来接收参数，一般的话是跟`ENTRYPOINT`一起使用

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428173740.png)

这个的话一般是把应用程序以服务的形式去运行，一般的话会拿来做后台的进程，比如说要启动数据库的服务

比如说最后一个是`MongoDB`的`dockerfile`的一部分

就是`copy`一个`shell`脚本到本地目录。然后把这个`entrypoint`作为启动的脚本来去执行

`entrypoint`在一些官方的脚本启动比较多，可以当成一个服务器去运行这个容器，而不是像`cmd`一样去执行

##   docker image的发布

### 上传到docker hub

上面都讲了如何做一个镜像，这里就讲如何去发布

`docker hub`就是跟`github`一样的东西，可以把自己的镜像放到上面

这里我还没有创建自己的`image`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428180148.png)

这里先要`docker login`进去

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428180733.png)

命令也很简单

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428180842.png)

`docker push`和`docker image push`是一样的，因为只能发布`image`

这里跟着的参数是`docker push +image`的名字**+**版本

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428181028.png)

然后在`push`完毕的时候会生成一个id

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428181102.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428181115.png)

这里就会显示我刚刚的`image`

但是仅仅是这样放上去的话，没有说明别人就不知道是怎么产生的，这样还不如直接放上一个`dockerfile`上去

`dockerfile`才是推荐的方式

点击到这个界面

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428182906.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428183223.png)

这里可以关联`github`仓库

只要`github`仓库有个`dockerfile`，`docker hub`就可以把`dockerfile`自动`build`成`image`到`docker hub`仓库中

### 上传到私有hub

当然如果是需要公司内部的`docker`仓库的话

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428202643.png)

可以使用`docker`推荐的`registry`去搭建一个

只需要按照这上面的命令搭建即可

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428202905.png)

两个服务器之间要可以通信，在我的服务器上已经搭建好了registry这个环境后

```shell
telnet *.*.*.* 5000
```

来查看是否可以正常访问

出现

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428203409.png)

即访问成功，这样就可以往私有的`hub` 来`push image`了

这里先把原来的`image`删掉，再去重新`build`一个

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428203704.png)

如图删掉我创建的`hello-world`，然后再把`docker hub`的`id`改成服务器的IP地址和端口

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428203850.png)



直接`push`上去会报错的，如下图

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428204041.png)

因为这个是对于`docker`来说是不可信任的，要去修改一个文件

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428204024.png)

要在里面添加一行代码

```
"insecure-registries":["39.106.12.6:5000"]
```

如下图所示

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428204254.png)

然后还需要配置一个文件

```
sudo vim /lib/systemd/system/docker.service
```

然后再去添加这一行代码，需要读取这个环境的文件，然后配置才能被加载

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428204456.png)

然后需要重启`docker`

```
sudo service docker restart
```

```
sudo systemctl daemon-reload
```

然后就可以`push`成功了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428204831.png)

然后这里看到私有hub的服务器上

命令查看docker registry api，这里可以看到命令是`GET /v2/_catalog`，即在IP地址后添加/v2/_catalog`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428205035.png)



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428205130.png)

当然`pull`的原理也一样

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200428205339.png)

直接把原来的`images`删掉，然后直接按照IP地址:5000端口+项目名字回来即可

## 对进行中的容器进行操作

之前都是对退出的容器进行操作，这里介绍正在运行的容器操作

### 进入运行中的容器

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200429110050.png)

```
docker exec -it +容器的id +需要执行的命令
```

### 停止容器

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200429110409.png)

可以直接简写

```
docker stop +id或者是名字
```

### run指定名字

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200429170938.png)

这个时候就可以stop指定名字了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200429110936.png)

然后可以指定名字来启动了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200429111015.png)

### docker inspect查看容器详情

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200429111209.png)

可以看到这个容器的详细信息(命令、网络配置、创建时间等)

### logs查看容器日志

就是查看容器的日志

```
docker logs +容器的id
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200429111358.png)

## CMD与ENTRYPOINT共存

这里拿的是stress这个linux环境下的压力测试来演示

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200429112212.png)

 

如果定义一个十分大的内存的话，最大也不能超过宿主机的内存

这里先写一个`Dockerfile`来查看

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200429113021.png)

这里的`CMD`用来接收参数，因为`vm` 设定内存等参数需要重复的填写，但是又是变动的，不能用`ENV`来默认

此时直接运行的话就会出现这种情况

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200429115038.png)

因为没有加上参数，只会显示命令

这个时候就可以加上参数

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200429115307.png)

这个原理就是后面的参数都是放在`CMD`里面的，这个就是`ENTRYPOINT+CMD`的方式去结合的，这个是十分的流行的

`CMD`可以放上默认的参数，也可以自己的指定的参数去运行

