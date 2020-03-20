---
title: Restframework之入门
author: LY
date: 2019-10-29
summary: 简单介绍了Restframework的简单使用
categories:
    - 后端
tags:
    - Django
    - Restful
---

> 官方文档（英文）： [https://www.django-rest-framework.org/](https://www.django-rest-framework.org/ ) 
>
> 官方文档（翻译）： [http://www.sinodocs.cn/](http://www.sinodocs.cn/ ) 
>
> 官网文档（未翻译全）（推荐）： https://q1mi.github.io/Django-REST-framework-documentation/ 
>
> 参考文档： [https://www.cnblogs.com/chenxuming/articles/9370009.html#_label2](https://www.cnblogs.com/chenxuming/articles/9370009.html#_label2 ) 

> restful系列博客是在学习51cto的课程和在观看完官方文章之后的学习总结，自己重新做了一个案例来说明

## Django Restframework是什么

`Django Restframework`是`Django`中可以 帮助我们快速的开发符合`RESTful`规范的接口的框架 ，`RESTful`是如今开发基本都会采用的规范

### Restful规范

- GET（SELECT）：查询; 从服务器取出资源（一项或多项）。
- POST（CREATE）：新增; 在服务器新建一个资源。
- PUT（UPDATE）：覆盖,全部更新 ; 在服务器更新资源（客户端提供改变后的完整资源）。
- PATCH（UPDATE）：更新;  在服务器更新资源（客户端提供改变的属性）。
- DELETE（DELETE）：删除; 从服务器删除资源。
- HEAD：获取资源的元数据。
- OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的。



### Restframework作用

- 路由

  - 可以通过`as_view`传参数,根据请求方式的不同执行响应的方法

  - 可以在url中设置一个结尾类似于.json的数据形式

- 视图

  - 帮助开发者提供了一些类,并在类提供了很多的方法供我们使用 

- 版本控制

  - 在url中设置version参数,用户请求传入参数时,在`request.version`中获取版本,根据版本的不同做不同的处理 

- 认证

  - 写一个类并注册到认证类,在类的authticate方法中编写认证逻辑
    - 认证成功(user,auth)
    - rase AuthticateFaild(..)
    - None 继续

- 权限

  -  写一个类并注册的权限类,在类的`has_permission`方法中写权限逻辑 

-  频率限制 

  -  写一个类并注册的权限类,在类的`allow_requset/wait` 方法中写权限逻辑 
    - 匿名
    - 用户
    - 返回为`True`或者`False`，`False`就要执行`wait`方法

- 解析器

  - 根据ContentType请求头,选择不同的解析器对请求体中的数据进行解析.
    - `Content-Type : url-formedo`
    - `Content-Type :application/json`

- 分页

  - 对从数据库中取到的数据进行分页处理. SQL---> limit offset
    - 根据页码:http://www.luffycity.com/api/v1/student/?page=1&size=10
    - 根据索引:http://www.luffycity.com/api/v1/student/?offset=60(游标位置)&limit=10
    - 根据加密:

- 序列化

  -  对`queryset`序列化以及对请求数据格式校验 

-  渲染器 
  -  根据url中传入的后缀,决定在数据如何渲染到页面上

&emsp;

**.......简单的总结就是，`Django Restframework`可以帮助你前后端分离开发，后台人员通过`Restframework`来开发`API`接口，前端人员写前端的页面（不论是PC还是移动端），到时候整合直接使用后端人员的接口就可以完成整个项目的开发**

&emsp;

接下来我从把`model`的数据转换为json格式来引入`Restframework`的概念

## model数据转换为json形式

在开始演示之前，要先配置好Django的基本配置：编写好model、setting配置、从model插入数据、view视图配置等操作，这里我一一演示一遍

### 演示前配置

先创建一个空文件夹，然后创建项目，如我下图所示

![创建项目](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030104429158.png)

然后使用`pycharm`打开项目，配置好数据库、插入`Book`(我拿来演示的app)到`INSTALLED_APPS`，以下是我的配置

#### setting.py

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'Book',
    'rest_framework',
]

REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAdminUser',
    ],
    'PAGE_SIZE': 10
}
......

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'restful_demo',
        'USER': 'root',
        'PASSWORD': '',
        'HOST': '127.0.0.1',
        'PORT': '3306',
    }
}
```

#### 根目录`__init__`文件

在我的`restful_test(项目根目录)`文件夹里面的的`__init__.py`文件，因为是python3版本， 所以要配置一下mysql

```python
import pymysql
pymysql.install_as_MySQLdb()
```

#### 创建model模型字段

在我创建的Book文件夹里面的model.py添加我要演示的字段

```python
class Publish(models.Model):
    name=models.CharField(max_length=20,verbose_name="出版社名称",unique=True)
    address=models.CharField(max_length=128,verbose_name="地址")

    def __str__(self):
        return self.name

    class Meta:
        verbose_name="出版社"
        verbose_name_plural=verbose_name
```

然后对生成迁移文件和写入数据库

`python manage.py makemigrations`->`python manage.py migrate`

#### 创建管理员为model添加数据

 `python manage.py createsuperuser`创建好管理员的用户名和密码

然后在`admin.py`里面注册Book

```python
from django.contrib import admin
from .models import Publish
# Register your models here.
admin.site.register(Publish)
```

这里我添加好了三条数据

![Book数据](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030111244863.png)

以上你要用`Django shell`添加也行

到这里演示前的准备都已经配置好了，接下来的都是配置`view`视图方法

### 方法一：使用字典的方式转换为json

```python
from django.shortcuts import render
from django.http import HttpResponse
from django.views.generic.base import View
from .models import Publish
import json
# Create your views here.

