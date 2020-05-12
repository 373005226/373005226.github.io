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

Swarm manager和worker节点之间通信是通过TLS去加密的，加密的信息的都是寸在于manager的节点的内置分布式存储数据库里面的，而且是通过加密存储在硬盘里面的

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

`docker service remove`这个过程是很快的，但是后台需要有一个比较复杂的操作，要看`service`有多少个容器在`cluster`里面，并且在哪台机器上面，然后再去把`container`删除，这个过程比较复杂，所以比较慢一点，但是`service`本身的删除是很快的

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

## 集群之间的网络通信Rouing Mesh之Internal

![Rouing Mesh的两种体现方式](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509110347.png)

上面的那个实战`WordPress`有两个`service`组成，一个`WordPress`，一个`mysql`，通过`service`的创建，把它部署到`swarm  cluster`里面

这两个`service`是在两个不同的节点上面的，注意这两个节点之间是需要相互通信的，这个就涉及到`DNS服务发现`的问题

通过`docker-compose`去部署一个app的话，如果app的`service`，如果他们连接到了一个网络上面去，他们之间就可以通过`service`的名字去相互访问的，底层是通过`DNS服务发现`去实现的，这个是`docker-compose`在单机的情况下

如果是在`swarm`的`cluster`里面，`service`是在不同的节点上面的，他们之间也是可以通过`service` 的名字去通信，这个肯定也有`DNS`的功劳在里面，对于`swarm`来讲，有内置的`DNS服务发现`的功能在里面 ，`service`之间如果是连到一个`overlay`的网络上面，然后就可以在`overlay`这个网络上面的所有`service`添加DNS的记录，通过DNS记录呢就可以得到他的IP地址，然后就可以访问这个服务了，但是实际上来讲，DNS的名字呢就是`service`的名字，但是对应的DNS记录后面的IP并不是这个`service`所在容器的`IP`，而是有一个虚拟的IP地址叫做`VIP`，为什么会这样呢，如果`service`有`scale`（即有横向的拓展），`service`有`scale`来说不一定在哪个节点上面，可能是因为某个节点进程的service down掉了，可能会在另一个节点上重新启动一个，这个时候IP地址都变了，或者说`scale`的时候有很多个`service`，对应着很多个IP地址，如果是在`DNS`里面，通过真实的IP地址去标识的话，这个就是很不稳定的，这个时候就可以通过虚拟的IP来解决这个问题（即`service`会分配一个`虚拟的IP`，而这个`IP`是不会变的），但是这个`虚拟IP`背后指的实际的IP，这个呢就是通过`LVS`来实现的，下面通过一个实验来证明

###  确定不是真实的IP地址

（1） 首先要要确保有一个名为`demo`的`overlay`的`network`，如果没有就通过以下的命令去创建一个

```
docker network create -d overlay demo
```

（2） 接着创建一个名为whoami的service（是一个web服务，访问会返回容器的host name）

```
docker service create --name whoami -p 8000:8000 --network demo -d jwilder/whoami
```

（3） 看一下这个服务运行在哪个容器

```
docker service ps whoami
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508171352.png)

（4） 可以看到是在manager的容器上面

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508171449.png)

（5） 输入这个ip地址+端口看看

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508171546.png)

 （6）再创建一个service也连接到这个名为demo的network上面

```
docker service create --name client -d --network demo busybox sh -c "while true;do sleep 3600;done"
```

（7）创建好之后查看client这个service的信息

```
docker service ls
```

```
docker service client
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508172017.png)

（8）查看这个服务的节点服务器

```
docker ps
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508172042.png)

（9）进入这个容器`ping` `whoami`的这个`service`

```
docker exec --it 6ead sh
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508172218.png)

可以得出结论，不仅仅是可以`ping`的通的，而且`ping`的地址不是真实的IP地址而是`10.0.0.49`

### 确定地址不会改变

