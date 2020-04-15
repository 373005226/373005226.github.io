---
title: Restframework之基本配置
author: LY
date: 2020-3-21
summary: 介绍了Restframework的跨域、限速、缓存、自动生成文档、APIROOT等基本配置
categories:
    - 后端
tags:
    - Django
    - Restful

---

> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;这里是对之前的restframwork的简述和对restframwork的部分功能配置做一个总结
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;本文的案例、讲解均为慕课网天天生鲜项目后的总结
> 

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

如果单单是只要get方法等，到这里就已经足够了，但是如果是需要分页等需求。





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

## viewsets

viewsets是比mixins更高级的写法，也是我经常使用的写法

点击查看里面结构的源码，如下图

![图33 viewset.GenericViewSet源码](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415202205.png)

点击`ViewSetMixin`一看，里面最重要的部分就是重写了`as_view`方法

![ViewSetMixin源码](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415202332.png)

如果是使用viewset的话，继承的`generics.GenericAPIView`里面没有get方法，所以这里要用回mixins的方法

如果只是把GenericAPIVIew和mixins写上去的话，是没办法吧两者绑定起来的：

![案例图](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415203721.png)



把两者绑定起来有个很关键的步骤，这里看向官方文档：

这里需要在urls里面配置，这里配置有两种写法：

### 方法1：一个个方法的绑定

即get对应list方法、post对应create方法

![官网文档方法1](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415204040.png)

但是如果是这样写，那么在urls里面就会堆积大量的重复代码

### 方法2：使用路由器的写法

![官网文档方法2：路由器的写法](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415203928.png)



所以最终的写法如下：

![最终写法](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200415203119.png)

## ViewSets的好处