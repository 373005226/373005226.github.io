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

先创建一个`signals.py`的文件，依照文档的说明写

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200419212958.png)

然后在apps.py的这个文件夹设置一个ready方法

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200419213133.png)





## 用户收藏

### 收藏功能

用户收藏的话应该是只需要填写商品即可，用户的ID默认为当前登录的用户，文档有说明

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200420115456.png)

此时选择商品添加即可，不用再去选择是哪个用户

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200420115545.png)

### 判断是否已收藏

一般情况下做出来的接口会是如下情况

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200420120111.png)

此时需要在model里面把用户和商品做一个联合唯一索引

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200420120313.png)

这条代码的意思就是如果记录重复了，数据库就会抛出异常，数据表的设计如下：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200420142521.png)

再继续收藏的话那么就会报如下错误：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200420142610.png)

在DRF里面也可以自定义

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200420142906.png)

两者可以都用也可以只用一个，DRF还可以自定义错误

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200420143104.png)

再继续收藏已收藏的商品就会报错

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200420143204.png)

此外，因为是联合查询，就要定义retrieve的查询字段，lookup_field，默认的查询是pk，这里指定为goods的id

![](D:\workplace\blog\source\_posts\upload\image-20200421092529564.png)

## 权限认证

DRF有丰富的权限认证

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200420144145.png)

这里的第一个验证IsAuthenticated是验证用户是否登录，如果没有登录的话那么就返回一个401错误

但是只有这个的话远远不够，需要自定义权限来判断删除的东西是属于自己的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200420145252.png)

图中的第一个是白名单设置，第二个是自定义权限设置，这里直接采用第二个即可

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200420145609.png)



##  动态权限设置

这里是用户注册的时候，这里可以有两种写法，一种是动态权限设置，一种是写两种的方法

这里我选择的是第一种

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200420153531.png)

这里因为注册的话，那么肯定是不能要登录后才能注册的，如果是查看个人信息的话肯定是要登录后的，于是就是这样设置

他源码的方法在`viewsets.GenericViewSet->GenericViewSet->GenericAPIView->APIView->get_permissions`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200420153838.png)

依照源码的方法所示：

![](D:\workplace\blog\source\_posts\upload\image-20200420214016211.png)

## 动态序列化设置

源码地址为：`viewsets.GenericViewSet->generics.GenericAPIView->GenericAPIView->get_serializer_class`

![image-20200420215012918](D:\workplace\blog\source\_posts\upload\image-20200420215012918.png)

所以就可以重载这个函数

![](D:\workplace\blog\source\_posts\upload\image-20200421091453768.png)

这里的`self.actiion`是只有`viewsets`才有的

## 购物车

购物车其实是比较复杂的，如果使用之前的viewset和serializers的方法，那么如果购物车已存在商品，那么再加入购物车， 就会报错说商品已经存在，这里需要重写部分的方法来改变原来的功能

比如说更新数据就是update、没有商品创建就是create，删除就是delete，展示就是list，已经把所有的方法都用上了

首先用户和商品应构成一个联合唯一索引

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421134148.png)

其次应该要进行验证，商品已存在应该是+1，而不是报错

此时用的应该是serializers.Serializer方法

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421135024.png)

这里goods是外键，但是官方文档那里写的是没有queryset的，因为官网文档使用的是ModelSerializer

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421135300.png)

接下来要重写create方法，create方法分为两种情况，一种是从无到有，一种是有了继续增加

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421135910.png)

这里create方法的validated_data，是序列化后的数据，initial_data就是没序列化之前的数据，这里的context["request"]是请求过来的数据

接下来要判断数据是否已经存在于购物车表中

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421142326.png)

先是判断如果存在，就取得第一条的数据，然后把数量+1

如果是不存在，那么就创建一个新数据

接下来视图函数也要加上

```
lookup_field = "goods_id"
```

因为详情修改的时候不是传入pk而是传入商品的ID才行

此时还是不能进行更新操作的，会报错

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421143053.png)

因为还没有重载update方法 ，为什么ModelSerializer不需要呢

这里看向源码`Serializer->BaseSerializer->update`

![image-20200421143630232](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421143631.png)

