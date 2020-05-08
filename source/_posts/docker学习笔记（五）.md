---
title: docker学习笔记五
author: LY
date: 2020-5-07-21:50
summary: 介绍了Docker swarm
categories:
    - 运维
tags:
    - docker
password: d548c5b83fa61d8e3bd86ad42a7ffea9b7c86e3f9d8095c1577d3e1270bb9420
---

> 总结慕课网docker课程的学习总结，本篇总结了docker swarm的理念和操作

## Docker swarm

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507213343.png)

之前学的都是docker在一台机器上运行，但是一般的应用都是比较复杂的，不可能所有的应用都在一台机器上运行

生产环境一般都是在集群里面部署应用，这样就会涉及到很多容器，那么就会遇到如下等问题

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507213536.png)

在这样的情况下，需要一套容器的编排系统，在这样的情况下swarm就诞生了，swarm并不是唯一的一个编排的工具，但是swarm是唯一一个内置于docker的编排工具，之前的都是运行在单机的模式下，不是运行在swarm的模式下

这里先介绍一下swarm的架构，以及重要的概念

## swarm基础架构

swarm是一套集群的架构，有集群里面肯定是有节点，有节点的话就有角色

在swarm里面，节点主要有两种角色，一种叫manager，一种叫worker

第一种角色就是manager，manager是整个集群的大脑，既然是大脑，而且是生产环境，为了避免单点故障，大脑至少要有两个，如果是多个的话，那么就设计到状态的同步问题，这个就涉及到一个内置的分布式存储的数据库，这个数据呢是通过Raft协议去做的一个数据同步，Raft能够确保manager节点之间的信息是对称同步的

第二种角色就是worker，worker就是一个干活的节点，大部分的容器要部署，都要运行到worker节点上面，当然manger节点上也是可以运行的，但是主要还是在worker节点上面，因为worker节点比较多一点。worker节点也有数据需要同步，他们就通过一个叫Gossip的网络去做信息的同步

整体架构如下图所示：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507214550.png)

## Service和Replicas

Service在docker compose里面的Service概念其实是一样的，一个Service就代表了一个容器，但是在Replicas模式的情况下，就是需要去做一个横向的拓展，既然是横向去拓展，在部署的时候，一个Replicas其实就是一个容器，比如下图的service是Nginx，要给它做三个拓展，实际上部署的时候就会产生三个容器，他们会通过调度系统来调度到不同的node上面（也就是通过swarm manager这个节点去部署一个service的时候，是不知道service会运行在哪些swarm节点上面的，这个是通过swarm的调度算法去算的，比如看哪些节点的负载比较轻一些，有充足的内存和cpu资源，就会把容器调度在这台机器上面）

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200507214633.png)

![swam创建service的时候调度的过程](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508095409.png)

基本上讲的就是在swarm manager上做所有的决策，最终的结果呢就是把service部署在哪台node节点上面，再去执行实际操作就可以了，这个就是一个基本的调度情况

## docker swarm创建三个节点

如果要安装一个 swarm cluster

先看初始化init的参数

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508103017.png)

主要看第一条参数指定地址

因为初始化swarm cluster就要宣告一个地址，cluster必须要有多个节点，要让别的节点知道这个节点的存在，就要添加一个本地的地址，本地的地址通过ip a查看

知道自己的服务器地址后直接添加manager节点即可

输入后可得到docker swarm join之类的结果，如下图所示

![manager节点](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508110231.png)

只需要黏贴这串字符串执行，那么就可以添加为worker节点

然后再用一个服务器，执行这串命令，如下图所示就是成功

![worker1节点](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508110447.png)

再切回manager服务器查看节点

![manager节点](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508110529.png)

可以看到节点添加成功

实战需要三台服务器，所以我再用本地的docker添加为worker节点

![worker2节点](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508110846.png)

可以看到已经有三台服务器，有leader就是manager节点的服务器，其余皆是worker节点

![manager节点](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508110932.png)

当然还可以通过docker-machine和play-with-docker

并且play-with-docker还可以快速创建节点和环境

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508111622.png)

## docker swarm service

在docker-compose里面一个service就是一个容器，而且是在允许在同一台机器上面

而在swarm模式下面呢，如果是创建一个service，这个service是一个容器，默认情况下会运行到cluster里面的任何一个节点上面

在docker swarm模式下创建service是不同的

先查看命令如何使用

```
docker service
```

![docker service](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508112139.png)

这个create命令跟run命令很相似，但是run是在本地创建一个container，在cluster里面创建一个service，这个service不一定会在本地

![docker service create命令](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508112724.png)

这里先创建一个service

![创建service](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508113037.png)

busybox就是image的名字，sh也就是shell，这个就是command

然后`docker service ls`就可以看到`service`创建完成

![docker service ls执行结果](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508113211.png)

这个service也可以理解为container，这个在哪台机器上呢可以通过ps命令查看

```
docker service ps +service的名字
```

