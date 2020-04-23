---
title: DRF面试题总结
date: 2020-04-23
author:  Mr、G
categories:
    - 后端
tags:
    - python面试题
---

> 本文转载于https://www.cnblogs.com/gfhh/p/12103640.html#d20

### 6.drf组件提供的功能

```
 免除csrf认证
 视图(三种：(1)APIView,(2)ListAPIview,(3)ListModelMixin)
 版本处理
 认证
 权限
 节流（频率限制）
 解析器
 筛选器
 分页
 序列化和数据校验：可以对QuerySet进行序列化，也可以对用户提交的数据进行校验——展示特殊的数据
     depth
     source：无需加括号，在源码内部会去判断是否可执行，如果可执行自动加括号。【多对一、一对一/choice】
     SerializerMethodField定义钩子方法【多对多】
 渲染器：可以帮我们把json数据渲染到drf自己的页面上。
```

### 7.drf继承过哪些视图类？以及他们之间的区别？

```
 第一种：APIView
     第一种遵循了CBV的模式，里面的功能比较多但是需要自己写的代码也有很多
     提供了免除csrf认证，版本处理、认证、权限、节流、解析器、筛选器、分页、序列化、渲染器
 
 
 第二种：ListAPIView,RetrieveAPIView,CreateAPIView,UpdateAPIView,DestroyAPIView
     第二种则在第一种的基础上，封装了许多我们需要自己的写的代码，许多功能的实现只需要给专属的变量名赋值就可以实现该功能
 
 
 第三种：GenericViewSet、ListModelMixin,RetrieveModelMixin,CreateModelMixin,UpdateModelMixin,DestroyModelMixin
     第三种则重构了APIView中的as_view()方法，结合请求方法和不同Mixin类的方法名从而进行执行不同的功能。与前面两种最主要的区别是url路由中as_view()方法中需要传值。
     目前使用的主要目的是把第二种的bug(查询全部数据的功能和查询单个数据的功能无法在一个类中实现)实现在一个类中！
```

### 8.GenericAPIView视图类的作用



### 9.drf版本的实现过程？

### 10.drf组件认证的实现过程？

### 11.drf组件权限的实现过程？

### 12.drf组件中节流的实现方式？

### 13.序列化时many=True和many=False的区别？

### 14.drf各个功能的使用程度，不代表重要程度

### 15.什么是jwt？ 它的优势是什么？

### 16.装饰器

### 17.面向对象中基于继承+异常处理来做的约束

### 18.面向对象封装

### 19.面向对象继承

### 20.反射

### 21.ajax请求写法

### 22.跨域问题

### 23.如何解决ajax+跨域？

### 24.常见的HTTP请求方法

### 25.http请求中Content-type请求头

### 26.django中F查询

### 27.django中获取空Queryset

### 28.基于django的fbv和cbv都能实现遵循restful规范的接口