上面已经得出结论ping的IP地址不是真实的IP

（1）这里再使用`scale`进行一次横向拓展

```
 docker service scale whoami=2
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508172559.png)

（2）查看部署到哪个节点了

```
docker service ps whoami
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508172646.png)

（3）进入容器查看真实的DNS地址

可以从图中看到已经部署了两个容器,然后再进入容器里面

```
docker exec -it 6ead sh
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508173351.png)

这个ping的IP和上面的居然还是一样的，还是`10.0.0.49`

有两个whoami的service，它到底ping的是谁呢

这个`10.0.0.49`并不是任何一个容器的地址，其实是一个`VIP（virtual IP）`

这里因为跟视频的操作有冲突，可能是版本不同，所有nslookup whoami没有反应

如果是按照视频中的操作的话，`nslookup whoami`会返回`10.0.0.49`

如果`scale`了两个`whoami`，那么`nslookup tasks.whoami`会有两个地址，如下图

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508183052.png)

这两个才是真实的容器地址

同理，如果`scale`横向拓展成三个，再取得`scale tasks.whoami`，就会返回三个记录

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508183327.png" style="zoom:67%;" />

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508183306.png)

这三个就是真实的`ip`

如果是`ping whoami`的话返回的肯定只是唯一的结果，是视频中`service`的`VIP``10.0.0.7`

而虚拟的IP会跟真实的IP有一个web关系，就通过这个web关系去找到`service`的实际ip地址

然后再去访问一下`whoami`的服务，会发现每一次访问得到的结果都是不一样的

原因如下：

首先`whoami`在三个节点上都有运行

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508202144.png)

然后访问`whoami`的DNS

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200508202313.png)

背后是有三个IP地址的

（4）测试负载均衡

进入容器后下载一下whoami，查看每次的host是否相同

每次访问视频里面的10.0.0.7，都会做一次负载均衡

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509100252.png)

从图中可知，每次的访问的得到的结果都不一样，是因为`swarm`部署到三个节点，再访问的话每三次返回的值都是不一样的

其中负载均衡都是通过`LVS`去实现的 ，整体架构如下图

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509111935.png)

访问web的时候访问的是那个VIP，VIP如何映射到具体的container的IP呢，这个就是通过LVS去实现的，LVS的全称为Linux Virtual Server，如果在谷歌搜索LVS Keepalived配置，这两个东西可以部署一个高可用的负载均衡配置

两台web service和一台client service都连接到同一个overlay的网络，他们分别位于不同的swarm节点上面，client访问web的时候是通过VIP去访问的，VIP会自动负载均衡到每一个service节点上面，这个就是internal Load Balancing，具体过程如下图

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509112515.png)

这张图的意思就是在client访问外边的service的时候，过程是DNS把service name解析成一个具体的VIP，VIP的话比如VIP去访问8000端口，那么具体会通过iptables以及IPVS（LVS）去做负载均衡，负载均衡到不同的service节点上去

## 集群之间的网络通信Rouing Mesh之Ingress

Ingress网络呢，就是service有绑定端口，比如说WordPress是绑定的80端口，whoami绑定的是8000端口。，但是呢service虽然只是在swarm的部分cluster节点上运行，但是呢可以在swarm的任何节点上通过端口去访问到这个服务，这个是就是Ingress

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509113821.png)

 这个ingress network其实之前的实验是有个现象的：把WordPress这个服务部署到swarm cluster里面，也就是只在一个cluster节点上，web服务暴露一个端口8080，但是在swarm cluster的任何节点上访问8080，都可以访问到WordPress ，这个就是ingress network去实现的

ingress network的作用就是，当去任何一台swarm节点上访问端口服务的时候，会把服务通过本节点的 IPVS（IPV6）去通过LVS去load balance到真正具有service的节点上面，比如图中的访问docker host3上的8080，但是docker host3上面肯定是没有这个service

但是可以通过ipvs把请求转发到另外两台具有service的docker host上面去，然后再把response返回，这个就是ingress network的一个主要的现象

 就想curl whoami，每次访问得到的结果都是不一样的，即便是在没有whoami的service的服务器，curl whoami也是照样可以返回结果

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509150834.png)



那么是为什么即使是在没有部署whoami的host上面，还是可以继续访问8000端口得到结果呢，这里可以通过看看iptables看看转化规则

```
sudo iptables -nL -t nat
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509152459.png)

