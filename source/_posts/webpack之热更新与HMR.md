---
title: webpack之热更新与HMR
date: 2020-05-22 16:52:13
summary: 略降webpack热更新的三种方法和Hot Module Replacement的概念
author: LY
categories:
    - 前端
tags:
    - webpack
---

## 热更新

以上的演示都是需要每次修改都要重新打包，然后再点开html来运行

实现这个目的，有三种方法

### 方法1：修改package.json

之前写的scripts的bundle都是webpack命令，这里可以修改成为实时观看，只需要在webpack里面添加一个watch参数

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521181713.png)

这个意思是监听打包文件的变化，如果变化就重新打包

### 方法2： webpack-dev-server

我还是嫌方法1不够好，我还要watch可以自动打包，自动打开浏览器，还可以模拟服务器上的特性，可以借助webpack devS erver来实现

webpack默认没是有这个东西的，要额外的下载

`cnpm install webpack-dev-server -D`

![webpack.config.js](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521182336.png)

![package.json](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521182406.png)

可以看到npm run start启动后，会帮我们启动一个端口

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521182536.png)

而且打包文件的改变，并不用F5刷新，可以实时的改变

此外还可以设置自动打开浏览器

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521182814.png)

vue和react的脚手架都是使用了webpack-dev-Server

为什么脚手架可以设置跨域呢

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521183208.png)

`webpack`可以设置`proxy`来跨域，只要访问了`localhost:8000/api`，就可以转发到3000端口

方法2是用的人最多一个方法

webpack-dev-server打包不会生成dist文件，而是打包存储在内存里面了

### 方法3：自己创建服务器

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521184126.png)



这个也就是自己创建一个服务器，早起webpack-dev-Server不稳定而且功能比较少的时候，脚手架开发者都是自己写一个服务器来实现实时更新的功能的

```javascript
/* server.js */

const express = require('express')
const webpack = require('webpack')
const WebpackDevMiddleware = require('webpack-dev-middleware')
const config = require('./webpack.config')
const complier = webpack(config)

const app = express()
app.use(WebpackDevMiddleware(complier,{
    publicPath:config.output.publicPath
}))

app.listen(3000,()=>{
    console.log('server is running')
})

```

方法3耗费的精力十分的大，这个方法不建议

## Hot Module Replacement

### 案例1

编写配置文件

![index.js](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522103835.png)

![style.css](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522103848.png)

![package.json](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522103912.png)

依照如上的配置，使用`npm run start`命令启动

![结果](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522104251.png)

代码的结果是点击新增按钮，偶数的背景为黄色

此时我修改一下背景颜色

原来新增的item就消失了，虽然点击颜色已经发生了改变

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522104615.png)

但是原来新增的item却不在了

此时希望的是改变样式的时候，只改变颜色，而不把页面刷新

这里就可以借助于HMR的功能来实现这个效果

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522104944.png)

hot的作用是启动HMR

hotOnly的作用是如果HMR出了问题，webpack会刷新页面，这里因为不需要重新刷新页面，所以就是写为true即可

配置完成后，就可以改变样式不刷新页面了

### 案例2

案例的代码如下：

![打包代码文件结构](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522154319.png)

![counter.js](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522154308.png)

![index.js](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522154400.png)

![number.js](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522154412.png)

进行`npm run start`后

一开始就是这样的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522154929.png)

但是我修改`number.js`的`innerHTML`为2000

在HMR模式关闭的情况下，每次修改1000的值，页面都会进行一次刷新。

如果是开启的情况下，修改1000的值是不会实时刷新的，要手动F5刷新

如果是要实时刷新要额外写一些代码

在HMR模式开启的情况下编写如下新增的代码

![index.js](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522162041.png)



此时如果修改了innerHTML的值，会出现如下的情况

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522162234.png)

每一次修改都会新增一个节点

这里还要把之前的节点删除掉

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522162418.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522162358.png)

这里跟案例1的css文件类比，为什么css文件不需要这么一长串代码来监听HMR是否开启就可以不刷新执行

因为这么一长串代码在css loader里面已经编写好了，vue之类的也一样的，vue也可可以实时刷新，但是我们在使用脚手架的时候也没看到有这么一长串代码，因为vue loader已经编写好了

HMR的功能都是官网有详细说明的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522163221.png)

在官网中底层原理也有详细的文档说明

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522163304.png)