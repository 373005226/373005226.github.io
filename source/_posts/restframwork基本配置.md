---
title: 详解Restframework
author: LY
date: 2020-4-16
summary: 介绍了Restframework的序列化、视图的结构
categories:
    - 后端
tags:
    - Django
    - Restful
---

> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里是对之前的restframwork的简述和对restframwork的部分功能配置做一个总结
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;本文的案例、讲解均为慕课网天天生鲜项目后的总结

## Restframwork的通用配置

1. 在开始之前需要安装如下的包：

```python
pip install djangorestframework 
pip install markdown # Markdown 
pip install django-filter # 可选 
ip install coreapi # 可选
```

其次需要在`INSTALLED_APPS`添加`'rest_framework'`，因为`rest_framework`其实就是一个app，是把原本的http请求封装成一个自己的方式

2. 配置好自己的数据库字段，其次需要然后配置好自己的序列化文件`serializers.py`，如下所示：

**models.py**:

![图1](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415155822.png)



**serializers.py**:

![图2](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415155832.png)



然后再类视图**views.py**中配置好自己的字段

![图3](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415155834.png)



最后需要在**urls.py**里面配置好路由地址，这里是采用路由器配置的，不用再像函数式编程一样编写各种方法

![图4](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415155837.png)

这里的`path('', include(router.urls)),`就是配置首页的接口文档地址，如图所示：

![图5](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415155840.png)

`path(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework')),`是可以登录页面，即右上角的login

## serializers的结构

把models里面的数据序列化出来有两种方法，具体如下所示：

### serializers.Serializer

![图6](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415163852.png)

这里的`objects`其实是`Model`的一个管理器，`create`只是其中的一个方法。`ModelSerializer`则是把常用的方法固定，如果要重写方法的话还是得用`serializers.Serializer`

其中`create`方法和`update`方法里面的字段都是固定的写法的，重复代码太多

如果只是寻常的增删改查的功能的序列化的话，那么就可以把这些的配置转换为`ModelSerializer`

### serializers.ModelSerializer

![图7](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415164510.png)

### 需要serializers.Serializer情景

一般的话是不需要使用`serializers.Serializer`的方式的，因为重复的代码太多。如果是一些需要特定的验证，比如：

验证购物车：

![图8](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415164933.png)

验证手机号：

![图9](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415165723.png)

以上类似这些功能的话`serializers.ModelSerializer`是完全封装好的，所以就只能用`serializers.ModelSerializer`

## Views的结构

### APIView

![图10](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415171818.png)

![图11](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415172547.png)

这个方法是DRF的`views`基本的方法，而`APIView`又是基于`Django`的`View`，其余的所有方法都是基于`APIView`来写的

这里`is_vaild`方法是跟`Django`的`forms`表单验证一样的

这里的request.data其实在Django里面是没有的，但是DRF进行了封装，无论用户是POST过来的数据还是什么，都会放在data里面，这样取的数据就会很统一，不用再去判断什么get、post方法

### USing mixins

![图12](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415173818.png)

这里是官方文档介绍的方法，其中`GenericAPIView`的源码如图：

![图13](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415174124.png)

他是继承于`APIView`的，`APIView`是DRF的东西，其中`APIView`又继承与Django的`View`

![图14](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415180029.png)

如图12所示，为什么还要重写get方法和post方法呢，因为点击`ListModelMixin`的源码可知：

![图15](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415174606.png)

里面有一个list方法，所以在使用的时候是需要重写的单单定义一个`serializers_class`是没有用的，还需要重写对应的方法才可以使用，如果重写`get`方法的话就会出现如下错误：

![不重写get方法的错误提示](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415174922.png)

这里的`get、psot、put、delete`的四种方法分别对应着`http`的四种方法



至此这里的方法已经很简单了，但是还是每个接口都需要调用一下get、psot等重复的字段，那么以下的方法更为的间接

这里可以看一下源码的结构，打开源码后点开这里：

![图16：查看源码结构](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415180545.png)

![图17:ListAPIView源码](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415180624.png)

这里DRF又做了进一步的封装，原本需要重写get的方法，在这里已经重写了。这里就可以在views直接继承这个方法，而不用去重写原来的字段了，其余的post、put、delete三种方法也是一样的，即cbv的写法

### Using generic class-based views

采用了图17的ListAPIView的写法后，只需要很少的代码就可以显示所有的结果了，如下图所示：

![图18](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415181213.png)

## generics和ViewSets的结构区别

generics的方法和viewsets的方法其实本质上是一样的，这里我来部署一下结构图：

### generics结构图

![viewsets第一层结构](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416095113.png)

![viewsets第二层结构](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416095200.png)

![viewsets第三层结构](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416095245.png)

![viewsets第四层结构](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416095317.png)

![viewsets第五层结构](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416095340.png)

其中viewsets的前四层结构都是DRF封装好的，只有最后一层View是Django的，这里摆一下generics的结构图

如果是要使用GenericAPIView，那么就跟图12一样，需要自己去重写get和post等方法，太多的重复，如下图所示：

![GenericAPIView结构图](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416101327.png)

如果是这样的话就要跟图12一样重写get和post方法。



如果是继承其他的方法的话，使用对应的方法就可以不用重写对应的代码，如下图所示：

![ListAPIView结构图](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416103114.png)

如图18一样只写`generics.ListAPIView`不用再去重写get方法就可以调用

当然每一个方法都要写一次的话代码肯定还是很多，这里也对方法做了一个整合

![ListAPIView方法的整合](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416104417.png)

需要get、put、partch就可以使用这种集和的类

然后最后在urls里面用正则表达式配置路径即可，如果是需要重写get等的方法，就可以使用generics

### ViewSets结构图

generics是内部实现了get、post等方法，但是viewset不同，因为他内部没有去实现那些get、post等方法

这里直接摆viewsets结构图

![viewsets第一层](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416105906.png)

![重点！viewsets第二层](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416105921.png)

![重点！viewsets第二层](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416110022.png)

![viewsets第三层](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416105947.png)

![viewsets第四层](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416105959.png)

重点是viewsets第二层的ViewSetMixin方法，里面是没有实现get、post等方法的，具体如下：

![ViewSetMixin方法详情](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416110129.png)

所以如果是没有http的那五个方法的话，就需要在路由上设置，这里要重点看向as_view方法

至此都是使用视图的方法来创建API的，但是如果是要显示在url上的话，还需要进行路由的配置，这里有两种不同的方法

#### 方法1：配置api_view来显示api

在配置完viewset或者generics的视图代码后，我们要在路由显示可用的API，具体局部的实现看我[Restframework之超链接API]()这篇文章

#### 方法2：as_view配置API

如图所示：

![as_view配置API](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416114450.png)

这里需要一个方法一个视图的来配置，代码极为的复杂

#### 方法3：使用路由器的方法来配置API

这个才是最主要的写法，直接看图：

![路由器的写法](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416112831.png)

这里就可以实现跟方法1和方法2同样的功能，但是却可以使用更少的代码来实现。

#### 总结

这里就跟官网说的一样

![官网文档总结](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200416113025.png)

如果是使用路由器的写法，代码更少；如果是使用as_view的写法的话结构更为清晰，只不过这里建议如果是要清晰的话建议使用swgger来编写开发文档呢，我认为开发速度和代码的简洁对于开发者来说是比较重要的，对于团队协作的话使用swgger来编写文档还可以写上自己的开发思路。