红线这行的意思就是访问8000端口，就可以把端口转发到172.19.0.2:8000地址上

然后先看看本地端口的情况

```
ip a
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509152800.png)

可以看到两个标注的地方是同一个地址的

```
brctl show
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509152916.png)

可以看到这个docker_gwbridge连接的interface有两个

看一下network的情况

```
docker network ls
docker network inspect docker_gwbridge
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509153118.png)

而得到的结果中可以看到这个就是刚刚转发后的地址

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509153225.png)

但是端口转发后ingress-sbox后悔怎么样呢，这里就要先进到ingress-sbox里面看看

```
sudo ls /var/run/docker/netns
nsenter --net=/var/run/docker/netns/ingress_sbox
```

 ![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509153511.png)

这里是因为我用的是paly with docker，所以是进不去，正常情况下进去的情况是这样的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509153755.png)

进去后角色就变成了root

整体情况如下图

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509153916.png)

数据包 通过default_gwbridge转发到了一个叫做ingress-sbox里面，ingress-sbox这个其实是一个network inspect

在ingress-sbox里面通过iptables和IPVS（LVS）就可以看到数据被正确的转发到了另外一个host上面去，通过的网络就是ingress 的overlay的这个网络，经过vxlan的这个封装去到另一台机器上面

数据包进入到淡绿色框框里面了，要通过防火墙去看一下数据包的数据运行情况

```
iptables -nL -t mangle
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509160221.png)

标注的地方就是给8000端口做了一个mark，这个其实就是做负载均衡的，是要通过lvs把去往8000端口的数据包呢做一个load balance来转换到具体实际的ip地址

继续去演示一下LVS

