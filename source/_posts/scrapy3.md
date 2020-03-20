---
title: 死磕爬虫之scrapy模拟登陆的方法
author: LY
date: 2019-10-10
summary: 介绍了scrapy的模拟登陆的三种方法
categories:
    - 爬虫
tags:
    - scrapy
---

>  以下三个策略以人人网作为案例来描述

## 策略一：使用保存的cookie登陆（100%成功）

### 登陆后查看自己的cookie信息

![cookie信息](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/1570770626349.png)



### 重写Spider类的start_requests方法

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;因为所有的爬虫类都是继承于Spider的，所以重写start_requests方法可以适用于所有的爬虫类

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;最重要记得要把setting.py的COOKIES_ENABLED开启起来

&emsp;

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;我的setting.py文件关键配置如下：

```python
COOKIES_ENABLED = True

DEFAULT_REQUEST_HEADERS = {
  'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8',
  'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/74.0.3729.169 Safari/537.36',
}

ROBOTSTXT_OBEY = False
```

然后使用命令创建一个新的爬虫文件(renren.py)



### 编写爬虫字段

renren.py

```python
# -*- coding: utf-8 -*-
import scrapy


class RenrenSpider(scrapy.Spider):
    name = 'renren'
    allowed_domains = ['http://www.renren.com/972451360/newsfeed/photo']
    start_urls = ['http://www.renren.com/972451360/newsfeed/photo/']

    cookies ={
    "JSESSIONID": "abc6L9jo347Dw9fWUf52w",
    "anonymid" : "k1lmx3wm-pdg03r",
    "_r01_" : "1",
    "depovince" : "GW",
    "springskin" : "set",
    "jebe_key" : "09e48352-6a6a-490c-a137-d78928ffe419%7C18edbeb77a97f6145ca4db5e06940ded%7C1570768484510%7C1%7C1570768487369",
    "t" : "7e803da6e1d3c6ce4421d9adc13a20390",
    "societyguester" : "7e803da6e1d3c6ce4421d9adc13a20390",
    "id" : "972451360",
    "xnsid" : "46d5aac1cccccccc",
    "loginfrom" : "null"
    }

    def start_requests(self):
        for url in self.start_urls:
            yield scrapy.FormRequest(url, cookies=self.cookies, callback=self.parse_page)

    def parse_page(self, response):
        with open("deng.html", "wb+") as filename:
            filename.write(response.body)
```



爬取得到的结果如下：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/1570776253453.png)



这个方法虽然麻烦，但是肯定是可以成功的



## 策略二：使用post请求来模拟登陆

### 找到表单登陆请求地址

如果使用post请求的话就一定要找到人人网登陆表单的请求地址，人人网的请求地址如图所示：

![人人网表单](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/1570769345282.png)

得到的地址就为'http://www.renren.com/PLogin.do'



### 编写爬虫字段

```python
import scrapy


class RenrenSpider(scrapy.Spider):
    name = 'renren'
    # allowed_domains = ['http://www.renren.com/972451360/newsfeed/photo']
    start_urls = ['http://www.renren.com/972451360/newsfeed/photo/']

    def start_requests(self):
        url = 'http://www.renren.com/PLogin.do'
        data = {
            "email": "",
            "password": ""
        }
        request = scrapy.FormRequest(url, formdata=data, callback=self.parse_page)
        yield request

    def parse_page(self, response):
        with open("renren.html", "wb+") as filename:
            filename.write(response.body)
```

这个方法其实就先访问登陆页面的表单，然后登陆成功之后再去爬取想要的页面

## 策略三：模拟正规的登陆流程来模拟登陆

### 编写爬虫字段

这个方法不同于前两种，前两种是重写start_requests方法，这个方法是模拟用户的登陆流程：先访问登陆页面，输入账号密码之后再访问详情页面

```python
import scrapy


class RenrenSpider(scrapy.Spider):
    name = 'renren'
    allowed_domains = ['renren.com']
    start_urls = ['http://renren.com/']

    # 处理start_urls里的登录url的响应内容，提取登陆需要的参数（如果需要的话)
    def parse(self, response):
        # 提取登陆需要的参数
        # 发送请求参数，并调用指定回调函数处理
        yield scrapy.FormRequest.from_response(
            response,
            formdata={"email": "你的邮箱", "password": "你的密码" },
        	callback = self.parse_page
        	)  # 获取登录成功状态，访问需要登录后才能访问的页面
	def parse_page(self, response):
		url = "http://www.renren.com/422167102/profile"
		yield scrapy.Request(url, callback=self.parse_newpage)

            # 处理响应内容
	def parse_newpage(self, response):
		with open("renren.html", "wb+") as filename:
			filename.write(response.body)
```

## 补充点

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;单单爬取人人网这类的简单网站用以上三个方法是没问题的，但是类似于知乎这种使用`Django`作为后台的网站，登陆注册都有个`_xsrf`字段放在form表单的末尾，这个字段是Django为了防止 跨站域请求伪造攻击而添加的。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;所以在使用模拟登陆前一定要查看表单有没有这个字段，如果有就用xpath或者正则表达式提取出来，然后一起提交。比如我这里拿最后一个方法来说明：

```python
import scrapy


class RenrenSpider(scrapy.Spider):
    name = 'renren'
    allowed_domains = ['renren.com']
    start_urls = ['http://renren.com/']

    # 处理start_urls里的登录url的响应内容，提取登陆需要的参数（如果需要的话)
    def parse(self, response):
        # 提取登陆需要的参数
        _xsrf = response.xpath("//_xsrf").extract()[0]
        # 发送请求参数，并调用指定回调函数处理
        yield scrapy.FormRequest.from_response(
            response,
            formdata={"email": "xxx@163.com", "password": "xxx", "_xsrf" : _xsrf },
        callback = self.parse_page
        )  # 获取登录成功状态，访问需要登录后才能访问的页面
        def parse_page(self, response):
            url = "http://www.renren.com/422167102/profile"
            yield scrapy.Request(url, callback=self.parse_newpage)

        # 处理响应内容
        def parse_newpage(self, response):
            with open("renren.html", "wb+") as filename:
                filename.write(response.body)
```

