---
title: Nginx反向代理
author: LY
date: 2019-10-24
summary: 使用Nginx去间接访问内网
categories:
    - 工具
tags:
    - Nginx
---

## 目的

> 本文使用Nginx来模拟第三方云平台的运行，通过Nginx来去调用内网的接口，从而实现保护内网的真实IP又可以提供对外访问的服务的目的

## 架构思路

音乐、电影、书籍信息接口都是部署在服务器1里面，来模拟在内网的接口

`Nginx`作为中间件来模拟第三方的云服务器端，小程序等APP调用的时候是直接去调用`Nginx`服务器，而不是直接去调用内网的接口，达到一种隐藏内网的地址而又可以间接去调用内网的接口

我的第一个模拟内网的服务器IP是[39.106.12.6](http://39.106.12.6/)，第二个模拟云平台的服务器IP是[47.93.184.113](http://47.93.184.113/)

下面我拿个`Django API`提供对外调用来模拟内网的接口，拿小程序去对接口的出来的结果进行增删改查

## Nginx架构图

![架构图](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/Nginx/1571905522931.png)

## 演示

以下三个是我在服务器1里面写的Django的查询API，是我通过Nginx去访问的

![音乐信息](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/Nginx/1571903983358.png)

![书籍信息](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/Nginx/1571903998646.png)

![电影信息](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/Nginx/1571904033699.png)



如果我直接访问我的内网API则会被拒绝，如图所示

![直接访问服务器1](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/Nginx/1571904490645.png)

因为我在Nginx配置了只允许[47.93.184.113](http://47.93.184.113/)访问，其余IP访问都会报403错误，只有我指定的Nginx服务器IP才能 访问内网的接口，如图所示

![使用Nginx间接访问内网](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/Nginx/1571904572264.png)

![小程序去调用Nginx的IP](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/Nginx/1571906670097.png)

使用小程序等移动端APP也一样，直接去调用Nginx服务器的IP就可以调用内网的API

## 结论

使用Nginx作为中间件可以免去多余的第三方服务，而且还可以借助Nginx去实现多余的功能，比如负载均衡，反爬虫等

如果使用云平台和第三方的服务商进行内网穿透，会导致访问服务的速度变慢，而使用Nginx一步就可以达到原有的功能而且还更方便