---
title: Vue配置移动端和PC端
summary: 讲解vue根据设备的不同来配置移动端和PC端
date: 2020-02-04 18:15:35
categories:
    - 前端
tags:
    - Vue
---

## Vue分辨移动端还是PC端

### 首先在**App.vue**配置判断请求头

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200321190919.png)

### 在路由文件route.js上配置好PC和Mobile的路由

**router.js**

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200321191013.png)

这里拿移动端的来示例

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200321190123.png)

### 配置视图文件

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200321190156.png)