而在ModelSerializer中`ModelSerializer->update`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421143815.png)

是重写了update各种方法的

所以这里重写update方法

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421143914.png)

instance就是ShoppingCart这个model的实例

update方法在重写后就可以正常使用了

delete方法是不需要自己去重写的

## 订单

订单的话首先考虑的是如何清除购物车的商品，现在视频设计的是把购物车全部清空，这个等我有空我要改回来

视频中首先是把购物车的商品加到订单上，然后再把购物车的商品清空这两步

这里首先设计的是要生成订单号，订单号不能冲突，不然的话支付宝返回的支付地址就会出错

所以这里设计的是当前时间+随机数

 视频中建议在序列化文件中写

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421153047.png)

 

然后使用validate方法

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421153703.png)

在使用validate方法后

![image-20200421153840088](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421153841.png)

调用save就可以拿到order的实例了

接下来就可以把购物车的数据加入到订单的商品表，并且在最后清除掉购物车的数据

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421154138.png)

## 支付系统

因为支付宝的文档规定了很多的东西，所以这里就需要使用APIView来开发

get方法要处理支付宝返回的支付链接，post方法要处理支付宝的notift_url

这里要先去验证函数是否能接收返回的信息

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421160930.png)

这里要把app_notify_url和return_url都改成设置好的返回函数，也就是如下图

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421161244.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421161315.png)

先打断点看看函数是否可以接收到返回信息，再去写具体的业务逻辑

支付后返回的断点查看信息，所有的数据都在post里面

![image-20200421161514048](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421161514.png)

## 商品的点击数的修改

 点击数的话，因为商品视图表继承了RetrieveModelMixin，只需要重写这个方法即可

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421164229.png)



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421164300.png)

## 收藏数的修改

方法1：

在收藏商品操作中，重写mixins.CreateModelMixin的perform_create方法

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421183451.png)

instance就是UserFav的实例

方法2：

其次还有一个信号量这个方法可以用，这个方法的代码分离性比较好

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421185927.png)

## 修改购物车商品数量

这里先要记录保存之前的数据，再和现在的数据进行比对

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421210750.png)

## 商品销量增加

商品销量增加肯定是在购买后才可以增加的，所以要在订单视图函数里面配置

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421211026.png)

这里是反向查询数据的

## 缓存设置

部分页面的话不必要每次都查询数据库，Django本身是有个缓存机制的，DRF的缓存也是基于DJango的缓存机制开发的，但是这里不能直接拿Django的去用，因为Django的模板语言开发和前后端分离开发是有区别的，这里可以看向别人拓展的包

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421212938.png)

只需要

```
from rest_framework_extensions.cache.mixins import CacheResponseMixin
```

然后把CacheResponseMixin放到继承的第一个类中即可

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421213250.png)

这里已经是可以使用缓存了，但是这里最好还要配置一下redis作为缓存，因为这个默认方法是基于内存来缓存的，但是项目重启之后就失效了。这里还可以设置过期时间

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421213716.png)

## 使用redis缓存

 redis有更多的好处，比如如果只是在内存中存储，我们是看不到存储了什么的，如果是redis来存储的话可以看到更多的细节问题，比如请求列表的时候，缓存的是html还是json，比如说请求的参数不同对应的缓存是什么，通过redis的key就可以来看到

 使用Django redis的话需要用到一个第三方的库，是专门用来做Django的redis的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421214333.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421214805.png)

第一步当然是安装Django-redis，然后开启redis

接下来只需要把下面这段代码加入到setting里面就可以了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200421215003.png)

## 限速问题

为了防止恶意爬虫和DDOS攻击，要对数据进行一个限速，限速这个功能是DRF自带的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200422091201.png)

把这段代码加到restframwork的配置中

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200422091303.png)

第一个是不登录也可以请求，第二个是登录后的请求

这里可以定义单位为分钟、秒、小时、日期等

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200422091508.png)

然后只需要导入包

```python
from rest_framework.throttling import UserRateThrottle,AnonRateThrottle #配置访问的速率
```

再配置throttle_classes即可

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200422091658.png)

其内部实现原理如下：

`AnonRateThrottle->SimpleRateThrottle->parse_rate`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200422091810.png)