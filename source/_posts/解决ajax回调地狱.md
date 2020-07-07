---
title: 解决ajax回调地狱
date: 2020-07-07 17:30:54
summary: 介绍如何解决ajax回调地狱的问题
categories:
    - 前端
tags:
    - JavaScript
    - 转载
---

> 本文转载于：https://blog.csdn.net/weixin_44701229/article/details/105015212

## 什么是ajax回调地狱

### 回调函数是什么

回调函数是由于JavaScript是单线程运行所以需要按流程执行，便有了回调函数一般添加callback。

例如：

```js
var ignition = (a,callback)=>{ 
    callback(a)
}
var drive = (b)=>{
    console.log("let's go" + b)
}
ignitio(1, drive)
```

上述为点火之后才能开车。 调用点火函数传入第二参数为函数开车，当点火执行完了才能开车。

### 回调地狱是什么

ajax回调地狱意如其名多层嵌套导致回调变得繁琐

例如：

```js
	ajax1(url, () => {
   	 ajax2(url, () => {
        ajax3(url, () => {
        	doSomething()
       		})
    	})
    })
```

## 解决办法

### promise

promise可以理解为一个自动机器人，它有三种状态：等待中（pending） 完成了 （resolved） 拒绝了（rejected），当他觉得你的要求可以通过便会返回给你resolved，若是你的要求达不到它便会返回给你rejected拒绝你。当然先展示一下具体的用法

```js
 let parkplay = (who) =>{
			return new Promise((resolve,reject)=>{
				resolve("去游乐园玩啦！")
			})
		}
		parkplay("me").then(res =>{
			//去玩游乐圆玩先买票
			return Buytickets(res)
		}).then(res=>{
			//买票入园后游乐设施前排队
			return queue(res)
		}).then(res=>{
			//开始玩啦
			return playing(res)
		})
```

运用Promise代码不仅简结且更易修改.

### async和await

async和await可以理解为进阶玩法，更加简洁明了，它就是Generator函数的语法糖。

```js
let parkplay = (who) =>{
   		return new Promise((resolve,reject)=>{
   			resolve("去游乐园玩啦！")
   		})
   	}
   	(async()=>{
   		let parkplay_result = await parkplay("me");
   		let buytickets_result = await buytickets(parkplay_result);
   		let queue_result = await queue(buytickets_result);
   		let playing_result = await playing(queue_result)
   	})()
```

promise(async和await)由于可以链式调用 对于开发这种多层嵌套的代码很方便，降低了代码的维护难度等等。