```
yum install ipvsadm
ipvsadm -l
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509161455.png)

可以从图中看到做了一次load balance，选择了这两个地址，这两个地址就是真实IP地址

## DockerStack部署wordpress

Docker-compose是在本机上进行开发的工具和只能在本地进行部署的，在swarm里面不是单机的，是一个cluster，这里演示如何在swarm上面部署一个WordPress

docker- compose3版本中只增加了一个deploy命令

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509164117.png)	deploy支持的写法如下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509164220.png)

deploy之下有很多个子命令

### ENDPOINT_MODE

这个是指明endpoint的模式

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509164338.png)

这个有两种的取值方式，默认是使用VIp

第一种是VIP，也就是虚拟的IP，也就是上面提到的

第二种是dnsrr，也就是不使用虚拟的IP了，直接使用service的IP地址，但是service的IP地址肯定是有很多个 ，特别是使用scale拓展之后，每个service都有自己的IP地址，使用dnsrr的时候，就不通过ip地址去访问，通过dns的时候会帮我们去做一个robin（负载均衡的一种策略，如果是service有三个IP地址去循环使用）

### LABELS

这个是一个帮助描述的一个信息，类似一个key-value的形式

### MODE

这个有两种，一种是global，一种是replicated，区别在于global的cluster只有一个（即不能做横向拓展，就是不能用scale命令去拓展）。第二种是replicated，这个刚刚好相反，这个是mode的默认值

### PLACEMENT

这个主要是为了限制service的条件，比如限制节点的角色为manager

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509170547.png)

如图限制角色为managr，则db这个service就只会部署到manager上面

### REPLICAS

这个是mode设置成replicated的时候，就可以在初始化的时候指定需要几个replicas，比如说图中的6个，那么就会部署6个service

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509170938.png)

### RESOURCES

这个很简单就是做一些资源的限制，比如cpu啊memory啊。或者是做一个保留，去做一个优先保留cpu、memory，可以优先的去使用这些资源

### RESTART_POLICY

这个如果是某个service因为某种原因停止了，那么是否需要去重启，重启的条件是什么，比如延时啊、最大重启的次数什么的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509171221.png)

### UPDATE_CONFIG

做一些配置，这个配置可以用于对service进行更新的时候遵循的原则，比如并行的数量

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509171343.png)

如上图，对service进行update的时候，有两个replicas，如果parallelism为1，delay为10s，那么意思就是每个更新的间隔都是10s

###  实战

对比之前单机版的compose文件

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509171806.png)

首先如果是在swarm上面因为可能不在一台机器上面，所以就不能使用bridge网络了，要改成overlay

其次对mysql是有要求的，mysql只能有一台机器，不能够去做横向拓展，所以mode要改成global，而且还有额外的要求只能部署在manager节点上面

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509172058.png)

对于WordPress呢，首先是要可以横向拓展的，并且初始化replicated要等于3

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509172320.png)

第三个参数是RESTART_POLICY，重启或者是失败的情况下要设置一些参数

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509172517.png)

最后是要设置更新的策略

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509172624.png)

 

这里进入正题，在swarm模式下使用docker-compose去部署，命令是不同的

```
docker stacks
```

![docker stacks命令](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509173213.png)

`docker stack`的命令比较简单

`deploy`是通过命令去部署一个`stack`，这个`stack`就是一个`service`，`service`是`docker.compose.yml`的文件里面去自定义的

看看`stack`的命令，可以看出是要给一个`stack`取一个名字

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509173424.png)

这里把`stack`取名为`WordPress`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509173611.png)

紧接着是`--compose-file`后面要接一个文件

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509173927.png)

如图创建成功

`wordpress_my-network`这个前缀`wordpress_`就是前面取的`stack`的名字

然后查看一下`stack`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509174050.png)

再看一下细节

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200509174121.png)

## 部署投票应用

使用swarm部署投票应用的话是跟compose不同的

这里的image一定拉取才行不能通过build

## Docker Secret管理和使用

 上面的docker-compose.yml文件，如果要连接数据库的之类的话， 就需要添加密码，但是如果是生产环境的话，肯定是不可以把密码这么明显的信息放到文件里面的

所以肯定是希望docker可以访问到这个密码

Secret有如下几种类型

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200510202037.png" style="zoom: 50%;" />

![Docker Swarm架构图](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200510202108.png)

架构图如上图所示

Swarm manager和worker节点之间通信是通过TLS去加密的，加密的信息的都是寸在于manager的节点的内置分布式存储数据库里面的，而且是通过加密存储在硬盘里面的。基于这个环境之下，如果有个service想去用这个Secret的话，就比如说数据库想用一个密码，可以分配给这个service一个权限，能让他访问的到Secret就可以了

Docker Secret有如下那么几点

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200510202844.png" style="zoom:50%;" />

接下来查看一下secret的创建

```
docker secret create
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511104344.png)

意思就是secret可以是从文件里面创建，也可以从标准的输入里面创建

这里先看一下从文件里面创建

### 文件创建密码

```
vim password 
```

在里面随便写一个密码，比如我的是admin123，文件名字为password

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511105703.png)

然后再创建一个密码，password就是文件

```
docker secret create my-pw password 
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511105958.png)

就可以创建一个secret，然后再去把这个文件删掉

然后就可以看到secret上有哪些密码了

```
docker secret ls
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511110230.png)

### 输入流创建密码

```
echo "adminadmin" | docker secret create my-pw2 -
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511110409.png)

然后再看一下secret密码

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511110501.png)

### 使用secret

先看一下secret create的参数

```
docker service create --help
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511112726.png)

