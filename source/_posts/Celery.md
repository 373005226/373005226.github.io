---
title: Celery异步框架的使用
author: LY
summary: Celery是一个专注于实时处理和任务调度的分布式任务队列,可以把耗时任务使用Celery异步完成，这里记录Celery的使用方法
date: 2019-09-24
categories:
    - 后端
tags:
    - 异步
    - Django
---



## 	Celery是什么

​	`Celery`是一个实时处理和任务调度的分布式任务队列，简单的说所就是分布式应用队列，队列的意思就是把任务放在队列中执行，通常应用是把一些耗时的任务放在队列里面，主任务继续执行，耗时任务在队列中执行。



​	`Celery`本身只是一个任务队列，或者严格的说是管理分布式任务队列的工具，封装了常用的任务队列操作，通过`Celery`来管理任务队列的执行和管理，`celery`本身不含消息服务，它使用第三方消息服务来传递任务，目前，`celery`支持的消息服务`RabbitMQ`,`redis`甚至是数据库，`redis`是最佳选择，RabbitMQ([http://docs.celeryproject.org/en/latest/getting-started/brokers/rabbitmq.html#broker-rabbitmq](http://docs.celeryproject.org/en/latest/getting-started/brokers/rabbitmq.html#broker-rabbitmq))、Redis([http://docs.celeryproject.org/en/latest/getting-started/brokers/redis.html#broker-redis](http://docs.celeryproject.org/en/latest/getting-started/brokers/redis.html#broker-redis))处理任务队列的方式，两者的使用文档写的很清楚

​	在实际应用中，可以把一些比如邮件注册、手机注册、网站页面静态化等耗时操作交给`Celery`队列中，然后让`Redis`或者`RabbitMQ`去执行。下面我拿我项目中的案例来演示一下，也便于自己加深印象

## Celery演示

这里我拿的都是我最熟悉的`Django`框架和`redis`来演示

`首先要导入Celery包`

一般第三方的处理文件都会专门放到一个文件夹里面，我的直接命名`Celery_tasks`

```python
from celery import Celery
app = Celery('任务名字', broker='redis://你的服务器IP/数据库名')

@app.task
def 你的要运行的耗时任务:
    pass
```

直接使用装饰器就可以使得普通的方法变成异步方法

上面的是uwsgi使用的服务器的代码，在运行异步方法的服务器还要加上这一行代码， 整体代码如下：

```python
from celery import Celery
app = Celery('任务名字', broker='redis://你的服务器IP/数据库名')

import os
import django
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'dailyfreshshop.settings')	#dailyfreshshop是我的项目名
django.setup()

@app.task
def 你的要运行的耗时任务:
    pass
```

把原本的同步的方法转换为`Celery`异步的代码运行即可



如果你在linux系统上运行Celery代码，就在linux服务器端运行`celery -A celery_tasks.tasks worker -l info `

如果是windows系统上运行Celery代码，就运行`celery -A celery_tasks.tasks worker -l info -P eventlet`