![查看service在哪个节点](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508113404.png)

从图中看到，这个容器是在docker-desktop这个节点上的，也就是我本地window上的docker节点，然后在本地的docker查看一下运行的容器

![在docker-desktop节点上查看运行的容器](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508114342.png)

可以看到刚刚service创建的容器，这个名字并不叫demo，而是`demo+.随机数`

接下来可以看`docker service ls`执行结果的`MODE`和`REPLTCAS`

这两个说明这个service是可以水平拓展的，这个跟docker-compose的scale，可以把service去横向去拓展，拓展成多个，这个`REPLTCAS`也是同样的意思，那么如何对它进行拓展呢，其实命令差不多的，先执行下面的命令查看如何使用 

```
docker service scale
```

![docker service scale查看命令](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508115448.png)

然后对service demo进行拓展

```
docker service scale demo=5
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508115553.png)

因为这个service要平均部署到cluster里面，可能在其他的节点看到这个service（容器）

然后等待执行完毕再看看service

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508115722.png)

这里就变成了5个了，分母代表scale的数量，分子是代表有多少个service ready了

这里再看看service的详情

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508115834.png)

这里是部署到整个cluster里面的，具体详情如下图

![manager节点](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508120046.png)

![worker1节点](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508120059.png)

![worker2节点](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508120116.png)

这里如果是我再其中一台服务器把service强制删除

![错误：destop节点删除容器](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508120236.png)

然后再ls看一下service的情况

![错误：manager节点查看结果](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508120610.png)

一开始是4/5的，几秒后就变成5/5了

下面这个也是一样

![work1节点服务器删除容器](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508120524.png)

![manager节点服务器查看结果](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508120549.png)

然后等待几秒后再查看一次

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508120711.png)

这个究竟是什么原因让4/5变成5/5呢

这里详细查看一下demo

![docker service ps demo](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508142754.png)

 可以看到启动的容器数量还是没有少的，这个scale的5不仅是能做到横向拓展，还能够保证一定数量的拓展是有效的，也就是说swarm发现部分的节点失效了或者是退出了，然后通过cluster的任意节点去修补来达到特定数目的scale，能确保数量一定是5，而且是一定是有效的，也就是能确保系统是有效和有用的

如果是想删除的话

直接rm +service的名字即可，比如

```
docker service rm demo
```

虽然删除后ls和ps上是看不到了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508144847.png)

然后`docker ps` 马上看节点上的容器还是在运行的，要等几秒才能够删除完毕

docker service remove这个过程是很快的，但是后台需要有一个比较复杂的操作，要看service有多少个容器在cluster里面，并且在哪台机器上面，然后再去把container删除，这个过程比较复杂，所以比较慢一点，但是service本身的删除是很快的

## WordPress实战

 如果是用swarm来启动WordPress的话，要考虑到WordPress和mysql通信问题，这两个容器极有可能不在同一个节点上，这个其实可以用docker-compose的那个原理，只要两个容器在同一个网络上就可以进行通信

所以是先要创建一个overlay的network

```
docker network create -d overlay demo
```

先在manager节点服务器创建并且查看网络

![manager节点查看网络](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508151237.png)

![worker节点查看网络](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508151324.png)

这里worker节点服务器是没有的

这里先去创建一个mysql的服务看一下

**注意：一定要是5.7及以下，因为mysql8.0有新的验证**

```
docker service create --name mysql --env MYSQL_ROOT_PASSWORD=root --env MYSQL_DATABASE=wordpress --network demo --mount type=volume,source=mysql-data,destination=/var/lib/mysql mysql:5.7
```

 --env：就是环境变量

--mount：在docker run有个-v的参数来指定volume，在service就是--mount，格式是type就是volume，volume的名字就是mysql-data，destination就是文件的地址

--network：就是指定的网络名字

![mysql创建成功](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508153243.png)

然后ps看一下mysql这个service在哪个地方

```
docker service ps mysql
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508153333.png)

然后发现是在manager的服务器上

![manager节点服务器ps查看](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508153440.png)

然后创建的应该是WordPress这个service

```
docker service create --name wordpress -p 80:80 --env WORDPRESS_DB_PASSWORD=root --env WORDPRESS_DB_HOST=mysql --network demo wordpress
```

![安装WordPress](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508153813.png)

然后要查看一下WordPress被安装在哪个节点上了

![查看WordPress在哪个节点](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508160412.png)

可以看到是在我的另一台worker1节点服务器上

![worker1节点服务器](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508160517.png)

然后输入这个节点的ip地址

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508162406.png)

则docker service之间是可以正常的访问的

而且无论你输入的是manager还是worker的节点，正常情况下输入ip地址：80端口都是可以访问WordPress的，但是因为我的服务器80端口部署着网站，所以就不再演示

