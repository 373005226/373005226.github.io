---
title: 死磕爬虫之scrapy框架初识
author: LY
date: 2019-10-07
summary: 介绍了scrapy的配置和基本使用
categories:
    - 爬虫
tags:
    - scrapy
---

> 前言
>
> ​		我们原生爬虫需要使用urllib、requests等库去请求指定的网址，然后通过正则表达式、xpath、css选择等去解析网址，得到自己想要的信息，然后再写一个函数存储到数据库或者本地文件中，这样思路虽然说很清晰，但是流程总归而言还是比较麻烦的，而scrapy框架则帮我们封装好了所有的流程，我们只需要编写几行代码就可以爬取我们需要的资源。
>

## scrapy架构图

![scrapy架构图](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/scrapy_all.png)

- `Scrapy Engine`就是爬虫的引擎，负责`Spider`、`ItemPipeline`、`Downloader`、`Scheduler`之间的通讯

- `Scheduler(调度器)`：负责引擎传递过来的request请求，并且按照一定的方式整理排列

- `Downloader（下载器）`：负责下载引擎传递过来的所有request请求，然后将得到的Responses还给引擎，由引擎交给`spiders`处理

- `Downloader Middlewares（下载中间件）`：自定义扩展下载功能的地方，比如需要添加下载的代理等

- `Spider（爬虫）`：负责从Responses分析提取出所要的数据，如果Responses还有需要的URL数据，就把这些数据交给引擎，由引擎传递给`Scheduler(调度器)`去处理

- `Spider Middlewares（Spider中间件）`：一个可以自定扩展和操作`引擎`和`Spider`中间`通信`的功能组件（比如进入`Spider`的Responses;和从`Spider`出去的Requests），这个很少用到

- `Item Pipeline(管道)`：负责处理spiders获取到的item，并且对这些数据进行存储、分析、过滤等处理

  



## scrapy文件配置详解

创建项目：

```
scrapy startproject demo(你的项目名)
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/1570425985011.png)

然后就可以进入刚刚创建的demo项目了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/1570426136366.png)

scrapy.cfg里面是确定项目的配置文件是哪个

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/1570426236269.png)

如图，项目的配置文件就是`demo/setting.py`



项目的所有文件如下：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/1570426417062.png)

`items.py`是根据要爬取的数据的字段进行设置

`pipelines.py`是管道文件，即把爬取下来的数据进行存储和过滤等操作

`setting.py`是核心配置文件

```python
12. BOT_NAME = 'demo'							#项目名字
14. SPIDER_MODULES = ['demo.spiders']			#项目模板位置
15. NEWSPIDER_MODULE = 'demo.spiders'			#新爬虫模板位置
22. ROBOTSTXT_OBEY = True                     	#是否遵守robots协议
25. CONCURRENT_REQUESTS = 32					#爬虫的并发量
30. DOWNLOAD_DELAY = 3							#爬虫延迟
36. COOKIES_ENABLED = False						#是否启用cookie，不想爬虫留下自己的痕迹就不用开启

42. DEFAULT_REQUEST_HEADERS = {					#42-45 默认的请求头
43.   'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
44.   'Accept-Language': 'en',
45. }

49. #SPIDER_MIDDLEWARES = {						#爬虫中间件设置，数字越小优先级越大
50. #    'demo.middlewares.DemoSpiderMiddleware': 543,
51. #}

55. #DOWNLOADER_MIDDLEWARES = {					#下载中间件
56. #    'demo.middlewares.DemoDownloaderMiddleware': 543,
57. #}

67. #ITEM_PIPELINES = {							#管道文件
68. #    'demo.pipelines.DemoPipeline': 300,
69. #}
```

更多的详细配置可见[官方文档](https://scrapy-chs.readthedocs.io/zh_CN/1.0/topics/settings.html#topics-settings-ref)



## 第一个scrapy爬虫

scrapy的所有命令都可以用cmd看到

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/1570517173800.png)

创建爬虫文件：

```
scrapy genspider bilibili(爬虫文件名) https://www.bilibili.com/(要爬虫的网站)
```

刚刚创建的爬虫项目内容如下

```python
# -*- coding: utf-8 -*-
import scrapy


class BilibiliSpider(scrapy.Spider):
    name = 'bilibili'
    allowed_domains = ['https://www.bilibili.com/']					#允许访问的域
    start_urls = ['http://https://www.bilibili.com//']				#开始爬取的网站地址

    def parse(self, response):										#解析方法
        pass
```

现在编写一些请求函数

```python
# -*- coding: utf-8 -*-
import scrapy


class BilibiliSpider(scrapy.Spider):
    name = 'bilibili'
    allowed_domains = ['https://www.bilibili.com/']
    start_urls = ['https://www.bilibili.com/v/game/stand_alone/?spm_id_from=333.6.b_7072696d6172795f6d656e75.41#/']

    def parse(self, response):
        with open("demo.html","wb+") as f:
            f.write(response.body)
