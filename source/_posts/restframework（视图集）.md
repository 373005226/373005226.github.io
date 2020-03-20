---
title: Restframework之视图集
author: LY
date: 2019-10-31
summary: 介绍了Restframework的视图集
categories:
    - 后端
tags:
    - Django
    - Restful
---

> 视图集这个其实用不用意义不大，文档中做了一个亲切的比喻，就像编写视图函数的时候是使用函数的形式去编写还是通过类视图的形式去编写一样，主要还是为了减少代码

## 视图集

视图集的主要作用就是减少代码，就像我刚刚的代码，很多重复的，如图所示：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191031164554.png)

要写多少个字段就要重复多少个`queryset`、`serializer_class`和`permissions_classes`字段

视图集的主要作用就是对重复的代码做一个整合

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191031164925.png)

依照文档所说，只需要定义一个`ViewSets`就可以有`Book_list`和`Book_Detail`两个字段的功能

**views.py**

```python
# class Book_list(generics.ListCreateAPIView):
#     queryset = Book.objects.all()
#     serializer_class = Bookserializer
#     permission_classes = (permissions.IsAuthenticatedOrReadOnly,)
#
#
# class Book_Detail(generics.RetrieveUpdateDestroyAPIView):
#     queryset = Book.objects.all()
#     serializer_class = Bookserializer
#     permission_classes = (permissions.IsAuthenticatedOrReadOnly, )

#上面注释的代码都可以直接删了~~~

class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = Bookserializer
    permission_classes = (permissions.IsAuthenticatedOrReadOnly,)
```

然后需要在`urls.py`里面修改成视图集的字段，要改动的地方有点多，所以我直接丢出代码了

```python
from django.conf.urls import url
from django.contrib import admin
from Book.views import *

book_list = BookViewSet.as_view({
    'get': 'list',
    'post': 'create'
})
book_detail = BookViewSet.as_view({
    'get': 'retrieve',
    'put': 'update',
    'patch': 'partial_update',
    'delete': 'destroy'
})

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^$',api_root),
    #Publish的API
    url(r'^publish/$',Publish_list.as_view(),name='publish-list'),
    url(r'^publish/(?P<pk>[0-9]+)/$',PublisherDetail.as_view(),name='publish-detail'),
    #Book的API
    # url(r'^Book/$', Book_list.as_view(),name='Book-list'),
    # url(r'^Book/(?P<pk>[0-9]+)/$', Book_Detail.as_view(),name='Book-detail'),

    # 使用BookViewSet(视图集)的API
    url(r'^Book/$', book_list,name='Book-list'),
    url(r'^Book/(?P<pk>[0-9]+)/$', book_detail,name='Book-detail'),
]
```

但是看看这个`urls.py`代码，在`views`里面减少了我们要的代码量，却在`urls`里面增加了我们的代码量~~~所以接下来要引入路由器的概念

## 路由器

使用视图集一般我们都会配套使用路由器

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191031171940.png)

依照官网的说法，我们要实例化一个`DefaultRouter`类

**urls.py**

```python
from django.conf.urls import url,include
from django.contrib import admin
from Book.views import *
from rest_framework.routers import DefaultRouter

router = DefaultRouter()
router.register(r'Book',BookViewSet)

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^',include(router.urls)),
    #这里的API先注释，因为api_root里面的publish没有使用视图集注册
    # url(r'^API/$',api_root),
    #Publish的API
    url(r'^publish/$',Publish_list.as_view(),name='publish-list'),
    url(r'^publish/(?P<pk>[0-9]+)/$',PublisherDetail.as_view(),name='publish-detail'),
    #Book的API
    # url(r'^Book/$', Book_list.as_view(),name='Book-list'),
    # url(r'^Book/(?P<pk>[0-9]+)/$', Book_Detail.as_view(),name='Book-detail'),

    # 使用BookViewSet(视图集)的API
    # url(r'^Book/$', book_list,name='Book-list'),
    # url(r'^Book/(?P<pk>[0-9]+)/$', book_detail,name='Book-detail'),
]
```

运行试试

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191031174926.png)

也是可以运行的

然后再把剩下的那个`publish`也去使用视图集的方式注册一下

**views.py**

```python
class BookViewSet(viewsets.ModelViewSet):
    queryset = Book.objects.all()
    serializer_class = Bookserializer
    permission_classes = (permissions.IsAuthenticatedOrReadOnly,)

class PublishViewSet(viewsets.ModelViewSet):
    queryset = Publish.objects.all()
    serializer_class = Publishserializer
    permission_classes = (permissions.IsAuthenticatedOrReadOnly,)


@api_view(['GET'])
def api_root(request, format=None):
    return Response({
        'publish': reverse('publish-list', request=request, format=format),
        'Book': reverse('Book-list', request=request, format=format)
    })
```

**urls.py**

```python
from django.conf.urls import url,include
from django.contrib import admin
from Book.views import *
from rest_framework.routers import DefaultRouter

router = DefaultRouter()
router.register(r'Book',BookViewSet)
router.register(r'Publish',PublishViewSet)

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^',include(router.urls)),
]
```

然后再运行一下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191031175129.png)

都是完全可以运行的，用最少的代码去运行了同样的功能

但是你要是问我选哪种，我肯定不会用视图集，因为遇到没有读过文档的人，碰到这段代码绝对是看不懂的，而且出了Bug还不好找

最终还是要根据自己的情况去选择使用视图还是视图集