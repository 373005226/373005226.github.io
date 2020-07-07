---
title: file转换为blob
date: 2020-06-09 19:55:33
categories:
    - 前端
tags:
    - 日常总结
    - 转载
---

> 本文转载于https://segmentfault.com/a/1190000020153597

## javascript实现上传文件流file转base64，base64转blob，blob转url访问地址

这file转换为blob的话，在uniapp是有一个API可以直接调用`uni.chooseImage`

而在H5的话需要自己写方法去转换

具体方法如下：

```javascript
	/**
	* 上传附件转base64
	*/
	fileByBase64(file, callback) {
		var reader = new FileReader()
		// 传入一个参数对象即可得到基于该参数对象的文本内容
		reader.readAsDataURL(file)
		reader.onload = function(e) {
			// target.result 该属性表示目标对象的DataURL
			// console.log(e.target.result)
			callback(e.target.result)
		}
	},
     /**
     * base64转Blob
     */
	base64ByBlob(base64, callback) {
		// eslint-disable-next-line one-var
		var arr = base64.split(','),
			mime = arr[0].match(/:(.*?);/)[1],
			bstr = atob(arr[1]),
			n = bstr.length,
			u8arr = new Uint8Array(n)
		while (n--) {
			u8arr[n] = bstr.charCodeAt(n)
		}
		// console.log(new Blob([u8arr], { type: mime }))
		callback(new Blob([u8arr], {
			type: mime
		}))
	},
     /**
     * 使用
     */
	selectedFile(event) {
		let file = event.target.files[0]

		this.fileByBase64(file, (base64) => {
			this.base64ByBlob(base64, blob => {
				// console.log(blob, 'blob')
				var url = window.URL.createObjectURL(blob)
				this.fileList = url
				console.log(this.fileList)
			})
		})
	}

```

然后只需要在input里面绑定这个事件即可

```html
<input type="file" @change="selectedFile($event)" class="upload">
```

