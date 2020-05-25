---
title: Develoment和Production打包模式下的区别
date: 2020-05-24 10:55:12
summary: 由浅入深讲解webpack的作用和特点
categories:
    - 前端
tags:
    - webpack
---

## 两种模式下的区别

## 分离两个模式的配置文件

首先说明两种模式下的区别

在Develoment模式下，webpack可以启动dev-Server来启动一个服务器，服务器里面还集成了HMR的特性，并且在这个模式下source-map是非常的全的，可以快速定位到错误点

下面看看开发环境的配置

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200524143402.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200524143426.png)

除此之外还有一系列的配置

如果是要改成生产环境的话，那么又要把以上的配置删掉改过，这样就显得十分的麻烦

这个时候可以把开发环境的配置写到一个webpack.dev.js文件里面，然后把生产环境的写到webpack.prod.js里面

然后再在package.json里面配置两个环境不同的命令

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200524152258.png)

这个也就是vue cli之类的原理

## 取出共同配置文件

但是只是分离不够，因为生产环境和开发环境的webpack配置有大量的相同配置代码

这个时候可以新建一个`webpack.common.js`文件，把共同的配置项写在里面



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200524162309.png)

公共参数已经抽离出来了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200524162556.png)



但是这样还是不可以引入公共文件的

需要下载一个插件才行`cnpm install webpack-merge -D`

然后再去生产环境和开发环境的配置上引入这个文件



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200524163848.png)

- 需要引入merge的文件
- 需要引入公共配置文件
- 然后使用merge把两个结合起来



有一些脚手架的版本会把这三个webpack的文件放到一个build文件夹下，所以只需要改一下package.json的配置即可

