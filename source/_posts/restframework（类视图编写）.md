---
title: Restframework之类视图的编写
author: LY
date: 2019-10-30
summary: 介绍了Restframework的请求、响应、视图的编写
categories:
    - 后端
tags:
    - Django
    - Restful
---

## Restframework请求和响应

`Restframework`扩展了`Django`默认的 `Request` 对象，提供了更加的灵活解析，除此之外`Restframework`还引入了一个 `Response响应对象`和`状态码`，如翻译文档截图所示：

![请求响应说明文档](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030161355293.png)

状态码呢，平常我们得到的结果404什么的我们都知道是找不到界面的意思了，但是`Restframework`在返回状态码的基础上还给我们返回一个说明

## Restframework编写 API视图 

### 函数式编写API视图

`Restframework`的视图分为函数视图和类视图，这里我就直接用类视图来演示了，因为我开发`view视图`函数的时候都是用继承`from django.views.generic.base import View`来开发的，关于通过使用 `@api_view 装饰器`来对`API视图`进行开发官网有较明确的说明

![函数编写方法说明文档](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030162340135.png)

### 类的方法来编写API视图

![类方法说明文档](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030163246255.png)

官网有详细说明的案例，这里我就直接编写视图代码了

**views.py**

```python
from .models import Publish
from rest_framework.views import APIView
from .serializers import Publishserializer
from rest_framework.response import Response
from rest_framework import status
from django.http import Http404


class Publish_list(APIView):
    """
        列出所有的出版社或者创建一个新的出版社
    """
    def get(self, request, format=None):
        publish = Publish.objects.all()
        serializer = Publishserializer(publish, many=True)
        return Response(serializer.data)

    def post(self, request, format=None):
        serializer = Publishserializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class PublisherDetail(APIView):
    """
    具体的出版社的查看、修改和删除视图
    """
    def get_object(self, pk):
        try:
            return Publish.objects.get(pk=pk)
        except Publish.DoesNotExist:
            raise Http404

    def get(self, request, pk, format=None):
        publish = self.get_object(pk)
        serializer = Publishserializer(publish)
        return Response(serializer.data)

    def put(self, request, pk, format=None):
        publish = self.get_object(pk)
        serializer = Publishserializer(publish, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk, format=None):
        publish = self.get_object(pk)
        publish.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

然后去编写路由

**urls.py**

```python
from django.conf.urls import url
from django.contrib import admin
from Book.views import Publish_list,PublisherDetail

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^publish/$',Publish_list.as_view()),
    url(r'^publish/(?P<pk>[0-9]+)/$',PublisherDetail.as_view())
]
```

配置完成，直接去访问地址

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030170643572.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030170732515.png)

## 对类方法编写API视图进行优化

### mixins类说明

对上面我演示的案例来说，单单只有一个类方法来编写，就要使用到`get_object`、`put`、`delete`、`post`等方法，如果有很多个API方法，就要使用到很多个重复的方法，所以`Restframework`整合了这些方法成为一个类，把重复的方法都整合起来，我们只需要修改表就可以了，这样就可以大大的**减少代码量**了

![整合的方法](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030171934693.png)

来，让我们康康`mixins`的源码

![部分源码](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030174711526.png)

根据源码所示，`model`的表名要赋值给`queryset`，序列化后的字段要赋值给`serializer_class`，不然就会报错

`mixins`类里面封装了很多的方法，我们需要用到什么方法的时候就继承里面的哪个方法即可

### 使用mixins重写view方法

**views.py**

```python
from .models import Publish
from .serializers import Publishserializer
from rest_framework import mixins
from rest_framework import generics


class Publish_list(mixins.ListModelMixin, mixins.CreateModelMixin, generics.GenericAPIView):
    """
        列出所有的出版社或者创建一个新的出版社
        """
    queryset = Publish.objects.all()
    serializer_class = Publishserializer

    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)


class PublisherDetail(mixins.RetrieveModelMixin,
                      mixins.UpdateModelMixin,
                      mixins.DestroyModelMixin,
                      generics.GenericAPIView):
    """
    具体的出版社的查看、修改和删除视图
    """
    queryset = Publish.objects.all()
    serializer_class = Publishserializer

    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)

    def put(self, request, *args, **kwargs):
        return self.update(request, *args, **kwargs)

    def delete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)
```

拿修改后的代码跟原来的一比，代码的逻辑少了很多，`queryset`和`serializer_class`是`mixins`里面定义好的字段，所以我们把值赋值给这两个字段即可

再访问 http://127.0.0.1:8000/publish/ 试试

![使用mixins重写后访问](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030174023961.png)

依然没有任何问题

## mixins之再优化

在上述的mixins方法中已经帮我们整合了大部分的方法，已经减少了我们原本的大部分代码，但是还是有优化的地方

我们每次要调用很多个mixins方法的时候，一次要继承三四个类，看起来十分不美观

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030175530143.png)

你想到的Restframework早就想到了，而且还帮你做出了优化

我拿截图的这两个代码来说明，我们一次要继承四个类，所以我们可以直接去继承一个整合类来代替四个类

```python
class Publish_list(generics.ListCreateAPIView):
    """
        列出所有的出版社或者创建一个新的出版社
    """
    queryset = Publish.objects.all()
    serializer_class = Publishserializer

class PublisherDetail(generics.RetrieveUpdateDestroyAPIView):
    """
    具体的出版社的查看、修改和删除视图
    """
    queryset = Publish.objects.all()
    serializer_class = Publishserializer
```

这个整合的代码的名称很简便易懂，让我们康康源码

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030175816419.png)

哦吼，里面的代码不就是我们刚刚写的吗，直接帮我们把要重复的代码又整合一遍，把原本要十几行代码整合到了两行，我们直接写字段就好了

