---
title: Restframework之编写API文档
author: LY
date: 2019-10-31
summary: 介绍了Restframework的如何编写API文档，显示概要信息
categories:
    - 后端
tags:
    - Django
    - Restful
---

## 概要文档

`Restframework`有一个显示概要文档的功能，根据文档显示

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191031180132.png)

根据文档的说明，我就直接修改路由了

**urls.py**

```python
from django.conf.urls import url,include
from django.contrib import admin
from Book.views import *
from rest_framework.routers import DefaultRouter
from rest_framework.schemas import get_schema_view


router = DefaultRouter()
router.register(r'Book',BookViewSet)
router.register(r'Publish',PublishViewSet)
schema_view = get_schema_view(title='Pastebin API')

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^',include(router.urls)),
    url('^schema/$', schema_view),
]
```

根据文档的说明，直接执行是可以的，但是我这里还是出了一些问题，比如：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191101095757.png)

这个问题谷歌搜索后的最佳答案是这样的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191101140649.png)

emmm......虽然说知道了这个是什么原因也没啥用，我们直接升级一下`Django`和`Restframework`的版本就好了，新一点的版本没有这个bug

比如`Django2.0`和`Restframework3.7.7`及以上的版本不会出现这个问题

我更新了下版本，按照原来的配置，是可以运行的，如图概要文档已经生成

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191101095810.png)

## API文档

`Restframework`自带一个`API`总和的说明文档，我们只需导入一个包和配置路由就可以直接使用

**urls.py**

```python
......
from rest_framework.documentation import include_docs_urls
......
urlpatterns = [
    url(r'^docs/',include_docs_urls(title="图书管理系统")),
......
```

然后我们直接访问 http://127.0.0.1:8000/docs/ 就可以看到我们的文档已经做好了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191101100700.png)

