---
title: Vue跨域
date: 2020-05-27 16:58:49
summary: 讲解Vue跨域的问题
categories:
    - 转载
tags:
    - Vue
---

> 本文转载于https://www.cnblogs.com/wasbg/p/10973880.html



## 什么是跨域

  跨域指浏览器不允许当前页面的所在的源去请求另一个源的数据。源指协议，端口，域名。只要这个3个中有一个不同就是跨域。 这里列举一个经典的列子：

```javascript
#协议跨域
http://a.baidu.com访问https://a.baidu.com；
#端口跨域
http://a.baidu.com:8080访问http://a.baidu.com:80；
#域名跨域
http://a.baidu.com访问http://b.baidu.com；
```

  现在很多公司都是采用前后分离的方式开发。那么出现经常和会跨域打交道。我这里整理日常开发中解决跨域的几种方案。我们前端使用的Vue，后端使用的NodeJs。

## 解决方案

### proxyTable

  在vue脚手架的2.*版本中，这里vue脚手架生成的标准项目为准。一般在项目config目录下面有个index文件。里面格式如下：

```javascript
'use strict'
const path = require('path')
module.exports = {
    dev: {
        // Paths
        assetsSubDirectory: 'static',
        assetsPublicPath: '/',
        proxyTable: {
            '/api': {
                target: 'http://localhost:7001',//后端接口地址
                changeOrigin: true,//是否允许跨越
                pathRewrite: {
                    '^/api': '/api',//重写,
                }
            }
        },
        host: '192.168.0.104',
        port: 8081,
        autoOpenBrowser: false,
        errorOverlay: true,
        notifyOnErrors: true,
        poll: false,
        useEslint: true,
        showEslintErrorsInOverlay: false,
        devtool: 'eval-source-map',
        cacheBusting: true,
        cssSourceMap: false,
    },

}
```

  上面配置中，我们根据实际情况只需要修改proxyTable对于配置即可。假设我后端请求地址是http://localhost:7001 ，所有api的接口url都以/api开头。所以首先需要匹配所有以/api开头的.然后修改target的地址为http://localhost:7001 。最后修改pathRewrite地址。将前缀 '^api' 转为 '/api'。如果本身的接口地址就有 '/api' 这种通用前缀，就可以把 pathRewrite 删掉。注意这个方式只能在开发环境中使用。



### CORS

  CORS即跨源资源共享，它定义了一种浏览器和服务器交互的方式来确定是否允许跨域请求。它是一个妥协，有更大的灵活性，但比起简单地允许所有这些的要求来说更加安全。但是CORS也具有一定的风险性，比如请求中只能说明来自于一个特定的域但不能验证是否可信，而且也容易被第三方入侵。 这里一般需要后端配合,开启CORS。一般各种语言都有类似的包。比如Node.JS的[koa2-cors](https://www.npmjs.com/package/koa2-cors)，Django的[CORSheaders](https://github.com/adamchainz/django-cors-headers)

```javascript
var koa = require('koa');
//npm install --save koa2-cors
var cors = require('koa2-cors');
var app = koa();
//开启
app.use(cors());
```

这个方式解决的跨越问题支持开发和生产环境。但是有一定的安全性问题。

### Nginx

  当我们明白跨越的含义之后。只要解决了'源'的问题。那么跨越也就不存在了。这里我们便会想到proxy，同时也会想到Nginx。
![enter image description here](http://img.shepherd618.cn/vue_po.png)
我们只需要在部署静态资源配置下面加上红框里面的配置就可以了。同时这个方法支持开发环境和生产环境。

### 后端程序代理

  当然上面2个方法都需要后端的配合和需要修改服务器配置。所有还有一种方法不需要他们配合 ，我们自己就可以做到。就是我们自己启一个后端程序做代理。然后把所有的请求转发到服务器。这里要用到node的一个包[http-proxy-middleware](https://www.npmjs.com/package/http-proxy-middleware)。关键代码(express)如下
![enter image description here](http://img.shepherd618.cn/nodo_py.png)

  

用Vue-cli脚手架搭建了个demo,前后分离就有跨域问题的出现。

vue-clie搭建demo步骤（传送门）：https://www.cnblogs.com/wangenbo/p/8487764.html

我自己在网上找了2个接口做测试：

CSDN：https://www.csdn.net/api/articles?type=more&category=home&shown_offset=1524276761019196&first_view=false

掘金：https://suggest-follow-api-ms.juejin.im/v1/getByTag?tag=%E5%89%8D%E7%AB%AF&src=web&t=1

安装模块axios进行ajax请求，直接请求，肯定显示跨域，无法请求。

 ===============  以下为npm run dev 跨域的解决方法  ===============

![img](https://images2018.cnblogs.com/blog/1180395/201804/1180395-20180421172816214-612268121.png)

脚手架Vue-cli已经帮我们留好了接口

参考这篇文章才知道：https://blog.csdn.net/qq_31201781/article/details/78778193

在config目录下的index.js文件，有个参数：

proxyTable:{}

所以，我把参数添加成：

![img](https://images2018.cnblogs.com/blog/1180395/201804/1180395-20180421173308108-1728593951.png)

原来接口：

CSDN：https://www.csdn.net/api/articles?type=more&category=home&shown_offset=1524276761019196&first_view=false

掘金：https://suggest-follow-api-ms.juejin.im/v1/getByTag?tag=%E5%89%8D%E7%AB%AF&src=web&t=1

请求链接就变成这样了：

![img](https://images2018.cnblogs.com/blog/1180395/201804/1180395-20180421173405626-1245942528.png)

![img](https://images2018.cnblogs.com/blog/1180395/201804/1180395-20180421173447763-1398900761.png)

最后，把数据打印出来，就可以了。。

![img](https://images2018.cnblogs.com/blog/1180395/201804/1180395-20180421173606705-1558230752.png)

===============  以上为npm run dev 跨域的解决方法  ===============

 

 

===============  以下为npm run build 跨域的解决方法  ===============

我们要打包上线：就用npm run build会打包成dist文件

但发到线上，就得用nginx解决跨域。

首先，下载nginx

https://blog.csdn.net/biaoge0310/article/details/78900538

入门命令：

　 在ngix文件目录下执行以下命令

　　start nginx　　开启ngix

　　nginx -s quit　 退出　

　　nginx -s reload 重启

​    nginx -t 检查配置文件是否成功 


然后：进入nginx/conf目录下的，找到nginx.conf文件

添加如下配置：

![img](https://images2018.cnblogs.com/blog/1180395/201804/1180395-20180421175000045-2066705480.png)

启动ngix服务器： start nginx

输入网址：http://localhost:8099

完美！！！！打包后也解决了。。

===============  以上为npm run build 跨域的解决方法  ===============