在创建两个容器之前，创建了一个名为demo的network，但是在manager节点的服务器创建完之后并没有在worker服务器能够实时查看的到，但是创建完service以后，worker上的名为demo的network就可以显示了，这个过程是swarm去完成的，WordPress这个节点被分配到worker这个节点以后，要保证和manager节点进行通信的话，这个overlay的网络一定要同步，swarm这个底层的机制会自己去同步网络的创建，因为要实现多个节点之间的网络通信，肯定要通过overlay这个网络去实现

## 集群之间的网络通信

上面的那个实战`WordPress`有两个`service`组成，一个`WordPress`，一个`mysql`，通过`service`的创建，把它部署到`swarm  cluster`里面

这两个`service`是在两个不同的节点上面的，注意这两个节点之间是需要相互通信的，这个就涉及到`DNS服务发现`的问题

通过`docker-compose`去部署一个app的话，如果app的`service`，如果他们连接到了一个网络上面去，他们之间就可以通过`service`的名字去相互访问的，底层是通过`DNS服务发现`去实现的，这个是`docker-compose`在单机的情况下

如果是在`swarm`的`cluster`里面，`service`是在不同的节点上面的，他们之间也是可以通过`service` 的名字去通信，这个肯定也有`DNS`的功劳在里面，对于`swarm`来讲，有内置的`DNS服务发现`的功能在里面 ，`service`之间如果是连到一个`overlay`的网络上面，然后就可以在`overlay`这个网络上面的所有`service`添加DNS的记录，通过DNS记录呢就可以得到他的IP地址，然后就可以访问这个服务了，但是实际上来讲，DNS的名字呢就是`service`的名字，但是对应的DNS记录后面的IP并不是这个`service`所在容器的`IP`，而是有一个虚拟的IP地址叫做`VIP`，为什么会这样呢，如果`service`有`scale`（即有横向的拓展），`service`有`scale`来说不一定在哪个节点上面，可能是因为某个节点进程的service down掉了，可能会在另一个节点上重新启动一个，这个时候IP地址都变了，或者说`scale`的时候有很多个`service`，对应着很多个IP地址，如果是在`DNS`里面，通过真实的IP地址去标识的话，这个就是很不稳定的，这个时候就可以通过虚拟的IP来解决这个问题（即`service`会分配一个`虚拟的IP`，而这个`IP`是不会变的），但是这个`虚拟IP`背后指的实际的IP，这个呢就是通过`LVS`来实现的，下面通过一个实验来证明

###  确定不是真实的IP地址

首先要要确保有一个名为`demo`的`overlay`的`network`，如果没有就通过以下的命令去创建一个

```
docker network create -d overlay demo
```

接着创建一个名为whoami的service（是一个web服务，访问会返回容器的host name）

```
docker service create --name whoami -p 8000:8000 --network demo -d jwilder/whoami
```

然后看一下这个服务运行在哪个容器

```
docker service ps whoami
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508171352.png)

然后可以看到是在manager的容器上面

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508171449.png)

然后输入这个ip地址+端口看看

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508171546.png)

 然后再创建一个service也连接到这个名为demo的network上面

```
docker service create --name client -d --network demo busybox sh -c "while true;do sleep 3600;done"
```

创建好之后查看client这个service的信息

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508172017.png)

然后查看这个服务的节点服务器

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508172042.png)

然后进入这个容器`ping` `whoami`的这个`service`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508172218.png)

可以得出结论，不仅仅是可以`ping`的通的，而且`ping`的地址不是真实的IP地址而是`10.0.0.49`

### 确定地址不会改变

上面已经得出结论ping的IP地址不是真实的IP

这里再使用scale进行一次横向拓展

```
docker service scale whoami=2
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508172559.png)

然后回再部署一个whoami的service

等待部署完毕后查看部署到哪个节点了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508172646.png)

可以从图中看到已经部署了两个容器

然后再进入容器里面

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508173351.png)

这个ping的IP和上面的居然还是一样的，还是`10.0.0.49`

有两个whoami的service，它到底ping的是谁呢

这个`10.0.0.49`并不是任何一个容器的地址，其实是一个`VIP（virtual IP）`

这里因为跟视频的操作有冲突，可能是版本不同，所有nslookup whoami没有反应

如果是按照视频中的操作的话，`nslookup whoami`会返回`10.0.0.49`

如果`scale`了两个`whoami`，那么`nslookup tasks.whoami`会有两个地址，如下图

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508183052.png)

这两个才是真实的容器地址

同理，如果scale横向拓展成三个，再取得scale tasks.whoami，就会返回三个记录

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508183327.png" style="zoom:67%;" />

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508183306.png)

这三个就是真实的ip

如果是ping whoami的话返回的肯定只是唯一的结果，是视频中service的VIP`10.0.0.7`

而虚拟的IP会跟真实的IP有一个web关系，就通过这个web关系去找到service的实际ip地址

然后再去访问一下whoami的服务，会发现每一次访问得到的结果都是不一样的

原因如下：

首先whoami在三个节点上都有运行

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508202144.png)