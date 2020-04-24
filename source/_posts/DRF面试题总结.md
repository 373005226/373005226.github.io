---
title: DRF面试题总结
date: 2020-04-23
author:  Mr、G
summary: 介绍了DRF的面试题
categories:
    - 后端
tags:
    - python面试题
---

> 本文转载于https://www.cnblogs.com/gfhh/p/12103640.html#d20

### 1.drf组件提供的功能

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

### 2.drf继承过哪些视图类？以及他们之间的区别？

 **第一种：`APIView`**
     第一种遵循了CBV的模式，里面的功能比较多但是需要自己写的代码也有很多
     提供了免除csrf认证，版本处理、认证、权限、节流、解析器、筛选器、分页、序列化、渲染器

 **第二种：`ListAPIView,RetrieveAPIView,CreateAPIView,UpdateAPIView,DestroyAPIView`**
     第二种则在第一种的基础上，封装了许多我们需要自己的写的代码，许多功能的实现只需要给专属的变量名赋值就可以实现该功能

 **第三种：`GenericViewSet、ListModelMixin,RetrieveModelMixin,CreateModelMixin,UpdateModelMixin,DestroyModelMixin`**
     第三种则重构了`APIView`中的`as_view()`方法，结合请求方法和不同`Mixin`类的方法名从而进行执行不同的功能。与前面两种最主要的区别是url路由中`as_view()`方法中需要传值。
     目前使用的主要目的是把第二种的bug(查询全部数据的功能和查询单个数据的功能无法在一个类中实现)实现在一个类中！

### 3.drf版本的实现过程？

```python
# drf自带的版本类
     "DEFAULT_VERSIONING_CLASS": "rest_framework.versioning.URLPathVersioning",
 # 允许出现的版本
     "ALLOWED_VERSIONS": ['v1', 'v2'],
 # 如果没有传版本，可以使用默认版本
     default_version = api_settings.DEFAULT_VERSION
 # 设置url中获取版本的变量，默认是version
     version_param = api_settings.VERSION_PARAM
```

当前端来请求时，执行了`as_views()`方法，如果设置了全局版本或者进入了设置了版本的功能函数，则会先执行`APIView`类中的`dispatch`方法，之后再执行`initial`方法，然后进入了`self.determine_version`方法，

里面会先判断是否有`versioning_class`，如果没有就返回`(None,None)`，就代表没有版本，如果有就执行`versioning_class(URLPathVersioning)`类中的`determine_version`方法,它会返回版本，里面会判断

如果获取到的`version`为空则返回默认版本，并且还要判断版本是否存在允许出现的版本列表中，返回版本之后，再把版本号和版本类分别赋值给`request.version`和`request.versioning_scheme`

##### ![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184427876-956432096.png)

##### ![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184449951-352038703.png)

##### ![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184511493-333740807.png)

### 4.drf组件认证的实现过程？

当用户进行登录的时候，运行了登录类的`as_view()`方法，

1. 进入了`APIView`类的`dispatch`方法
2. 执行了`self.initialize_request`这个方法，是重定义`request`，并且得到了自己定义的认证类对象
3. 执行`self.initial`方法中的`self.perform_authentication`，里面运行了`user`方法
4. 执行了`user`方法里面的`self._authenticate()`方法
5.  然后执行了自己定义的类中的`authenticate`方法，自己定义的类继承了`BaseAuthentication`类，里面有  `authenticate`方法，如果自己定义的类中没有`authenticate`方法会报错。
6. 把从`authenticate`方法得到的`user`和`auth`赋值给`user`和`auth`方法
7. 这两个方法把`user`和`auth`的值赋值给了`request.user`：是登录用户的对象`,request.auth`：是认证的信息字典



##### ![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184530291-417601024.png)

##### ![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184545939-435067623.png)

![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184603205-521483617.png)

![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184621343-1161823402.png)

![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184634923-915787997.png)

![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184648677-892449560.png)

##### ![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184708498-411230673.png)

![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184719526-1079270113.png)

### 5.drf组件权限的实现过程？

 当用户执行一个业务的时候，运行了`as_view`方法

1. 进入了`APIView`类的`dispatch`方法
2. 进入`self.initial`方法中的`self.check_permissions(request)`方法
3. 里面执行了for循环，把每个权限类实例化对象，
4. 执行自己定义的权限类里面的`has_permission`方法，里面会判断`request.user`是否存在
5. 不存在就返回`False`，存在就返回`True`
6. 之后执行`self.permission_denied`报错方法，返回的是`False`就报错，可以自定义报错信息，在`has_permission`方法中写`message = {"status": False, "error": "登录成功之后才能评论"}`，    就实现了自定义报错
7. 如果返回的是`True`就让他进入功能



