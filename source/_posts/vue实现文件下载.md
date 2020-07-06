---
title: vue实现文件下载
date: 2020-06-24 17:32:11
categories:
    - 前端
tags:
    - 日常总结
---

> download.js这个插件会造成只下载文件地址，只能下载图片和视频地址，对于pdf和word之类的内容只有文件的下载地址
>
> 这个是我根据别人的方法封装成一个工具类，在vue中直接引入就可以使用了

在工具类中引入写入下载方法

![工具类截图](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200624173554.png)

```js
import axios from 'axios'

export default function downloadfile (url) {
  let params = {}
  axios.post('http://192.168.1.34:8000/media/' + url, params, { responseType: 'blob' })
    .then(res => {
      let content = res.data
      let blob = new Blob([content])
      let fileName = url
      if ('download' in document.createElement('a')) {
        // 非IE下载
        let elink = document.createElement('a')
        elink.download = fileName
        elink.style.display = 'none'
        elink.href = URL.createObjectURL(blob)
        document.body.appendChild(elink)
        elink.click()
        URL.revokeObjectURL(elink.href) // 释放URL 对象
        document.body.removeChild(elink)
      } else {
        // IE10+下载
        navigator.msSaveBlob(blob, fileName)
      }
    })
    .catch(err => {
      console.log(err)
    })
}

```

然后在vue文件中直接引入即可

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200624173657.png)