---
title: Restframework功能配置
author: LY
date: 2020-4-16
summary: 介绍了Restframework的分页、过滤、搜索、缓存、限速等配置
categories:
    - 后端
tags:
    - Django
    - Restful
---

> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里是对之前的restframwork的简述和对restframwork的部分功能配置做一个总结
> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;本文的案例、讲解均为慕课网天天生鲜项目后的总结

## 分页

### 全局分页配置

点击restframwork的源码的setting.py文件，可以看到DRF的所有的配置，如下图所示：

![图19：DRF的所有配置](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415182030.png)

这里可以看到分页的配置，如下图所示：

![图20，分页配置](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415182236.png)

然后在项目的setting.py里面重新配置就可以配置全局分页了

![图21，全局分页配置](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415182332.png)

### 局部分页

但是不是所有的数据都是需要分页的，而且默认的分页的字段是叫`page`，分页是可以局部配置的，比如下图

![图22 官网分页案例](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415184435.png)

如案例的下图：

![使用案例图](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415201140.png)

其中`page_size_query_param`就是配置显示多少条数据

## 