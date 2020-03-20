---
title: Restframework之超链接API
author: LY
date: 2019-10-31
summary: 介绍了Restframework的超链接API
categories:
    - 后端
tags:
    - Django
    - Restful
---

## 演示前配置

拿外键的例子来说，`Book`的外键是`Publish`，点击`Book`的超链接`API`就可以访问到外键的`Publish`

为了方便演示，我这里我就创建一个新的表`Book`，添加一个`model`字段

**models.py**

```python
class Book(models.Model):
    title=models.CharField(max_length=20,verbose_name="图书名称",unique=True)
    publish=models.ForeignKey("Publish",on_delete=True)

    def __str__(self):
        return self.title

    class Meta:
        verbose_name="书籍"
        verbose_name_plural=verbose_name
```

修改完`model`字段要`makemigrations`->`migrate`

然后添加一个新的序列化文件

**serializer.py**

```python
from .models import Publish,Book
......
class Bookserializer(serializers.ModelSerializer):
    #通过publish这个外键去找到publish这个表的name，把原本的那个显示的数字改成出版社的名字
    publish=serializers.StringRelatedField(source="publish.name")
    class Meta:
        model = Book
        fields = (
            "id",
            "title",
            "publish"
        )
```

接下来要编写视图views文件

**views.py**

```python
from .models import Publish,Book
from .serializers import Publishserializer,Bookserializer
.......
class Book_list(generics.ListCreateAPIView):
    queryset = Book.objects.all()
    serializer_class = Bookserializer
    permission_classes = (permissions.IsAuthenticatedOrReadOnly,)


class Book_Detail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Book.objects.all()
    serializer_class = Bookserializer
    permission_classes = (permissions.IsAuthenticatedOrReadOnly, )
```

最后要编写路由方

**urls.py**

```python
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    #Publish的API
    url(r'^publish/$',Publish_list.as_view()),
    url(r'^publish/(?P<pk>[0-9]+)/$',PublisherDetail.as_view()),
    #Book的API
    url(r'^Book/$', Book_list.as_view()),
    url(r'^Book/(?P<pk>[0-9]+)/$', Book_Detail.as_view()),
]
```

运行项目可以看到项目运行成功，且post请求等都可以运行

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191031160320.png)

这篇博客讲的就是要把publish外键字段的弄一个超链接，点击可以查看这个出版社的所有字段

即点击这上那个字段都可以跳转到出版社的API情况

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191031160635.png)

## 配置超链接

配置超链接十分的简单，只需要序列化字段的类修改为`HyperlinkedModelSerializer`和删掉刚刚配置的`publish=serializers.StringRelatedField(source="publish.name")`

**serializers.py**

```python
class Bookserializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Book
        fields = (
            "id",
            "title",
            "publish"
        )
```

**urls.py**

**这里一定要编写name配置，因为如果不配置就不能反向查找到API**

```python
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^$',api_root),
    #Publish的API
    url(r'^publish/$',Publish_list.as_view(),name='publish-list'),
    url(r'^publish/(?P<pk>[0-9]+)/$',PublisherDetail.as_view(),name='publish-detail'),
    #Book的API
    url(r'^Book/$', Book_list.as_view(),name='Book-list'),
    url(r'^Book/(?P<pk>[0-9]+)/$', Book_Detail.as_view(),name='Book-detail'),
]
```

配置完运行项目，可以看到已经配置好了超链接

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191031161638.png)

## 显示所有可用的API

我们需要一个路由，打开可以显示所有可用的API

文档有这个方法的详细说明

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191031162611.png)

依照文档的意思就是要在视图文件里面添加这个字段

**views.py**

```python
from rest_framework.decorators import api_view
from rest_framework.response import Response
from rest_framework.reverse import reverse
......
@api_view(['GET'])
def api_root(request, format=None):
    return Response({
        'publish': reverse('publish-list', request=request, format=format),
        'Book': reverse('Book-list', request=request, format=format)
    })
```

然后我们配置一下路由

**urls.py**

```python
    #api_root的路由
    url(r'^$',api_root),
```

运行项目，可见运行成功

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191031162837.png)