class Publish_list(View):
    def get(self,request):
        queryset=Publish.objects.all()
        data=[]
        for i in queryset:
            p_tmp={
                "name":i.name,
                "address":i.address
            }
            data.append(p_tmp)
        return HttpResponse(json.dumps(data),content_type="application/json")

    def post(self,request):
        pass
```

然后配置一下路由

```python
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^publish/',Publish_list.as_view())
]
```

访问 http://127.0.0.1:8000/publish/ 

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030112748744.png)

可以看到model的数据已经转换为json格式了

### 方法二：使用Django自带的model_to_dict方法

方法二其实是对方法一进行优化，Django默认自带了一个model_to_dict方法，可以帮助我们把model转换为一个字典

```python
from django.shortcuts import render
from django.http import HttpResponse
from django.views.generic.base import View
from .models import Publish
from django.forms.models import model_to_dict
import json
# Create your views here.

class Publish_list(View):
    def get(self,request):
        queryset=Publish.objects.all()
        data=[]
        for i in queryset:
            data.append(model_to_dict(i))
        return HttpResponse(json.dumps(data),content_type="application/json")

    def post(self):
        pass
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030115622712.png)

方法二得出的结果还是一毛一样的

### 方法三：使用Django自带的序列化转换成json

```python
from django.shortcuts import render
from django.http import HttpResponse
from django.views.generic.base import View
from .models import Publish
from django.core import serializers
# Create your views here.

class Publish_list(View):
    def get(self,request):
        queryset=Publish.objects.all()
        data=serializers.serialize("json",queryset)

        return HttpResponse(data,content_type="application/json")
    def post(self):
        pass
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030120017403.png)

### 总结

这个跟方法一和方法二的结果多了一个`model`和`pk`字段，但是方法三比其他两个方法更为简洁，更少的代码也更容易理解

如果单单只要提供信息的话使用方法三是完全足够的，从`model`读取信息转换为`json`格式方法三完全可以胜任。开发不仅仅只是把`model`转换为`API`，还要把`API`转换成`model`里面的数据

## Restframework的使用

### 初识：自定义序列化的类

`Restframework`的用法是跟`Django`的`form`表单的用法很相似

要先在创建一个`serializers`文件，这个文件里面存储序列化的字段

 **serializers .py**

```python
from rest_framework import serializers


class Publishserializer(serializers.Serializer):
    id=serializers.IntegerField(read_only=True)
    name= serializers.CharField(max_length=32)
    address=serializers.CharField(max_length=128)
```

以上字段已经完成了字段的序列化，在前面说的跟form表单验证的方法一样，也有个验证的方法，如果直接把字段序列化进去就会造成不安全的问题，如翻译文档所示：

![翻译文档](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030142657094.png)

所以我们要重写一个`create`和`update`方法

 **serializers .py**

```python
from rest_framework import serializers
from .models import Publish


class Publishserializer(serializers.Serializer):
    id=serializers.IntegerField(read_only=True)
    name= serializers.CharField(max_length=32)
    address=serializers.CharField(max_length=128)

    def create(self, validated_data):
        #这里不用管validated_data是什么，只需要按照官网的方法所示，返回**validated_data就可以保证序列化的字段经过验证
        return Publish.objects.create(**validated_data)

    def update(self, instance, validated_data):
        instance.name=validated_data.get("name",instance.name)
        instance.address=validated_data.get("address",instance.address)
        instance.save()
        return instance
```

然后我们使用`Django shell`测试一下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030143656397.png)

其中的`is_vaild`方法都跟`form`表单使用方法一样，然后再刷新网页看看数据

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030143900759.png)

如图所示，就增加了我刚刚写的数据

### 使用：在view里面添加序列化方法

**views.py**

```python
from django.shortcuts import render
from django.http import HttpResponse
from django.views.generic.base import View
from .models import Publish
from django.core import serializers
from .serializers import Publishserializer
import json
# Create your views here.

class Publish_list(View):
    def get(self,request):
        queryset=Publish.objects.all()
        serializer=Publishserializer(queryset,many=True)    #many=True就是多个对象，要去遍历出来
        return HttpResponse(json.dumps(serializer.data),content_type="application/json")


    def post(self):
        pass
```

### 优化：继承model的字段

跟`form`一样，`form`可以继承`model`的字段，`serializers` 当然也可以，已经在`model`里面创建的字段，当然没有必要重新写一遍了，所以现在要改写一下`serializers .py`的代码

```python
from rest_framework import serializers
from .models import Publish


class Publishserializer(serializers.ModelSerializer):
    class Meta:
        model=Publish
        fields=(
            "id",
            "name",
            "address"
        )

    def create(self, validated_data):
        #这里不用管validated_data是什么，只需要按照官网的方法所示，返回**validated_data就可以保证序列化的字段经过验证
        return Publish.objects.create(**validated_data)

    def update(self, instance, validated_data):
        instance.name=validated_data.get("name",instance.name)
        instance.address=validated_data.get("address",instance.address)
        instance.save()
        return instance
```

其余配置不变，输入配置好的路由地址 http://127.0.0.1:8000/publish/ ，依然可以访问到同样的数据

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/image-20191030145827519.png)

