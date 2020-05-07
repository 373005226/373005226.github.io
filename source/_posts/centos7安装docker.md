---
title: centos7安装docker
date: 2020-4-22
summary: 简洁的介绍docker的安装、部分配置
categories:
    - 工具
tags:
    - docker
---

> 本文引用于csdn的博客：
>
> https://blog.csdn.net/u014069688/article/details/100532774 (docker的安装)
>
> https://www.cnblogs.com/wchw2008/p/10161952.html (docker安装错误解决办法)
>
> https://blog.csdn.net/Qevery678/article/details/102811576  (docker安装错误解决办法)
>
> 这篇博客的意义在于把centos7安装docker的常见错误和安装过程整合在一起

## docker的安装

### 官网的安装方法

> 官网地址：https://docs.docker.com/engine/install/centos/

第一步：

```
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

第二步：

```
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

第三步：

```
sudo yum install docker-ce docker-ce-cli containerd.io
```

第四步：

```
sudo systemctl start docker
```

### 一般的安装方法

......这里省略docker是什么

1. root账户登录，查看内核版本如下

```shell
[root@localhost ~]# uname -a
Linux localhost.localdomain 3.10.0-957.el7.x86_64 #1 SMP Thu Nov 8 23:39:32 UTC 2018 
x86_64 x86_64 x86_64 GNU/Linux
```

2. 把yum包更新到最新

```shell
[root@localhost ~]# yum update
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: centos.ustc.edu.cn
 * extras: mirrors.aliyun.com
 * updates: mirrors.cn99.com
base                                                                                                  | 3.6 kB  00:00:00     
extras                                                                                                | 3.4 kB  00:00:00     
updates                                                                                               | 3.4 kB  00:00:00     
正在解决依赖关系
--> 正在检查事务
---> 软件包 NetworkManager.x86_64.1.1.12.0-6.el7 将被 升级
---> 软件包 NetworkManager.x86_64.1.1.12.0-10.el7_6 将被 更新
```

（期间要选择确认，输入 y 即可）

3. 安装需要的软件包， yum-util 提供yum-config-manager功能，另外两个是devicemapper驱动依赖的

```shell
[root@localhost ~]# yum install -y yum-utils device-mapper-persistent-data lvm2
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: centos.ustc.edu.cn
 * extras: mirrors.aliyun.com
 * updates: mirrors.cn99.com
软件包 device-mapper-persistent-data-0.7.3-3.el7.x86_64 已安装并且是最新版本
软件包 7:lvm2-2.02.180-10.el7_6.8.x86_64 已安装并且是最新版本
正在解决依赖关系
--> 正在检查事务
---> 软件包 yum-utils.noarch.0.1.1.31-50.el7 将被 安装
--> 正在处理依赖关系 python-kitchen，它被软件包 yum-utils-1.1.31-50.el7.noarch 需要
...
...
```

4. 设置yum源（选择其中一个）

yum-config-manager --add-repo http://download.docker.com/linux/centos/docker-ce.repo（中央仓库）

yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo（阿里仓库）

```shell
[root@localhost ~]# yum-config-manager --add-repo 
https://download.docker.com/linux/centos/docker-ce.repo
已加载插件：fastestmirror
adding repo from: https://download.docker.com/linux/centos/docker-ce.repo
grabbing file https://download.docker.com/linux/centos/docker-ce.repo to 
/etc/yum.repos.d/docker-ce.repo
repo saved to /etc/yum.repos.d/docker-ce.repo
```

5. 可以查看所有仓库中所有docker版本，并选择特定版本安装

