---
title: 死磕爬虫之scrapy下载中间件使用和配置
author: LY
date: 2019-10-11
summary: 介绍了scrapy的中间件，及中间件如何配置使用代理和请求头
categories:
    - 爬虫
tags:
    - scrapy
---



>  scrapy在请求网站的时候必须经过这个下载中间件
>
> 一些网站反爬虫措施做的比较严谨的时候，如果你并发过高就会封你ip等反爬虫操作，这个时候就可以使用一些代理、随机请求头来躲避这些反爬虫措施

## 常见的反爬虫策略

- 动态设置User-Agent（随机切换User-Agent，模拟不同用户的浏览器信息）

- 禁用Cookies（也就是不启用cookies middleware，不向Server发送cookies，有些网站通过cookie的使用发现爬虫行为）

  - 可以通过`COOKIES_ENABLED` 控制 CookiesMiddleware 开启或关闭

- 设置延迟下载（防止访问过于频繁，设置为 2秒 或更高）

- Google Cache 和 Baidu Cache：如果可能的话，使用谷歌/百度等搜索引擎服务器页面缓存获取页面数据。

- 使用IP地址池：VPN和代理IP，现在大部分网站都是根据IP来ban的。

- 使用 [Crawlera](https://scrapinghub.com/crawlera)（专用于爬虫的代理组件），正确配置和设置下载中间件后，项目所有的request都是通过crawlera发出（也就是爬虫代理商，直接使用他们给你的账号密码就可以不停地切换IP来反爬虫）

  ```python
    DOWNLOADER_MIDDLEWARES = {
        'scrapy_crawlera.CrawleraMiddleware': 600
    }
  
    CRAWLERA_ENABLED = True
    CRAWLERA_USER = '注册/购买的UserKey'
    CRAWLERA_PASS = '注册/购买的Password'
  ```

## 设置下载中间件（Downloader Middlewares）

下载中间件是处于引擎(crawler.engine)和下载器(crawler.engine.download())之间的一层组件，可以有多个下载中间件被加载运行。

1. 当引擎传递请求给下载器的过程中，下载中间件可以对请求进行处理 （例如增加http header信息，增加proxy信息等）；
2. 在下载器完成http请求，传递响应给引擎的过程中， 下载中间件可以对响应进行处理（例如进行gzip的解压等）

要激活下载器中间件组件，将其加入到 DOWNLOADER_MIDDLEWARES 设置中。 该设置是一个字典(dict)，键为中间件类的路径，值为其中间件的顺序(order)。

## 下载中间件字段

### process_request(self, request, spider)

- 当每个request通过下载中间件时，该方法被调用。

- process_request() 必须返回以下其中之一：一个 None 、一个 Response 对象、一个 Request 对象或 raise IgnoreRequest:

  - 如果其返回 None ，Scrapy将继续处理该request，执行其他的中间件的相应方法，直到合适的下载器处理函数(download handler)被调用， 该request被执行(其response被下载)。
  - 如果其返回 Response 对象，Scrapy将不会调用 任何 其他的 process_request() 或 process_exception() 方法，或相应地下载函数； 其将返回该response。 已安装的中间件的 process_response() 方法则会在每个response返回时被调用。
  - 如果其返回 Request 对象，Scrapy则停止调用 process_request方法并重新调度返回的request。当新返回的request被执行后， 相应地中间件链将会根据下载的response被调用。
  - 如果其raise一个 IgnoreRequest 异常，则安装的下载中间件的 process_exception() 方法会被调用。如果没有任何一个方法处理该异常， 则request的errback(Request.errback)方法会被调用。如果没有代码处理抛出的异常， 则该异常被忽略且不记录(不同于其他异常那样)。

- 参数:

  - `request (Request 对象)` – 处理的request

  - `spider (Spider 对象)` – 该request对应的spider

    

### process_response(self, request, response, spider)

当下载器完成http请求，传递响应给引擎的时候调用

- process_request() 必须返回以下其中之一: 返回一个 Response 对象、 返回一个 Request 对象或raise一个 IgnoreRequest 异常。
  - 如果其返回一个 Response (可以与传入的response相同，也可以是全新的对象)， 该response会被在链中的其他中间件的 process_response() 方法处理。
  - 如果其返回一个 Request 对象，则中间件链停止， 返回的request会被重新调度下载。处理类似于 process_request() 返回request所做的那样。
  - 如果其抛出一个 IgnoreRequest 异常，则调用request的errback(Request.errback)。 如果没有代码处理抛出的异常，则该异常被忽略且不记录(不同于其他异常那样)。
- 参数:
  - `request (Request 对象)` – response所对应的request
  - `response (Response 对象)` – 被处理的response
  - `spider (Spider 对象)` – response所对应的spider

## 编写中间件字段

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;首先先设置你的UserAgent和PROXIES的数据，可以在setting里面设置，也可以在中间件里面设置，但是setting直接导入就好了更方便，在类里面设置还得用个self。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;比如我的setting.py文件

```python
USER_AGENTS = [
    'Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 6.0)',
    'Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.2)',
    'Opera/9.27 (Windows NT 5.2; U; zh-cn)',
    'Opera/8.0 (Macintosh; PPC Mac OS X; U; en)',
    'Mozilla/5.0 (Macintosh; PPC Mac OS X; U; en) Opera 8.0',
    'Mozilla/5.0 (Linux; U; Android 4.0.3; zh-cn; M032 Build/IML74K) AppleWebKit/534.30 (KHTML, like Gecko) Version/4.0 Mobile Safari/534.30',
    'Mozilla/5.0 (Windows; U; Windows NT 5.2) AppleWebKit/525.13 (KHTML, like Gecko) Chrome/0.2.149.27 Safari/525.13'
]
PROXIES = [
    {"ip_port": "IP地址:端口号", "user_passwd": "账号:密码"}
           # {"ip_prot" :"121.42.140.113:16816", "user_passwd" : ""}
           # {"ip_prot" :"121.42.140.113:16816", "user_passwd" : ""}
           # {"ip_prot" :"121.42.140.113:16816", "user_passwd" : ""}
           ]
```



然后编写中间件字段：

```python
import random
import base64

from ZLzhaoping.settings import USER_AGENTS
from ZLzhaoping.settings import PROXIES

# 随机的User-Agent
class RandomUserAgent(object):
    def process_request(self, request, spider):
        useragent = random.choice(USER_AGENTS)
        #print useragent
        request.headers.setdefault("User-Agent", useragent)

class RandomProxy(object):
    def process_request(self, request, spider):
        proxy = random.choice(PROXIES)

        if proxy['user_passwd'] is None:
            # 没有代理账户验证的代理使用方式
            request.meta['proxy'] = "http://" + proxy['ip_port']

        else:
            # 对账户密码进行base64编码转换
            base64_userpasswd = base64.b64encode(proxy['user_passwd'])
            # 对应到代理服务器的信令格式里
            request.headers['Proxy-Authorization'] = 'Basic ' + base64_userpasswd

            request.meta['proxy'] = "http://" + proxy['ip_port']
```



HTTP代理登陆的时候是要使用登陆验证的，原因如下：

> 为什么HTTP代理要使用base64编码：
>
> HTTP代理的原理很简单，就是通过HTTP协议与代理服务器建立连接，协议信令中包含要连接到的远程主机的IP和端口号，如果有需要身份验证的话还需要加上授权信息，服务器收到信令后首先进行身份验证，通过后便与远程主机建立连接，连接成功之后会返回给客户端200，表示验证通过，就这么简单，下面是具体的信令格式：

```python
CONNECT 59.64.128.198:21 HTTP/1.1
Host: 59.64.128.198:21
Proxy-Authorization: Basic bGV2I1TU5OTIz
User-Agent: OpenFetion
```

> 其中`Proxy-Authorization`是身份验证信息，Basic后面的字符串是用户名和密码组合后进行base64编码的结果，也就是对username:password进行base64编码。
