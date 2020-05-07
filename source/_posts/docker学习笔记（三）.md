---
title: docker学习笔记三
author: LY
date: 2020-4-29-16:00
summary: 介绍了Docker的持久化存储
categories:
    - 运维
tags:
    - docker
password: d548c5b83fa61d8e3bd86ad42a7ffea9b7c86e3f9d8095c1577d3e1270bb9420


---

> 总结慕课网docker课程的学习总结
>
> 对于dockerfile中指定的volume和run -v的区别，以下这两篇文章讲的更清楚，我的这篇文章是简化了的
>
> http://dockone.io/article/128
>
> http://www.mamicode.com/info-detail-2531988.html

## Docker的存储

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430111256.png)

container是在image之上创建的一个运行时的一个可写的层，但是在container里面写的数据是仅限于container的，如果把容器停掉或者去删除，那么里面的数据就会消失，container本身是无法保存数据的，所以docker有一个持久化存储的方法

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430111524.png)

之前的方法是把数据写在一个可写的层上面的，docker有个机制可以把数据复制到本地或者在本地进行存储，那么存储的数据就跟容器隔离开了

docker对于数据的存储方案有两大类

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430111817.png)

第一个意思是存储数据化的时候是映射到本地硬盘上的

第二个是在第三方上进行存储，比如远程的NAS、AWS上

volume的类型有两种

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430112108.png)

第一个是存储位置是固定的，名字是以随机的字符串去命名的

第二个是绑定挂载的，需要用参数去指定

## Data Volume

volume数据持久化的方式有两种

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430154505.png)

以docker hub官方的mysql为例

可以看到

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430113137.png)

这里演示一下如何使用mysql的volume

当然mysq肯定是要密码的，这里我设置为空

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430113307.png)

![错误演示1](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430113824.png)

直接这么写是会报错的

![错误演示2](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430113850.png)

然后看一下本地的存储

![错误演示3](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430113918.png)

然后删掉即可

![错误演示5](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430114115.png)

因为上面我没有设置密码为true

下图中才是正确的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430114158.png)

这个时候mysql会在本地创建一个volume

查看这个volume的详细信息

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430114805.png)

这里再创建第二个container

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430114954.png)

如果把这两个容器停止再删掉，那这个容器的volume其实还是在的，这个让容器数据得以保存，如下图所示:

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430115308.png)

但是这里还是有个问题的，比如这个volume的名字，是随机生成的不太友好，inspect的时候都要查时间来看

其实是可以起一个别名的

这里先删掉刚刚创建的两个volume

```
docker run -d -v mysql:/var/lib/mysql --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430115914.png)

接下来要看看这个是否生效

先查看环境

```
docker ps
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430153320.png)

然后进入这换个环境

```
docker exec -it mysql1 /bin/bash
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430153433.png)

然后进入mysql环境

```
mysql
```

然后再查看数据库

```
show databases;
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430153508.png)

然后创建一个名为docker的数据库再退出

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430153710.png)

然后停止删除这个容器

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430153825.png)

再看看volume

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430153932.png)

然后再重新创建一个，但是volume的位置一样

```
docker run -d -v mysql:/var/lib/mysql --name mysql2 -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430154034.png)

再以同样的步骤查看容器的mysql数据库

 ![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430154204.png)

可以看到docker的数据库还是存在的

##  volume 加参数-v 和不加的区别

上面的演示是在`dockerfile`写好了`volume`的基础上，`run`加参数和不加参数的区别

这里重新演示一下

`mysql`本身是在`dockerfile`指定了`volume`目录的

![mysql dockerfile](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430162426.png)

### dockerfile加了参数，但是run不指定

```
docker run -d --name mysql1 -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql
```

然后再查看，可以看到并没有`volume`的地址，而是在`/var/lib/docker/volumes/`目录之下绑定一个匿名卷

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430162723.png)

### docker加了参数，run也指定地址

```
docker run -d -v mysql:/var/lib/mysql --name mysql2 -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql
```

然后再查看详细信息，可以看到`volume`的地址就为`dockerfile`和`run -v`指定的目录

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430163014.png)

### docker加了参数，run指定地址与dockerfile不同

```
docker run -d -v mysql:/var/lib/mysqltest --name mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430174259.png)

这个情况比较复杂，对于我的这个版本来说，如果`run -v`和`dockerfile`指定的`volume`目录不同，会创建两个volume文件

一个是我指定名字的，一个是随机生成的名字，目录的话都是在`/var/lib.docker/volumes/`

 

## Bind Mounting

这个与Data volume区别是，Data volume需要在dockerfile定义volume目录，，而Bind Mounting不需要，这个只需要在允许容器run -v的时候加上映射的参数，如下所示：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430175045.png)

这里视频中拿的是Nginx作为案例，是用80端口进行启动

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430175749.png)



如图查看-v后面接的是映射的目录，把当前的目录，映射为dockerfile的workdir目录 ![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430180527.png)

这种方法就是把容器的数据和服务器本地的数据做一个映射，这样数据就可以同步了，如下所示，在容器里面创建了一个test.txt文件，然后退出查看linux本机的目录也同样会生成一个test.txt文件

![](D:\workplace\blog\source\_posts\upload\image-20200505200547603.png)

如果是在linux本机把test.txt文件内容添加一个abc，那么容器内的文件应该也会同样的修改

![](D:\workplace\blog\source\_posts\upload\image-20200505200742792.png)



## 实战Bind Mounting

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430181255.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430181322.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430181549.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430181603.png)

最后把docker的5000端口映射到本地的80端口上，然后就可以通过本地的127.0.0.1访问这个服务了 

然后就可以在本地运行了，但是这样的话如果是要修改了项目的代码，那么就要重新的build，下面介绍如何使用bind mounting去进行文件的实时修改

这里先把container删除了，然后再执行一次运行的命令，-v 把本地的文件跟docker container的工作目录文件夹进行一次映射

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200430181732.png)

然后就可以把本地目录映射到容器的/skeleton目录下

然后只要本地的文件保存了，那么docker里面的容器就会同步的修改，与之同时的运行的项目也会对应的改变

## 实战WordPress

先创建mysql数据库，mysql的数据库如下：

![mysql image的参数](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506105703.png)

先创建一个mysql的容器

```
docker run -d --name mysql -v mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=wordpress mysql:5.7
```

![创建mysql容器](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506153410.png)

**mysql8.0的登录访问是要配置一些东西的，而mysql5.7及以下都是可以直接登录**

WordPress的使用数据库的参数如下

![WordPress的image参数](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506105710.png)

这里的mysql就是容器的名字

```
docker run -d -e WORDPRESS_DB_HOST=mysql:3306 --link mysql -p 8080:80 wordpress
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200506110449.png)

