---
title: 死磕爬虫之CrawlSpider类
author: LY
summary: 通过案例来介绍CrawlSpider相对于Spider的便捷性
date: 2019-10-09
categories:
    - 爬虫
tags:
    - scrapy
---



> 爬虫文件继承的类不仅只有scrapy.Spider类，还有很多类，但是Spider是所有爬虫类的基类。这里我介绍一下CrawlSpider类的使用、说明和spider类和CrawlSpider类的对比

## Spider与CrawlSpider案例对比

### Spider爬取有页码的数据

Spider在爬取有页码的数据的时候要使用回调函数，在请求完一页的数据之后使用回调函数继续执行下一个页面

比如我这要爬取的是csdn的论坛网站

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/1570709064975.png)

下面有五页数据，从第二页开始url地址就会增加个pages，所以在每次请求完页面的时候都给pages+1去重新爬取新的页面。

部分源码如下：

scdemo.py（爬虫文件）

```python
# -*- coding: utf-8 -*-
import scrapy
from ZLzhaoping.items import ZlzhaopingItem


class ScdemoSpider(scrapy.Spider):
    name = 'scdemo'
    # allowed_domains = ['https://sou.zhaopin.com/?jl=763']
    pages=1
    url='https://bbs.csdn.net/forums/OL_Script?page='
    start_urls =[url+str(pages)]

    def parse(self, response):
        zhilian = ZlzhaopingItem()
        list=response.xpath("//td[@class='forums_topic']")

        for each in list:
            title=each.xpath("./a[contains(@class,'forums_title ')]/text()").extract()

            zhilian['title']=title[0]

            yield zhilian

        if self.pages<3:
            self.pages+=1

        yield scrapy.Request(self.url + str(self.pages), callback=self.parse)
        # with open("bbs.html", "wb+") as f:
        #     f.write(response.body)
```



pipelines.py（管道文件）

```python
import json


class ZlzhaopingPipeline(object):
    def __init__(self):
        self.filename = open("zp.json", "wb+")

    def process_item(self, item, spider):
        jsontext = json.dumps(dict(item), ensure_ascii=False) + '\n'
        self.filename.write(jsontext.encode('utf-8'))
        return item

    def close_spider(self, spider):
        self.filename.close()
```



> 里面代码关键的就是确定爬取的页码是多少，然后每一次请求玩一页的数据都让pages+1，这样就可以循环出所有的页码的数据了。不过使用CrawlSpider会更简单，CrawlSpider可以创建自己的规则,比如只要存在还有跳转到下一页页码的url字段，就反复请求。
>

### CrawlSpider爬取有页码的数据

crawlSpider我先演示一下同样是爬取同一个网页

使用如下命令快捷创建CrawlSpider

```python
$ scrapy genspider -t crawl clidemo https://bbs.csdn.net/forums/OL_Script?page=0
```

clidemo.py:

```python
import scrapy
from scrapy.linkextractors import LinkExtractor
from scrapy.spiders import CrawlSpider, Rule
from ZLzhaoping.items import ZlzhaopingItem


class ClidemoSpider(CrawlSpider):
    name = 'clidemo'
    # allowed_domains = ['https://bbs.csdn.net/forums/OL_Script?page=2']
    start_urls = ['https://bbs.csdn.net/forums/OL_Script?page=0']

    rules = (
        Rule(LinkExtractor(allow=r'page=\d+'), callback='parse_item', follow=True),
    )

    def parse_item(self, response):
        zhilian = ZlzhaopingItem()
        list=response.xpath("//td[@class='forums_topic']")

        for each in list:
            title=each.xpath("./a[contains(@class,'forums_title ')]/text()").extract()

            zhilian['title']=title[0]

            yield zhilian
```

> 运行之后就可以直接把所有的页面全部爬完了,方法比Spider来写方便很多。
>
> 唯一不足的地方就是CrawlSpider一次性把全部的页码请求完，而Spider可以设置一个停止条件，不必全部爬完。
>

## CrawlSpider的字段详解

CrawlSpider源码没啥好解释的，写的挺清楚的，这里解释一下CrawlSpider的匹配规则和提取规则

**LinkExtractor：**

```python
def __init__(self, allow=(), deny=(), allow_domains=(), deny_domains=(), restrict_xpaths=(),
                 tags=('a', 'area'), attrs=('href',), canonicalize=False,
                 unique=True, process_value=None, deny_extensions=None, restrict_css=(),
                 strip=True):
```

其中主要的参数如下：

- `allow`：满足括号中“正则表达式”的值会被提取，如果为空，则全部匹配。
- `deny`：与这个正则表达式(或正则表达式列表)不匹配的URL一定不提取。
- `allow_domains`：会被提取的链接的domains。
- `deny_domains`：一定不会被提取链接的domains。
- `restrict_xpaths`：使用xpath表达式，和allow共同作用过滤链接。



**Rule：**

```python
    def __init__(self, link_extractor, callback=None, cb_kwargs=None, follow=None, process_links=None, process_request=identity):
        self.link_extractor = link_extractor
        self.callback = callback
        self.cb_kwargs = cb_kwargs or {}
        self.process_links = process_links
        self.process_request = process_request
        if follow is None:
            self.follow = False if callback else True
        else:
            self.follow = follow
```

- `link_extractor`：是一个Link Extractor对象，用于定义需要提取的链接。

- `callback`： 从link_extractor中每获取到链接时，参数所指定的值作为回调函数，该回调函数接受一个response作为其第一个参数。

  > 注意：当编写爬虫规则时，避免使用parse作为回调函数。由于CrawlSpider使用parse方法来实现其逻辑，如果覆盖了 parse方法，crawl spider将会运行失败。

- `follow`：是一个布尔(boolean)值，指定了根据该规则从response提取的链接是否需要跟进(就是是否根据爬取到的页面继续点进去爬取)。 如果callback为None，follow 默认就为True。如果写了callback，follow默认就为False

- `process_links`：指定该spider中哪个的函数将会被调用，从link_extractor中获取到链接列表时将会调用该函数。该方法主要用来过滤。（比如一些网站，在你快速爬取的时候回给你一些错误的链接让你中断爬取，通常会修改url地址的某些参数，比如说&改成？，但是使用CrawlSpider的话，只要写一个函数来修改就可以了，方法如 process_links="deal_links"）

- `process_request`：指定该spider中哪个的函数将会被调用， 该规则提取到每个request时都会调用该函数。 (用来过滤request)