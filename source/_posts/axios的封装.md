---
title: axios的封装
summary: 根据看开源项目的代码来把axios封装成方法，可以大幅度的减少代码的复用
date: 2020-03-21 15:06:00
author: LY1
categories:
    - 前端
tags:
    - Vue
reprintPolicy: 
---

## axios的封装

```js
import axios from 'axios'

axios.defaults.timeout = 5000
axios.defaults.baseURL = 'http://127.0.0.1:8000/'
// axios.defaults.headers['Content-Type'] = 'application/json'
// axios.defaults.headers['Authorization'] = 'JWT '+localStorage.getItem('token')


export default {
  fetchGet(url, params = {}, config = {}) {
    return new Promise((resolve, reject) => {
      axios.get(url, params, config).then(res => {
        resolve(res.data)
      }).catch(error => {
        reject(error)
      })
    })
  },
  fetchPost(url, data = {}, config = {}) {
    return new Promise((resolve, reject) => {
      axios.post(url, data, config).then(res => {
        resolve(res.data)
      }).catch(error => {
        reject(error)
      })
    })
  },
  fetchDelete(url, data = {}, config = {}) {
    return new Promise((resolve, reject) => {
      axios.delete(url, data, config).then(res => {
        resolve(res.data)
      }).catch(error => {
        reject(error)
      })
    })
  },
  fetchPut(url ,params, data = {}, config = {}) {
    return new Promise((resolve, reject) => {
      axios.put(url , params , data, config).then(res => {
        resolve(res.data)
      }).catch(error => {
        reject(error)
      })
    })
  }
}

```

以上代码是axios的增删改查，已经封装好了可以直接调用

## 调用示例

index.js是我调用方法的文件

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200321154729.png)

### get方法：

#### 调用方法

```js
export const banner =(params)=>{
  return http.fetchGet('banner/',params)
}
```

#### 不带请求头的使用方法:

```js
    banner().then(res=>{
      // console.log(res)
      this.banner_list = res
    })
```

#### 带请求头的get方法

```js
userInfo({
    headers: {
        Authorization: 'JWT ' + localStorage.getItem('token')
    }
}).then(res => {
    console.log(res)
})
```

### Post方法：

#### 调用方法

```js
export const useraddress = (params,config)=>{
  return http.fetchPost('address/',params,config)
}
```

#### 不带请求头的写法：

```js
useraddress().then(res=>{
	
})
```



#### 带请求头的使用方法:

```js
useraddress({你的data数据}, {headers: {Authorization: 'JWT ' + localStorage.getItem('token')}}).then(res=>{
console.log(res)
})
```

其余的PUT和Delete方法都是一样的