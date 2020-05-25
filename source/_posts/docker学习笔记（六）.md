---
title: docker学习笔记六
author: LY
date: 2020-5-11-21:50
summary: 介绍了Kubernetes的用法和介绍
categories:
    - 运维
tags:
    - docker
password: d548c5b83fa61d8e3bd86ad42a7ffea9b7c86e3f9d8095c1577d3e1270bb9420

---

## Kubernetes介绍

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200513105938.png)

`swarm`的架构就是一个`manager`节点和`worker`节点，`K8S`的集群架构大致是一样的，但是呢在`K8s`中，`manager`节点成为`master`节点 ，`worker`节点成为`node`节点，`master`节点会对外提供一些接口，通过接口可以对`K8s`的集群进行操作

![Master节点](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200513110330.png)

`K8s`节点是整个集群的大脑，`master`的主要功能可以通过模块去看，从图中可知，主要有一个`API Server`（通过这个跟集群进行交互），`Scheduler`（调度模块，比如说部署一个应用，应用需要两个容器，容器要运行到哪个节点上去，也就是要`Scheduler`的调度算法来确定分配到哪个节点），`Controller`，`etcd`（就是一个存储K8S的整个状态和配置）

![Node节点](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200513165754.png)

对于`Node`节点来讲，最重要的概念就是Pod，Pod是容器调用的最小单位，Pod就是具有相同的namespace的一些container的组合

接下来看一下最下面的四个方块

`Docker`就是容器的技术

`Kubelet`：`node`节点是收到`master`节点的控制的，`master`节点如何去控制`node`节点呢，如何去创建pod、volume、网络等会通过Kubelet去创建

`Kube-proxt`：这个是跟网络有关的，K8S如何是想要暴露一些端口给外界的去访问，`Kube-proxt`就去做一些代理的端口的代理和转发，在`K8S`的`serverice`里面也可以做一些负载均衡，`serverice`的发现和负载均衡也是通过`Kube-proxt`来实现的

`Fluentd`：做日志的采集和存储以及查询的

除此之外还有一些`optional`的插件，有`DNS`服务模块来提供`DNS`服务的，`UI`来通过`web`的方式去访问`K8S`集群的；

最上层的还`有Imag Registry`来拉去`docker`需要的`image`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200513170828.png)



## K8S介绍

 Pod是K8s里面调度的最小单位，在K8s里面不对容container直接操作，Pod已经是最小单位了，那么pod和container是什么关系呢

一个pod可以包含一个或者多个container，一个pod共享一个namespace（用户、网络、存储等等），在一个pod里面的container就可以通过localhost去通信

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200514175857.png)

在运行容器之前需要安装一个单节点的K8S集群

```
minikube start
```

在使用集群之前要了解一个kubectl context（可以理解为上下文），kubectl 是一个client的cli，要去连接一个k8s的集群，k8s集群提供了一个API server，cli要去连接这个API server，必须要有一些基本的配置信息，比如说（API地址、IP地址、端口、基本的认证信息），把这些配置信息称之为一个context，minikube是已经帮我们生成好了

输入命令

```
kubectl config view
```

 可以看到当前的config的基本情况

```
kubectl config get-contexts 
```

![image-20200514202126622](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200514202128.png)

可以看到有一个集群，只要是不同的集群就有不同的config，所以就可以通过config去连接

通过

```
kubectl cluster-info
```

就可以看到集群的情况

同时也可以进入virtualbox创建的虚拟机

```
minikube ssh
```

## 端口映射

在视频中的实验是取一个pod_nginx.yml文件

![pod_nginx.yml](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515100826.png)

然后使用命令创建集群

```
kubectl create -f pod_nginx.yml
```

跑起来的结果如下：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515091338.png)

可以看到Nginx其实是一个容器，其实就是一个pod，pod里面跑一个container，那么这个pod在哪呢

然后使用如下命令可以看到pod的详细信息