这个行也就是说可以把secret暴露给一个service

```
docker service create --name client --secret my-pw busybox sh -c "while true;do sleep 3600;done"
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511112955.png)

然后查看一下这个服务运行在哪个端口

```
docker service ls
docker service ps client
docker ps
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511113209.png)

看到到服务是在这台服务器上运行后，进入里面查看一下

```
docker exec -it 容器ID sh
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511113405.png)

可以看到里面的密码就是我刚刚创建的

当然密码可以创建一个也可以创建多个，比如mysql有个root密码和普通用户的密码

这里拿mysql的看一下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511113731.png)

从图中看出是有这么一行参数来指定root密码的

然后再创建一下service

```
docker service create --name db --secret my-pw -e MYSQL_ROOT_PASSWORD_FILE=/run/secrets/my-pw mysql
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511113951.png)

等待几分钟，创建好并且查询到之后找到这个容器

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511114355.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511114406.png)

进入这个容器查询密码

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511114700.png)

密码没错就是刚刚我创建的，然后再去执行进入mysql

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511114752.png)

密码我输入的就是admin123，如图是可以进入的，说明password生效了

### 如何在docker-compose.yml文件使用secret

![docker-compose.yml](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511115258.png)

这里图中标注的就是指定secret的名字和指定密码的存储位置

secret的来源有两种

一种是事先创建好了my-pw，也就是图中的写法，写好docker-compose.yml文件后，通过命令去创建即可

```
docker stack deploy WordPress -c=docker-compose.yml
```



另外一种是如果没有事先创建好，也可以在docker-compose.yml文件里面指定一个部分去专门定义secret

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511115521.png)

在当前的文件中也创建一个名为password的文件，在里面去指定密码，当然这种方式是肯定不推荐的，因为密码都已经保存在文件里面了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511115618.png)



### image更新

这个更新的意思是在运行的时候都进行更新，因为swarm是作用于生产环境的，也就是在swarm运行的是实际业务，当然是不可以停止几分钟去更新

这里进行一次简单的更新演示

实验前首先要确定有demo这个overlay的network

```
docker network ls
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511150149.png)

如果没有就去创建一个

```
docker network create -d overlay demo
```

然后创建一个服务

```
docker service create --name web --publish 8080:5000 --network demo xiaopeng163/python-flask-demo:1.0
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511151023.png)

注意这个只是1.0的版本，下面是演示如何更新到2.0版本

 （1） 首先要对业务进行一个拓展，因为要停掉一个去重新创建，但是又不能让业务中止

```
docker service scale web=2
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511152101.png)



（2） 为了证明服务没有中断，这里在work节点上写一个脚本不停地访问8080端口

```
sh -c "while true;do curl 127.0.0.1:8080&&sleep 1;done"
```



（3） 在manager使用service update

先看一下参数

```
docker service update --help
```

可以更新很多

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511152922.png)

比如说可以更新密码、更新端口、更新镜像等，这里就是要更新镜像

```
docker service update --image xiaopeng163/python-flask-demo:2.0 web
```

然后服务就已经开始更新了，这个时候就可以回去work节点查看是否更新成功

这个时候会出现如下状况

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511155028.png)

因为只更新了一个，另外一个是正在更新，等全部更新完之后就可以看到全部都是2.0了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511155130.png)



此时看看web服务的详情，会发现1.0的版本已经shutdown

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511155229.png)

说明更新是成功的

但是这个有个很严重的问题就是1.0和2.0的版本在更新的时候是并存的，这个是很有问题的

### 端口更新

```
docker service update --publish-rm 8080:5000 --publish-add 8088:5000 web
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200511155838.png)

这个时候发现原来的端口不能用了，端口改成8088了

### docker-compose.yml更新

如果是用过docker stack去更新的话，只需要改掉对应的地方即可，因为docker stack是没有update参数的

改完之后再docker stack deploy -c=docker-compose.yml即可









