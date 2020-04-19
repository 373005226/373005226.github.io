---
title: 毕业设计总结
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

## 搜索

虽然`Django_filters`过滤字段也可可以搜索，但是这里还是介绍一下`restframwork`自带的搜索功能

如官网所示：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416173922.png)

这里要导入filters包和进行一些配置，这里我拿我的项目来演示

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416174259.png)

当然官网有更详细的配置

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416174059.png)

可以使用正则表达式来进行查询

## 排序

如官网所示：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416173840.png)

如图所示的配置：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416174213.png)

## 设置开发文档

在setting里面添加如下字段：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417105410.png)

再在urls里面添加路由即可

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417105507.png)

进入开发文档界面如下：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417110810.png)

## 登录

对于登录而言，其实DRF自带了一个登录也就是开发文档的那个登录

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416175725.png)

这个是因为在路由里面进行了设置

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416175859.png)

![DRF登录、登出的方法](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416180423.png)

这里可以看到有登录和登出的方法

再点进去看一下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416180513.png)

这里`csrf_protect`是针对于`DRF`开发文档的那个来做的，但实际上`DRF`开发的是不用管`csrf`的问题，因为前后端分离本身就是跨域的

这里直接上`DRF`的官方文档

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416182434.png)

DRF的登录原理，其实还是依靠于Django的认证的，其部分源码如下：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416201545.png)

这里没有做什么特殊的步骤，只是把request的user取出来而已，依赖的还是Django的session和auth

但是呢因为是前后端分离了，一般都是app端之类的，所以用的一般都是token

官网的文档如下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416212551.png)

插入authtoken后还要重新数据库迁移，迁移后会出现一张名为authtoken_token的表，但是这个表是没有任何的数据的，如下图所示：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417110717.png)

这里先配置登录的接口：

![登录方式](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416213541.png)

然后拿上面配置好的开发接口文档来进行验证：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417110925.png)

在进行账号密码登录的时候就可以获取一个token，并且存到数据库中

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417110941.png)





​	在setting的REST_FRAMEWORK添加TokenAuthentication配置好这个就可以在全局配置Token验证了，如下图所示：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417111233.png)

这里是对全局进行配置的，也就是对所有的页面都需要登录后才能访问，但是我们日常开发是需要部分的页面需要验证，所以这里的局部配置如下，先在REST_FRAMEWORK删掉token

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417111343.png)

然后在视图文件中在指定需要登录的页面配置好authentication_classes即可

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417150717.png)

这里在mixins里面打个断点，然后使用postman测试一下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417150840.png)

可看到user中的admin就是我的admin的token

但是这个token是有缺陷的，比如不能设置过期时间，如果被人拿到token就可以一直登录

## JWT登录

[JWT的详细介绍](https://www.jianshu.com/p/180a870a308a)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417160417.png)

点击文档查看流程

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417160938.png)

配置如上所示，进行如下步骤：

```
$ pip install djangorestframework-jwt
```



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417161222.png)

然后配置路由

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417161411.png)

测试结果如下：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417162042.png)

依照文档的说法：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417162150.png)

所以之后的验证都是把Token改为JWT

先把验证的方法改成JWT，这里跟token是一样的， 如果是在setting里面配置，那么就是所有的都要进行JWT登录验证，如果是放在视图类里面就是局部的验证

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417165400.png)

然后再去打断点验证的话

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417165642.png)

## 发送短信功能

这里我注册采用的是手机短信注册，这里我采用的是最便宜的云片网功能

注册序列化就不可以使用`ModelSerializers`了，因为验证码的数据字段是有个code，但是code是必填字段，所以是需要使用`serializers.Serializer`

此时还需要对手机号进行验证，如下图

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417175947.png)

这个方法前头的`validate_`是源码里面规定前头的

![官网文档](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417180557.png)

![源码](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417180148.png)

同时在视图的create方法中，还要写一个发送验证码的功能和生成验证码的函数，这个时候就要重写create方法才可以

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417181801.png)

写到这一步后，剩下的验证模块需要看云片网的文档来写

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417181828.png)

依照文档的code的0代表成功，所以返回的是400错误，如果成功了，那么就保存这个code和返还mobile信息和201状态码

![image-20200417181843523](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417181844.png)

## 注册功能

注册的话需要验证手机号验证码是否被注册、发送了几次验证码来取最后一次、验证码是否过期、长度验证等问题

验证方法的话官网有详细的说明，如下图

![官网关于验证字段的详细说明文档](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200419170539.png)

### 验证验证码是否存在

先获取短信的所有记录，这个code肯定要和手机号绑定起来的，是要查询手机号码里面的短信记录

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200417205724.png)

这里的`self.initial_data`的意思就是获取前端输入的数据

### 验证码的先后顺序和验证正确与否

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200419154826.png)

需要取得验证码的先后顺序，然后再对验证码进行验证

这里不去用get的原因是怕手机生成的字符串是一样的，第二get如果是在不匹配的情况下可能不返回数据，这里就要对数据异常进行各种检测，如果是使用filter的话就不用考虑那么多，如果没有数据就返回一个空数组，如果是有多个数据就返回个数组在进行判断

进行来这一步之后，code字段是多余的，因为在注册表中code这个字段没有必要存在，也就是在后面的数据中要把code这个字段设置为none

### 对所有的字段进行验证

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200419165738.png)

此处可以需要把上面没有实现的去除code和把username辅助给mobile去实现

validate这个函数是作用域全部的序列化字段之上的，attrs是每个字段validate_之后返回的所有字段

### 验证账号是否存在

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200419180612.png)

这里官网有详细的说明，对字段进行验证

我的项目结果如下：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200419202112.png)

### 输入框变成隐藏密码格式

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200419202714.png)

此处因为input输入的密码还是一个text的格式，需要输入框变成type="password"才可以达到想要的效果

### 输入密码存储为密文

此时存储的密码还是以明文的形式存储，此时需要把密码变成密文存储到数据库中

这里是有两种方法的

#### 方法1：

因为model继承的是AbstractBaseUser，所以进去找到这个的方法

![image-20200419210631784](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200419210633.png)

然后使用create方法在创建的时候使得密码变成密文即可

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200419211535.png)

这里设置的话只是对于这个方法可以把明文转换为密文

而方法2使用信号量的机制却可以把所有的新建用户的操作（admin后台操作）都可以设置

#### 方法2：

第二种就是采用Django自带的一个信号量的机制

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200419212217.png)

官网的文档反正我看不懂....建议直接去找博客看

DRF的文档倒是有详细的说明

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200419212709.png)

文档里面的sender指的是User，created指的是是否是新建的时候修改，这里肯定是新建用户的时候修改密码

先创建一个signals.py的文件，依照文档的说明写

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200419212958.png)

然后在apps.py的这个文件夹设置一个ready方法

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200419213133.png)