```
kubectl get pods -o wide
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515091452.png)

可以看到是允许在minikube的这个节点上

如果现在是想进去容器里面那怎么办呢

可以进去minikube的这个节点里面，然后再在里面使用docker进行操作

即

```
minikube ssh	#先进去pod里面
docker ps		#进去里面再使用ps
```

可以看到里面有个k8s_nginx

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515091646.png)

当然也可以直接通过kubectl进去

```
kubectl exec -it nginx sh
```

这个方法是只进入第一个容器，如果是有多个容器的话，先要查看这个pod的详细信息

```
kubectl describe pods Nginx
```

就可以打印出来Nginx的详细信息

 ![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515095714.png)

可以看到IP为172.17.0.4，但是现在的操作环境是在minikube的外边是没办法访问到这个地址的

所以是要先进去minikube里面

```
minikube ssh
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515095858.png)

无论是ping还是curl都可以访问到这个地址的

但是这个Nginx不是为了给开发者自己去访问的，是要给外边去访问的

此时就需要把这个Nginx地址映射到minikube的接口上

接下来要看下minikube的地址

```
minikube ssh	#先进去minikube
ip a			#然后查看ip
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515100120.png)

可以看到这个地址是可以供外面去访问的

为了端口映射出去可以让外部访问，有两种方法

第一种：

```
kubectl port-forward nginx 8080:80
```

可以看到映射成功

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515100343.png)

但是这个方法是要一直去运行的

## pods的拓展

### ReplicationController

![rc_nginx.yml](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515101009.png)

还是按照老方法直接create方法去创建

```
kubectl create -f rc_nginx.yml
```

查看pods

```
kubectl get pods	#查看所有的pods
kubectl get rx 		#查看Nginx总量
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515101311.png)

如果删掉其中的一个pods

```
kubectl delete pods nginx-6r92b
```

 然后再查看

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515101442.png)

也就是说如果是通过ReplicationController去创建的话，pods会维持一个数目

那么如何去拓展呢，还是通过scael方法

```
kubectl scale rc nginx --replicas=2
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515101658.png)

  当然也可以升

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515101725.png)

此时可以看到每个Nginx都是监听到80端口的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515102159.png)

可以看到每个Nginx都运行在minikube的这个节点上

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515102227.png)

在docker swarm中，是有个virtual IP（不变的虚拟IP）去做负载均衡

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515102623.png)

### ReplicaSet

ReplicaSet和Replication唯一不同就是ReplicaSet支持new ser-based selector

那么如何去使用ReplicaSet呢

这里需要修改原来的rc_nginx.yml文件

![rs_nginx.yml](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515103212.png)

第二和第三个地方是ReplicaSet和Replication要求不同的

然后修改好之后

```
kubectl create -f rs_nginx.yml
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515103332.png)

如果是需要去拓展

```
kubectl scale rs nginx --replicas=2
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515103428.png)

## Deployment

![Deployment官方文档介绍](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515103810.png)

即如果我希望部署一个app，那么我要描述这个我希望的状态（比如我希望有三个拓展，pod的docker image是什么版本），Deployment Controller会努力使得这个声明实现，也就是让底层的pods和replicasets去帮我们实现这个所希望的拓展

但是如果是通过Deployment 去创建的pods和replicasets，我们就不能对他通过独立的去操作了，也不能delete删除掉replicasets，一定只能对Deployment 去操作

![deployment_nginx.yml](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515104414.png)

这里nginx是低版本的，等一下演示如何去升级

然后创建这个文件

```
kubectl create -f deployment_nginx.yml
```

此时已经是保持了三个replicas

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515104824.png)

可以看到deployment名为nginx-deployment，可以看到replicas的名字为deployment名字后面加随机数字

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515104945.png)

然后pods也是一样的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515105102.png)

可以看到前面的数字是一样的，后面接着的就是pods的随机字符串

先看一下所有的详细信息

```
kubectl get deployment -o wide
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515105235.png)

此时对deployment进行升级