```shell
[root@localhost ~]# yum list docker-ce --showduplicates | sort -r
已加载插件：fastestmirror
可安装的软件包
 * updates: mirrors.cn99.com
Loading mirror speeds from cached hostfile
 * extras: mirrors.aliyun.com
docker-ce.x86_64            3:19.03.2-3.el7                     docker-ce-stable
docker-ce.x86_64            3:19.03.1-3.el7                     docker-ce-stable
docker-ce.x86_64            3:19.03.0-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.8-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.7-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.6-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.5-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.4-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.3-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.2-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.1-3.el7                     docker-ce-stable
docker-ce.x86_64            3:18.09.0-3.el7                     docker-ce-stable
docker-ce.x86_64            18.06.3.ce-3.el7                    docker-ce-stable
docker-ce.x86_64            18.06.2.ce-3.el7                    docker-ce-stable
docker-ce.x86_64            18.06.1.ce-3.el7                    docker-ce-stable
docker-ce.x86_64            18.06.0.ce-3.el7                    docker-ce-stable
docker-ce.x86_64            18.03.1.ce-1.el7.centos             docker-ce-stable
docker-ce.x86_64            18.03.0.ce-1.el7.centos             docker-ce-stable
docker-ce.x86_64            17.12.1.ce-1.el7.centos             docker-ce-stable
```

6. 安装Docker，命令：yum install docker-ce-版本号，我选的是docker-ce-18.03.1.ce，如下

```shell
[root@localhost ~]# yum install docker-ce-18.03.1.ce
已加载插件：fastestmirror
Loading mirror speeds from cached hostfile
 * base: centos.ustc.edu.cn
 * extras: mirrors.aliyun.com
 * updates: mirrors.cn99.com
正在解决依赖关系
--> 正在检查事务
---> 软件包 docker-ce.x86_64.0.18.03.1.ce-1.el7.centos 将被 安装
```

7. 启动Docker，命令：systemctl start docker，然后加入开机启动，如下

```shell
[root@localhost ~]# systemctl start docker
[root@localhost ~]# systemctl enable  docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
[root@localhost ~]# docker version
Client:
 Version:      18.03.1-ce
 API version:  1.37
 Go version:   go1.9.5
 Git commit:   9ee9f40
 Built:        Thu Apr 26 07:20:16 2018
 OS/Arch:      linux/amd64
 Experimental: false
 Orchestrator: swarm
 
Server:
 Engine:
  Version:      18.03.1-ce
  API version:  1.37 (minimum version 1.12)
  Go version:   go1.9.5
  Git commit:   9ee9f40
  Built:        Thu Apr 26 07:23:58 2018
  OS/Arch:      linux/amd64
  Experimental: false
```

> 附一些常用Docker命令，更多命令详解，请访问：http://www.docker.org.cn/dockerppt/106.html:
>
> docker ps 查看当前正在运行的容器
>
> docker ps -a 查看所有容器的状态
>
> docker start/stop id/name 启动/停止某个容器
>
> docker attach id 进入某个容器(使用exit退出后容器也跟着停止运行)
>
> docker exec -ti id 启动一个伪终端以交互式的方式进入某个容器（使用exit退出后容器不停止运行）
>
> docker images 查看本地镜像
> docker rm id/name 删除某个容器
> docker rmi id/name 删除某个镜像
>
> docker run --name test -ti ubuntu /bin/bash  复制ubuntu容器并且重命名为test且运行，然后以伪终端交互式方式进入容器，运行bash
>
> docker build -t soar/centos:7.1 .  通过当前目录下的Dockerfile创建一个名为soar/centos:7.1的镜像
>
> docker run -d -p 2222:22 --name test soar/centos:7.1  以镜像soar/centos:7.1创建名为test的容器，并以后台模式运行，并做端口映射到宿主机2222端口，P参数重启容器宿主机端口会发生改变

## docker关于python环境的报错

### File "/usr/bin/yum", line 30 except KeyboardInterrupt, e: SyntaxError: invalid syntax

这个报错的解决办法是

修改文件1

```shell
vim /usr/bin/yum

#!/usr/bin/python2.7
import sys
try:
    import yum
except ImportError:
    print >> sys.stderr, """\
```

修改文件2

```shell
vim /usr/libexec/urlgrabber-ext-down
 
#! /usr/bin/python2.7
#  A very simple external downloader
#  Copyright 2011-2012 Zdenek Pavlas
 
#   This library is free software; you can redistribute it and/or
#   modify it under the terms of the GNU Lesser General Public
```

然后再继续执行命令即可

### File "/usr/bin/yum-config-manager", line 133 except yum.Errors.RepoError, e:

这个报错的解决办法是

```
vim /usr/bin/yum-config-manager 
```

把头部的python改成python2

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200422121205.png)

再继续执行命令即可