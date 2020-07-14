---
title: proxyTable
date: 2020-05-28 14:25:10
summary: 由浅入深讲解proxyTable跨域的作用和特点
updated: 2020-5-19 17:00
author: LY
categories:
    - 前端
tags:
    - webpack
    - Vue
    - 转载
    - 前端面试题
---

> 本文转载于https://www.jianshu.com/p/a2e3147c2af2

我们在使用 `vue-cli` 工具生成 `vue` 项目时



```kotlin
vue init webpack my-project-vue
```

在生成的项目结构里,会有一个 `index.js` 文件.

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200528142725.png)



在这个 `index.js` 文件里,会有一个 `proxyTable` 的空节点.

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200528142736)



理解这个节点的作用需要有两个前置知识

- 前端跨域
- 利用代理服务器进行跨域.

关于这个问题,我在上一篇[[CORS & JSONP 学习笔记一](https://www.jianshu.com/p/3079a07dd37b)]文章中已经有了大致的说明.

如果忘记了,可以回头去看看复习一下.

------

### proxyTable

`proxyTable` 是 `vue-cli` 脚手架在开发模式下,为我们提供的一个跨域的代理中转服务器服务.基于 (`http-proxy-middleware`插件).

为什么要有 `proxyTable`?

有时候在开发期间,我们请求的后台`接口(a.b.c/data.json)`不和`vue(localhost:8080)`在同一个域名,后台也并未提供`cors`跨域服务.

![img](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200528142751)



正常情况下,我们无法通过`ajax`向后台请求到数据.因为跨域了.浏览器的同源策略,截获了这次数据的返回,没有给vue.

解决办法有两个:

- 服务器开启 `cors` (最简单,也是最方便的)
- 用代理服务器中转一下(vue请求的还是自己的后台,让后台去请求真是数据的后台,然后再将数据返回给vue)

------

### 访问一个不支持跨域的后台服务数据接口

这里,用node.js写一个非常简单http服务..端口号是54321.

这个服务就是返回一个`json`数据.

**但并没有开启跨域功能.**



```tsx
const http = require('http')
const fs = require('fs')

http.createServer((request, response) => { 
  console.log(request.url) 
  // 对于所有请求,都返回这个json数据.
  fs.readFile('./json.data.json', 'utf-8', (err, data) => { 
    response.end(data)
  })
}).listen(54321, () => { 
  console.log(`server is running at 54321`)
})
```

测试一下:

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200528142801)



然后使用 `vue-cli` 创建一个 `vue` 项目

并在 `App.vue` 里键入以下代码



```jsx
created() {
      this.axios.get('http://localhost:54321/json.data.json')
          .then(response => {
            this.users = response.data.todoList
            console.log(this.users.todoList)
          })
          .catch(error => {
            console.log(error)
          })
    },  
```

不出意外,肯定会提示跨域了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200528183538)



假设现在这个后台是别人写的,或者是一个压根你就不认识的人或公司写的.你无法让他们去后台追加 `cors` 来进行跨域支持.

那就只剩下另外一个办法了.

**配置 `proxyTable`**

### 配置 proxyTable 来进行跨域

`proxyTable` 跨域的基本原理是:

> 在开发模式下,`webpack` 会为我们提供一个`http`代理服务器.
>  我们请求接口的时候,实际上是请求的`webpack`提供的这个`http`代理服务器.
>  在由这个代理服务器请求真是的数据服务器.
>  最后数据经由`webpack`代理服务器,最后转交给我们的`vue`程序.

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200528142811)

为什么加个代理服务器就可以了?

**因为代理服务器不是浏览器,它没有同源策略的限制.**

在了解到了基本原理之后,就可以看看 `proxyTable` 这个节点的一些配置属性了.



```csharp
proxyTable: {
      // 这里配置 '/api' 就等价于 target , 你在链接里访问 /api === http://localhost:54321
      '/api': {
        target: 'http://localhost:54321/', // 真是服务器的接口地址 // http://localhost:54321/json.data.json,
        secure: true, // 如果是 https ,需要开启这个选项
        changeOrigin: true, // 是否是跨域请求?肯定是啊,不跨域就没有必要配置这个proxyTable了.
        pathRewirte: {
          // 这里是追加链接,比如真是接口里包含了 /api,就需要这样配置.

          '/^api': 'api/', 
          // 等价于 
          // step 1  /api = http://localhost:54321/
          // step 2 /^api = /api + api == http://localhost:54321/api
        }
      }
     },
```

关于 `pathRewrite` 节点说明:

> 首先，在ProxyTable模块中设置了‘/api’，target中设置服务器地址，也就是接口的开头那段地址，例如`http://localhost:54321/`，然后我们在调用接口的时候，就可以全局使用`/api`，这时候`/api`的作用就相当于`http://localhost:54321/`，比如接口的地址是`http://localhost:54321/api/json.data`，我们就可以使用`/api/json.data`

> 那`pathRewrite`是用来干嘛的呢，这里的作用，相当于是替代`/api`，如果接口中是没有`api`的，那就直接置空，如果接口中有`api`，那就得写成{`^/api`:`/api`}，可以理解为一个重定向或者重新赋值的功能。

总结:

- `proxyTable` 就是`webpack`在开发环境给我们提供的一个代理服务器,(使用的是 http-proxy-middleware)
- 目的是为了在服务器不方便开启跨域功能的时候,我们也能方便的在开发阶段发送ajax跨域请求.
- 当了真实发布环境,这个玩意就不起作用了.除非自己配置一个代理服务器,或者让后台开启 `cors`

[[码云code](https://links.jianshu.com/go?to=https%3A%2F%2Fgitee.com%2FcodeResp%2Fvue-proxyTable.git)]

> 作者：人话博客
> 链接：https://www.jianshu.com/p/a2e3147c2af2
> 来源：简书
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。