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

## 过滤

官网的文档有详细的说明

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416154525.png)

如上图所说，下载`django-filter`后要把`django_filters`加入到`apps`里面

![使用方法](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416155712.png)

直接在`filterset_fields` 编写过滤的字段就可以对字段进行过滤了，但是这个十分的严格，因为这个要精确查询的，只是按照这种写法是无法满足日常的开发需求的，因为没可能搜索需要数字和名字都一一对应的

所以这里可以看向django_filters,在github上搜索django_filter

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416160108.png)

找到榜一的项目进入文档

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416160157.png)

这里可以看到有关于DRF的过滤功能

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416160359.png)

这里可以添加一个filters的文件，里面填写我们要过滤的字段

然后就可以自己去编写过滤的方法了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416160616.png)

然后要把原来的`filterset_fields` 改成`django_filter`的`filterset_class` 

这样子才可以利用`django-filter`来进行模糊查询，就可以选择字段的区间

