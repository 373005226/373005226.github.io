---
title: axios
summary: 讲解axios的各种属性和方法
date: 2020-01-04 22:47:00
categories:
    - 前端
tags:
    - Vue
---

> 以下皆是由https://www.bilibili.com/video/av59594689?from=search&seid=2169751232721048853
>
> 视频中的老师操作代码和讲义总结而成

## axios

axios这个方法十分的简单，都是如如下形式

```javascript
axios({
  url:'',
  method:'get',
  params:{

  }
}).then(res=>{
  console.log(res)
})
```

axios默认是get请求，params是get请求附带的参数，如果是post请求的话参数就放在data里面

## axios.all

这个是如果要两个或者以上的请求都完成后再进行下一步

```js
axios.all([axios({
  url: ''
}),axios({
  url:''
})]).then(res=>{
  console.log(res)
})

```

## axios.spread

spread是对结果的延展

这里写一个案例

```js
axios.all([axios({
  url:'http://101.200.180.194:8000/goods/'
}),axios({
  url:'http://101.200.180.194:8000/category/'
})]).then(res=>{
  console.log(res)
})
```

如果是如上的写法的话，得到的结果是

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104170932.png)

spread的功能就是

```js
axios.all([axios({
  url:'http://101.200.180.194:8000/goods/'
}),axios({
  url:'http://101.200.180.194:8000/category/'
})]).then(axios.spread((res1,res2)=>{
  console.log(res1)
  console.log(res2)
}))
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104171348.png)

## axios全局配置

比如axios在配置的时候有很多是重复的，比如超时时间、请求头、地址的重复字段如http:127.0.0.1等

如果都重复写很臃肿，所以可以用default属性

```js
axios.defaults.baseURL='http://101.200.180.194:8000/'
axios.defaults.timeout=5000
axios.all([axios({
  url:'goods'
}),axios({
  url:'category'
})]).then(axios.spread((res1,res2)=>{
  console.log(res1)
  console.log(res2)
}))
```

这样还是可以请求到结果的

## axios的实例

这个还是上面的那个全局配置问题，如果项目里面有两个或者以上的不同配置，比如请求头的地址不一样，那么如果不用全局配置写法的话，那么就会很多重复，如果用了全局配置的话，那么剩下的不同配置的文件就会报错

```js
const instance1 = axios.create({
  baseURL:'http://101.200.180.194:8000/',
  timeout:5000
})

instance1({
  url:'goods'
}).then(res=>{
  console.log(res)
})

instance1({
  url:'category',
}).then(res=>{
  console.log(res)
})

const instance2 = axios.create({
  baseURL:'http://127.0.0.1/',
  timeout:5000
})

instance2({
  url:'person'
}).then(res=>{
  console.log(res)
})

```

这样就可以有两个不同的配置了

## axios的封装

axios如果是要使用的话要封装在一个包里面，如果使用的是如下的写法的话，那么如果axios不更新了或者是出现了什么大的bug，那么就要一个文件一个文件的修改

### 方法1：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104173614.png" style="zoom:67%;" />

如上的写法是可以，而且很常见，比如在uniapp里面的写法什么的.......



这些请求最好还是封装在一个包里面去调用，如果需要修改了，那么就可以只修改一个地方，就可以修改全局了

通常用的都是如下的写法：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104174417.png)



然后在组件中调用就是

```js
import {request} from "./API"

request({
  url:'goods'
},res=>{
  console.log(res)
},err=>{
  console.log(err)
})

```

### 方法2：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104174828.png" style="zoom:67%;" />

组件内的方法：

```js
import {request} from "./API"

request({
  baseConfig:{

  },
  success(res){

  },
  failure(err){

  }
})
```



### 方法3：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104175237.png" style="zoom:67%;" />

```js
import {request} from "./API"

request({
  url:'goods',
}).then(res=>{
  console.log(res)
}).catch(err=>{
  console.log(err)
})

```

因为axios本身就是由promise构造的

### 最终写法：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104175721.png" style="zoom:67%;" />

```js
import {request} from "./API"

request({
  url:'goods',
}).then(res=>{
  console.log(res)
}).catch(err=>{
  console.log(err)
})

```

因为在axios里面的`.then`、`.catch`都是多余的，instance的返回值本身就是promise

## axios拦截器

拦截器有**在请求之前进行拦截、请求之后进行拦截、响应成功之前拦截、响应之后拦截**这四种方法

请求前拦截和请求后拦截：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104181544.png" style="zoom:67%;" />

响应前拦截和响应后拦截：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104181630.png" style="zoom:67%;" />

这个用于比如只需要返回res.data的值，就可以在响应前拦截中返回需要的值即可

