---
title: Restframework之权限设置
author: LY
date: 2019-10-31
summary: 介绍了Restframework的权限设置
categories:
    - 后端
tags:
    - Django
    - Restful
---

## 第一步：增加显示操作人

查看`API`的这个界面默认是要登陆的，因为我上面我使用了`admin`账号去登陆，所以i才可以显示这个页面

但是默认的是所有账号登陆进去都可以对`API`进行增删改查，这个完全不符合逻辑。正常的逻辑是谁创建的才能谁去改，没有身份验证进去的只能读取操作。 

如图，是我去数据库把我的`session`信息删掉之后再查看API的结果

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191031103421125.png)

依照官网的文档演示步骤，要先在`model`创建一个外键字段

```python
......
class Publish(models.Model):
    name=models.CharField(max_length=20,verbose_name="出版社名称",unique=True)
    address=models.CharField(max_length=128,verbose_name="地址")
    operator = models.ForeignKey('auth.User', on_delete=models.CASCADE)

......
```

因为添加了一个字段，所以要进行数据库的迁移：`makemigrations` -> `migrate`

这里因为添加了一个字段，而之前的字段没有这个字段的默认值，所以会出现错误，要把默认值赋值为1即可

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191031104219944.png)

使用如上操作之后在数据库就已经生成了新的`operator`字段了，所以接下来要对序列化的文件也添加这个字段

```python
class Publishserializer(serializers.ModelSerializer):
    class Meta:
        model=Publish
        fields=(
            "id",
            "name",
            "address",
            "operator"
        )
        ......
```

再登陆`admin`账号查看`API`得到的结果应该如下：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191031104640419.png)

这个得到的`operator`的结果都为`1`是当然没问题的，`ID`为`1`就是代表`admin`账号，但是我们要显示的是这个`ID`的用户名

所以我们要在序列化文件里面添加字段：`operator=serializers.ReadOnlyField(source="operator.username")`

```python
class Publishserializer(serializers.ModelSerializer):
    operator=serializers.ReadOnlyField(source="operator.username")
    class Meta:
        model=Publish
        fields=(
            "id",
            "name",
            "address",
            "operator"
        )
```

## 第二步：权限设置

关于权限设置在官网都有详细的说明

![权限说明文档](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191031105427042.png)

依照文档的说法，我们要在视图文件里面添加一个字段，使得未登录的用户只具有读取权限，登录的用户才具有读写权限：

```python
from rest_framework import permissions
....
class Publish_list(generics.ListCreateAPIView):
    queryset = Publish.objects.all()
    serializer_class = Publishserializer
    permission_classes = (permissions.IsAuthenticatedOrReadOnly,)

class PublisherDetail(generics.RetrieveUpdateDestroyAPIView):
    queryset = Publish.objects.all()
    serializer_class = Publishserializer
    permission_classes = (permissions.IsAuthenticatedOrReadOnly,)
```

去演示一下，会发现未登录用户只有读取权限，登陆的用户才有写权限

这个`permissions`字段有好几种，在文档的API的权限设置详细文档里面有说明

![权限设置详细文档](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191031110534344.png)

在这里因为我要设置的是谁录入的`API`谁才具有读写权限，所以这里我要用的是[对象级别权限](http://www.sinodocs.cn/tutorial/4-authentication-and-permissions.html#object-level-permissions)

如图所示：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191031111206500.png)

依照文档的说法，我们要新建一个`permissions.py`文件

**permissions.py**

```python
from rest_framework import permissions


class IsOwnerOrReadOnly(permissions.BasePermission):
    """
    自定义权限只允许对象的权限来去编辑他
    """

    def has_object_permission(self, request, view, obj):
        # 读取权限允许任何请求
        # 所以我们总是允许Get，HEAD和OPTIONS请求
        if request.method in permissions.SAFE_METHODS:
            return True

        # 只有该出版社的所有者才允许去读写
        return obj.operator == request.user
```

然后再去视图函数里面继承`permissions`的这个类

 之后我们再去创建一个管理员账号`admin2`看看能不能修改`admin`的API

![随便找的图](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191121111047.png)

哦吼，大功告成

## 第三步：新建保存operator值的方法

还有最后的一个问题，假如我们新建一个用户，就拿刚刚创建的`admin2`来说，我创建一个新的`API`数据，但是我的`operator`的`ID`是没有的。

我之前创建的`admin`账号是我模型迁移之前设置了默认的值`1`，但是我新创建的`admin2`没有`operator`的值，所以我们还要创建一个类方法去编写

我通俗的将不太好懂，这个原理如下：

使用`post`去创建一个新的`API`的时候是通过序列化去添加的，而我的序列化代码如下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/微信图片_20191031113938.png)

序列化没有自己的`save`方法，默认存储方法是在`ModelSerializer`里面的，也就是在`Model`里面保存，但是我新建的用户`admin2`添加一个字段，他的`operator_id`都不知道去哪里找，如果使用`admin2`去添加`API`数据的话，会出现如下情况：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191031114249204.png)

对于这种情况，文档的说明如下：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191031114508.png)

即是让我们在视图文件创建一个`.perform_create`方法

**views.py**

```python
class Publish_list(generics.ListCreateAPIView):
    queryset = Publish.objects.all()
    serializer_class = Publishserializer
    permission_classes = (permissions.IsAuthenticatedOrReadOnly,IsOwnerOrReadOnly)

    def perform_create(self, serializer):
        serializer.save(operator=self.request.user)
```

好了现在再去试一下使用admin2去post请求

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191031114833.png)

完美，搞定



## 留坑

项目开发可能更多用到的是token验证，所以这里留个坑，到时候我做个项目案例回来填

在项目开发中，实际上用到的是比如购物车结算，没有登录的用户是无法进行结算的，所以一般要使用json.token去编写