```
kubectl set image deployment nginx-deployment
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515105418.png)

然后查看服务可以看到有个旧的被撤销了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515105442.png)

当然这个还可以进行版本的控制

这里先看一下版本

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515105524.png)

然后进行回滚

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515105608.png)

如图所示·版本回滚了，nginx的版本也变成了老版本

对于deployment来讲，可以很轻松的就可以进行版本的更新

以上讲部署nginx的方法（Pods、ReplicaSet、ReplicationController）都没办法吧minikube的端口让外部去访问，除非通过pod_forward

K8S提供了一个另外的方法，可以让端口暴露在节点上面

```
kubectl expose deployment nginx-deployment --type=NodePort
```

 然后看一下端口

```
kubectl get svc
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515110325.png)

可以看到监听了minikube的30451端口 

然后可以通过IP地址+30541去访问nginx，在个地址也就是minikube的地址或者是K8S集群Node的地址

## network

service和网络有密切的关系，这里要重新搭建一个环境，minikube的单节点环境可以演示但是不够直观，这里通过coreos去演示

可以通过vagrant方式或者是virtualbox方式安装

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515110826.png)

当然这里肯定会遇到兼容的问题，让kubectl去选择

 这里通过pod_busybox.yml和pod_nginx.yml两个yml文件去创建了pods

以下是两个pods的运行情况

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515144509.png)

如果是在busybox-pod容器里面去ping以下另一个容器的话是可以ping通的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515144757.png)

这里假设是在同一个node里面才可以ping通的，现在拿另一个集群的另个一个服务器去ping

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515144953.png)

而且还是可以ping通的，这个可以联到docker swarm的多机通信overlay网络

 ![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515145132.png)

如图所示：

C1可以ping通w1里面的两个地址，原因是因为两个地方之间做了一个overlay的网络

在这两台机器之间都安装了一个flanner的插件，这个插 件也是coreos的产品，通过这个插件实现了两个地方之间的overlay网络

里面的英文文章是k8s的网络说明文档，里面有列举很多插件，但是这些插件都要满足一个基本要求

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515145918.png)

1. 所有的容器跟其他容器之间可以直接通信，并且不需要NAT之间的转换
2. C1这台机器上的Node去ping W1上面的nginx和busybox，也就是在一个node上可以直接访问 其他的container，哪怕是这个container不在当前的节点上也是可以的
3. 就是自己的IP是什么， 别人访问他的IP地址就是这个地址

各个厂商都可以  根据这个规范要求去实现自己的插件 

在w1上使用ifconfig命令可以看到有一个flannel地址

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515150422.png)

## service

还是使用上面的图

 ![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515145132.png)

虽然是可以内部通信了，但是类似nginx之类的服务是需要外部网络可以访问的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515150746.png)

里面的每个pod都有自己的IP地址，每个之间都是可以ping通的

只要一个是web service，需要外部访问，只需要IP地址+端口号就可以访问到这个服务，但是不能直接这么做，会出现很多问题

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515151000.png)

在水平拓展的时候是做伸缩还是拓展，如果是减少的话那么就要销毁pod，那么这个IP地址肯定是没了，那么外部访问这个IP肯定就是有问题的了，如果是一个web服务，他的scale=3，那么外部访问三个地址之间的哪个

如果是通过Deployment去部署这个pod，后期如果是需要更新image，升级或者是降级，那么之间的pod就会被销毁，会生成一个新的pod，那么这个pod的IP也会改变

假如一个service是数据库，那么我们希望的是无论怎么改变，这个IP地址和端口都不要去变化，哪怕是要升级或者是降级

综上所述也就是关于service的问题

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515151451.png)

以下三种都是基于IP地址的

### cluster IP

#### pod

顾名思义，cluster IP就是只有cluster 可以访问的地址，外界是无法访问的，假如创建一个数据库service，类型为cluster IP，cluster IP是不会去改变的，是这个service所对应的IP地址，这个service肯定有pod，和这个pod的IP地址和service之间是有一个一对一的对应关系，这个service的IP地址是不会去变化的了，这个pod的IP地址是可以变化的，但是对外提供的cluster IP不会变，所以说这个服务是稳定的