##### ![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184747222-829444943.png)

![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184800627-1337250377.png)

![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184815886-1003379064.png)

![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184829968-1036353484.png)

### 6.drf组件中节流的实现方式？

```python
匿名用户通过ip地址来控制访问频率，已登录用户通过id来控制
 首先要设置配置文件：
 # 也可以设置全局，
     "DEFAULT_THROTTLE_CLASSES":["rest_framework.throttling.AnonRateThrottle",]
 # 设置访问频率——一分钟10次
     "DEFAULT_THROTTLE_RATES": {
             "anon":"10/m"
         }
 - 实现原理
     把所有登录记录时间放在一个列表中，当用户请求网页的时候，用现在的时间减去约束的时间间隔，然后把小于这个时间记录排除，再计算出时间间隙的记录条数，     
     如果其中的条数小于规定的条数则可以访问并且把当前时间添加进列表中，如果大于或等于则不让其访问。
 - 具体流程
     当用户请求网页的时候，后台允许该界面的url中的as_views()，运行源码的APIView中的dispatch方法，运行initial方法，里面的check_throttles方法，    
     循环运行节流类中的allow_request方法，但是AnonRateThrottle等类中没有，去执行SimpleRateThrottle类中的allow_request方法，里面就是实现原理中的代码，     
     如果可以访问返回True，如果不让访问则返回False，之后返回check_throttles，如果是False则运行SimpleRateThrottle类中的wait方法得到需要等待的时间在页面上显示！
```

##### ![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184919186-474514373.png)

![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123184953997-1674626370.png)

![img](https://img2018.cnblogs.com/common/1674415/201911/1674415-20191123185019005-840831653.png)

### 7.什么是jwt？ 它的优势是什么？

```python
 jwt的全称是json web token， 一般用于用户认证
 jwt的实现原理：
     - 用户登录成功之后，会给前端返回一段token。
     - token是由.分割的三段组成。
         - 第一段header：类型+算法+base64url加密
         - 第二段paylod：用户信息+超时时间+base64url加密
         - 第三段sign：hs256（前两段拼接）加密 + base64url
     - 以后前端再次发来信息时
         - 超时验证
         - token合法性校验
 优势：
     - token只在前端保存，后端只负责校验。
     - 内部集成了超时时间，后端可以根据时间进行校验是否超时。
     - 由于内部存在hash256加密，所以用户不可以修改token，只要一修改就认证失败。
```

### 8.反射

```python
应用场景：
 1、django中的View类的dispatch通过接收到的请求方法变为小写从而使用反射得到类中的相对应方法，比如get方法。
 class View(object):
     def dispatch(self, request, *args, **kwargs):
         # Try to dispatch to the right method; if a method doesn't exist,
         # defer to the error handler. Also defer to the error handler if the
         # request method isn't on the approved list.
         if request.method.lower() in self.http_method_names:
             handler = getattr(self, request.method.lower(), self.http_method_not_allowed)
         else:
             handler = self.http_method_not_allowed
         return handler(request, *args, **kwargs)
 
 
 2、django中的settings配置文件的源码也使用了反射，并且结合了
 import importlib
 self.SETTINGS_MODULE = settings_module  # 获取到settings文件的路径
 mod = importlib.import_module(self.SETTINGS_MODULE)  # 通过importlib.import_module获取到settings文件对象
 for setting in dir(mod): # 循环获取到settings文件对象里面的属性
     if setting.isupper(): # 得到大写的属性名
     setting_value = getattr(mod, setting)  # 得到属性值比如中间件等其他配置属性
```

### 9.跨域问题

```
 CORS（跨域资源共享）：是一种使用额外的 HTTP 头部来允许浏览器可以在一个不同域的网站内获取另一个域下的服务器资源的机制。
 
 本质是设置响应头。
```

### 10.基于django的fbv和cbv都能实现遵循restful规范的接口

```
 FBV:
 def user(request):
     if request.method == "GET":
     pass
 
 CBV:
 class UserView(View):
     def get():
         pass
     def post():
         pass
```

FBV和CBV的区别：
 FBV顾名思义就是函数处理请求，代码冗余比较多，不是面向对象编程
 CBV则是使用类中的不同方法来处理请求，迎合了python所推崇的面向对象编程思想。
 相比FBV的优点：

1. 提高了代码的复用性，可以使用面向对象的计算，比如`Mixin(多继承)`
2. 可以用不同的函数针对不同的`http`请求方法处理，而不是通过过多的if判断，提高了代码的可读性