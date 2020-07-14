---
title: JavaScript高阶函数
author: LY
date: 2019-11-05
summary: 介绍JavaScript的三个高阶函数：filter/map/reduce
categories:
    - 前端
tags:
    - JavaScript
    - ES6语法
    - 前端面试题
---

> 以下皆是由https://www.bilibili.com/video/av59594689?from=search&seid=2169751232721048853
>
> 视频中的老师操作代码和讲义总结而成

## 前言

如果我们要实现如下三个需求的话，我们可能写出的代码如下：

**需求1.去除所有小于100的数字**

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>	
		<script>
			// 1.需求去除所有小于100的数字
			const nums=[10,20,111,222,444,40,50]
			let number=[]
			for(let n of nums){
				if(n<100){
					number.push(n)
				}
			}
			console.log(number)
		</script>
	</body>
</html>

```

**需求2.将所有n<100的数乘2**

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>	
		<script>
            // 1.需求去除所有小于100的数字
			const nums=[10,20,111,222,444,40,50]
			let number=[]
			for(let n of nums){
				if(n<100){
					number.push(n)
				}
			}
            
			console.log(number)
			//2.将所有n<100的数*2
			let numbers2=[]
			for(let n of number){
				numbers2.push(n*2)
			}
			console.log(numbers2)
		</script>
	</body>
</html>

```

**需求3.将所有的numbers的数字相加,得到最终的而结果**

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>	
		<script>
            // 1.需求去除所有小于100的数字
			const nums=[10,20,111,222,444,40,50]
			let number=[]
			for(let n of nums){
				if(n<100){
					number.push(n)
				}
			}
            
			console.log(number)
			//2.将所有n<100的数*2
			let numbers2=[]
			for(let n of number){
				numbers2.push(n*2)
			}
			console.log(numbers2)
            
			//3.将所有的numbers的数字相加,得到最终的而结果
			let totalnum=0
			for(let n of numbers2){
				totalnum+=n
			}
			console.log(totalnum)
		</script>
	</body>
</html>

```

上面三个写法简直就是应付外行boss的最佳选择

测试：没Bug呀

产品：功能实现了啊

考核：代码提交足行

队友：太简洁易读了

但是我们还是不能这样写，因为要写的代码太多了，如果不想被批还是得写精一点~~~所以我们对于上述情况有如下三个高阶函数的写法。这个其实跟python的很像，python也有这三个高阶函数并且还多了一个sorted函数，所以说语言其实都是互通的

## filter

对于上面的**第一个需求：去除所有小于100的数字**，我们可以使用如下的写法

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>	
		<script>
			const nums=[10,20,111,222,444,40,50]
			//filter函数是每一次传入的数字都会经历一次回调
			//filter中的回调函数有一个要求:每一次都必须返回一个boolean值,即true和false
			//true:当是true的时候,函数会把这次回调的n值返回到一个新的数组中
			//false:当是false的时候,函数会过滤掉这次的n
			let numbers=nums.filter(function(n){
				//使用如下写法就可以使得n<100的为true,然后加入新的数组
				return n <100
			})
			console.log(numbers)
		</script>
	</body>
</html>

```

代码更为简洁

## map

对于上面的**第二个需求：将所有n<100的数乘2**

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>	
		<script>
			const nums=[10,20,111,222,444,40,50]
			//filter函数是每一次传入的数字都会经历一次回调
			//filter中的回调函数有一个要求:每一次都必须返回一个boolean值,即true和false
			//true:当是true的时候,函数会把这次回调的n值返回到一个新的数组中
			//false:当是false的时候,函数会过滤掉这次的n
			let numbers=nums.filter(function(n){
				//使用如下写法就可以使得n<100的为true,然后加入新的数组
				return n <100
			})
			console.log(numbers)
			
			//map函数的使用
			//对于上面filter得到的结果 10 20 40 50 ,把每一次得到的结果都乘2
			let numbers=numbers.map(function(n){
				return n*2
			})
			console.log(numbers)
		</script>
	</body>
</html>

```
## reduce

reduce函数比前两个较为复杂

这个reduce可以有多种写法，在JS中如果写两个函数的话后面的函数会覆盖前面的函数，而可以写很多种写法是因为他用的是TypeScript

使用reduce来重写**第三个需求：将所有的numbers的数字相加,得到最终的而结果**

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>	
		<script>
			const nums=[10,20,111,222,444,40,50]
			//filter函数是每一次传入的数字都会经历一次回调
			//filter中的回调函数有一个要求:每一次都必须返回一个boolean值,即true和false
			//true:当是true的时候,函数会把这次回调的n值返回到一个新的数组中
			//false:当是false的时候,函数会过滤掉这次的n
			let numbers=nums.filter(function(n){
				//使用如下写法就可以使得n<100的为true,然后加入新的数组
				return n <100
			})
			console.log(numbers)
			
			//map函数的使用
			//对于上面filter得到的结果 10 20 40 50 ,把每一次得到的结果都乘2
			let numbers2=numbers.map(function(n){
				return n*2
			})
			console.log(numbers2)
			
			//reduce函数的使用
			//reduce函数的作用就是对数组中所有的函数进行汇总
			//上一次返回的值是20,40,80,100
            //末尾的0是初始值
			var total=numbers2.reduce(function(prevalue,n){
				return prevalue+n
			},0)
			
			console.log(total)
			// 第一次返回的值是 prevalue 0 + 20
			// 第二次返回的值是 pervalue 20 +40
			// 第三次返回的值是 pervalue 60 +80
			// 第四次返回的值是 prevalue 140+100
		</script>
	</body>
</html>
```

## 总和代码

上面三个代码看起来都不怎么高逼格，但是合在一起写就很能装逼了

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<script>
			const nums=[10,20,111,222,444,40,50]
			let total = nums.filter(function(n){
				return n<100
			}).map(function(n){
				return n*2
			}).reduce(function(prevalue,n){
				return prevalue+n
			},0)
			
            //或者这样
            //let total = nums.filter(n=> n<100).map(n=>n*2).reduce((pre,n)=>pre+n);
			console.log(total)
		</script>
	</body>
</html>

```