```

然后执行命令

```python
scrapy crawl bilibili
```

就可以请求bilibili的页面，然后把源代码写到demo.html文件了



## 将scrapy得到的结果保存到第三方格式

scrapy是默认有xpath解析库的，所以可以直接使用xpath，这里拿传播智客的讲师页面来演示



创建一个爬虫文件

```python
scrapy genspider itcast http://www.itcast.cn/
```

首先先编写要确定爬取的部分

```python
# -*- coding: utf-8 -*-
import scrapy

class ItcastSpider(scrapy.Spider):
    name = 'itcast'
    allowed_domains = ['http://www.itcast.cn/']
    start_urls = ['http://www.itcast.cn/channel/teacher.shtml#ajavaee']

    def parse(self, response):
        teacher_list =response.xpath('//div[@class="li_txt"]')
        teacherItem = []
        for each in teacher_list:
            name=each.xpath('./h3/text()').extract()
            title=each.xpath('./h4/text()').extract()
            info=each.xpath('./p/text()').extract()
			
			print(name)
			print(title)
			print(info)
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/1570469433346.png)

得到如上页面说明爬取的内容正确

然后在`items.py`编写要存储的字段

```python
import scrapy


class DemoItem(scrapy.Item):
    # define the fields for your item here like:
    name = scrapy.Field()
    title= scrapy.Field()
    info = scrapy.Field()
```

然后再编写爬虫文件，将爬取的内容存储到这些字段中

```python
# -*- coding: utf-8 -*-
import scrapy
from demo.items import DemoItem

class ItcastSpider(scrapy.Spider):
    name = 'itcast'
    allowed_domains = ['http://www.itcast.cn/']
    start_urls = ['http://www.itcast.cn/channel/teacher.shtml#ajavaee']

    def parse(self, response):
        teacher_list =response.xpath('//div[@class="li_txt"]')
        Item=DemoItem()
        teacherItem = []
        for each in teacher_list:
            name=each.xpath('./h3/text()').extract()
            title=each.xpath('./h4/text()').extract()
            info=each.xpath('./p/text()').extract()

            Item['name']=name[0]
            Item['title']=title[0]
            Item['info']=info[0]

            teacherItem.append(Item)

        return teacherItem
```

最后再执行命令：

```
$ scrapy crawl itcast -o itcast.csv
```

可以保存为（json，csv，xml）等格式



## 使用管道来存储文件

管道就是在爬取数据之后的处理过程，这里拿个案例来说明

与上面不同的是爬取的结果不是直接return返回，因为return之后程序就结束了，所以使用yield返回

itcast.py文件(你的爬虫文件)

```python
# -*- coding: utf-8 -*-
import scrapy
from demo.items import DemoItem


class ItcastSpider(scrapy.Spider):
    name = 'itcast'
    # allowed_domains = ['http://www.itcast.cn/']
    start_urls = ['http://www.itcast.cn/channel/teacher.shtml#ajavaee']

    def parse(self, response):
        teacher_list =response.xpath('//div[@class="li_txt"]')
        Item=DemoItem()
        # teacherItem = []
        for each in teacher_list:
            name=each.xpath('./h3/text()').extract()
            title=each.xpath('./h4/text()').extract()
            info=each.xpath('./p/text()').extract()

            Item['name']=name[0]
            Item['title']=title[0]
            Item['info']=info[0]

            # teacherItem.append(Item)

            yield Item
        # return teacherItem
```



然后在piplines.py编写存储的方式，这里我用的是存储到json文件

```python
import json

class DemoPipeline(object):
    def __init__(self):
        self.filename=open("teacher.json","wb+")

    def process_item(self, item, spider):
        jsontext=json.dumps(dict(item),ensure_ascii=False)+'\n'
        self.filename.write(jsontext.encode('utf-8'))
        return item

    def close_spider(self,spider):
        self.filename.close()
```



## scrapy shell的使用

scrapy shell可以帮助我们快速查看网页的请求，一般我们在爬取网站的时候，可以先试一下使用scrapy shell启动，因为有些网页是使用ajax或者js来显示的，直接以上面演示的方式请求只能请求到一个空的网页，比如我现在要爬取一个智联招聘的页面：

```python
$ scrapy shell  https://sou.zhaopin.com/?jl=763&kw=python&kt=3&sf=0&st=0
```

 进入shell命令行之后输入命令

```python
>>> view(response)
```

然后就可以通过你的默认浏览器看到请求后的结果，如果爬取后的网页是跟浏览器输入地址访问的一样的话，那么就可以直接像我上面演示的方式请求。如果请求后发现重要数据为空的话，比如我这样：

![scrapy直接爬取智联招聘页面](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/1570623395604.png)

因为智联招聘的页面是通过js去请求的，所以这些数据都没有显示在request请求的页面上，因为这部分的数据都是使用js去加载的，所以只要打开调试网页查看js的请求地址，然后去请求js的请求地址就可以获取这部分的数据了。

更多关于如何提取异步加载的数据可以在《scrapy爬取动态网页》博客可以详解