实验环境还是基于上图的

![image-20200515152633840](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515152634.png)

然后使用命令

```
kubectl expose pods nginx-pod
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515152702.png)

然后再获取service

```
kubectl get svc
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515152727.png)

可以看到nginx-pod就是cluster IP，对外提供服务的话可以通过这个地址

10.2.0.252是nginx的pod地址，也是container的ip地址，自行销毁的话这个地址是会变化的，但是service ip是不会变化的

然后只需要在C1的服务器都可以访问到这个地址

#### deployment

如果是通过deployment去访问的话，先创建并且查看信息

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515153240.png)

在C1服务器访问10.2.0.142和10.2.0.141还是可以访问到这个服务的

这个服务是返回hostname，可以看到两个返回的信息是不一样的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515153416.png)

然后把端口暴露给外部去访问

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515153520.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515153601.png)

然后再去C1的服务器去访问这个地址

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515153713.png)

也就是这个service会给我们做一个负载均衡

#### 使用deployment去做一个无宕机的更新

这还是跟swarm的实验一样，使用循环去不停的访问地址

在C1的服务器上循环

```
while true;do curl 10.3.120.168:8080;done
```

在w1的服务器上可以set image，也就是使得docker image变化去更新deployment，看一下服务会不会中断

这里有另外一个方法：直接去编辑deployment文件，这里的编辑当然不是通过vim去编辑，而是使用kubectl去编辑这个deployment服务

```
kubectl edit deployment service-test
```

可以看到会打开启动这个service的yml文件

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515154331.png)

只改变输出的内容

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515154401.png)

保存退出后面可以看到服务会被down掉

中止了一断时间然后就变成新的输出内容了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515154440.png)

然后可以看到之前的pods已经被终止了还添加了一个新的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515154522.png)

但是这样并没有实现零宕机的更新，还是有那么一段时间没有访问到，因为这个并不是一个滚动的更新（Rolling Update）

### NodePort

### expose创建

顾名思义，是绑定到了一个node上面的，node是对外可以提供访问的

 这里先解释一下label

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515155356.png)

在K8S里面几乎是所有的资源都可以设置一个labels，labels是一个key-value的一个对，而且labels可以设置不止一个

先创建一个pod

```
kubectl create -f pod_nginx.yml
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515155709.png)

这里先看看帮助，因为默认的就是cluster IP模式

可以看到

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515155821.png)

type默认的就是cluster ip，只需要type指定就可以了

```
kubectl expose pods nginx-pod --type=NodePort
```

可以看到会有个IP

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515160020.png)

主要是看80:31404

然后看一下node

```
kubectl get node -o wide
kubectl describe node +节点名字
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515160254.png)

可以看到节点的IP地址为

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515160341.png)

在浏览器访问`172.17.4.101:31404`，可以看到访问nginx成功

再试一瞎另外一个node的地址`172.17.4.201:31404`，可以看到也可以访问

也就是NodePort这个service，可以把31404的这个整个端口映射到cluster里面的每个node的IP地址上

### 通过yml文件去创建

![service_nginx.yml](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515161352.png)

先创建这个服务

```
kubectl create -f service_nginx.ml
```

然后看一下服务

```
kubectl get svc
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200515161421.png)

然后输入`172.17.4.101:32333`和`172.17.4.201:32333`

但是这个NodePort，在端口低于3000的时候会报错，但是自己用的端口可能没那么大，可能是80、443 ，可以在后面加一个nginx代理映射或者是Port是暴露在所有的端口节点上，高端口数量是有限的，低端口不用是对端口是一种浪费，所以这个也不适合在生产环境用，但是可用。

在生产环境用的比较多的是ExternalName和LoadBalancer，LoadBalancer需要结合云服务商去用，ExternalName就是通过DNS的方式

### LoadBalancer

这个是需要云服务商去提供LoadBalancer，然后我们通过LoadBalancer就可以自动去访问pod里面的service

当然也可以去用DNS去做，但是是需要插件