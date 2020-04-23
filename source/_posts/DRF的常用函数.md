---
title: DRF的常用函数总结
author: LY
date: 2020-4-22
summary: 介绍了Restframework的各种细节
categories:
    - 项目总结
tags:
    - Django
    - Restful
---

> 介绍了restframwork在做的时候各种项目小结

## get_queryset

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416152030.png)get_queryset是返还的所有请求的结果，这里可以过滤掉不属于自己的请求，可以过滤掉想要过滤的值，比如说获取请求为price_min的参数，并且返还值

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416154137.png)