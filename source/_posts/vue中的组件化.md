---
title: Vue组件化的使用
summary: 详解组件化开发的使用及注意事项
date: 2019-12-20
author: LY
categories:
    - 前端
tags:
    - Vue
---

> 以下皆是由https://www.bilibili.com/video/av59594689?from=search&seid=2169751232721048853
>
> 视频中的老师操作代码和讲义总结而成

##  组件化的最基本使用

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<!-- 以下这种方法在Vue2.X是看不到了，基本上都是使用语法糖去写 -->
		<div id='app'>
			<my-cpn></my-cpn>
			<my-cpn></my-cpn>
			<my-cpn></my-cpn>
			<my-cpn></my-cpn>
			<my-cpn></my-cpn>
			<my-cpn></my-cpn>
		</div>
		
		<script src="../js/vue.js"></script>
		<script>
			
			//1.创建组件
			const cpnC = Vue.extend({
				template:`
				<div>
					<h2>我是标题</h2>
					<p>我是内容hhhhhhhhh</p>
				</div>`
			})

			//2.注册组件（全局组件）
			Vue.component('my-cpn',cpnC)
				
			const app = new Vue({
				el:'#app',
				data:{
					message:'hello'
				}
			})
		</script>
	</body>
</html>

```

组件化的最基本使用的步骤就是：

1. 创建组件
2. 注册组件
3. 使用组件



现在Vue的版本的源码已经发布到3.0了，等正式发布还需要一段时间，以前老版本使用的是上面代码的写法，但是实际上使用的方法还是如上代码的写法，只是写法更简单了而已



## 全局组件和局部组件

### 全局组件

如上代码的写法是用全局组件去注册的，也就是所有组件都可用，如下代码所示：

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<!-- 以下这种方法在Vue2.X是看不到了，基本上都是使用语法糖去写 -->
		<div id='app'>
			<my-cpn></my-cpn>
			<my-cpn></my-cpn>
			<my-cpn></my-cpn>
			<my-cpn></my-cpn>
			<my-cpn></my-cpn>
			<my-cpn></my-cpn>
		</div>
		<div id='app2'>
			<my-cpn></my-cpn>
			<my-cpn></my-cpn>
			<my-cpn></my-cpn>
			<my-cpn></my-cpn>
		</div>
		<script src="../js/vue.js"></script>
		<script>

			//1.创建组件
			const cpnC = Vue.extend({
				template:`
				<div>
					<h2>我是标题</h2>
					<p>我是内容hhhhhhhhh</p>
				</div>`
			})

			//2.注册组件
			Vue.component('my-cpn',cpnC)


			const app = new Vue({
				el:'#app',
				data:{
					message:'hello'
				}
			})

			const app2 = new Vue({
				el:'#app2'
			})
		</script>
	</body>
</html>

```

通过控制台一看，即使是在app里面注册的，在app2都还是可以用的



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191220160539.png)



### 局部组件

如果要使用局部组件的话，那么就要在app里面注册一个局部组件

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<!-- 以下这种方法在Vue2.X是看不到了，基本上都是使用语法糖去写 -->
		<div id='app'>
			<cpn></cpn>
		</div>
		
		<div id='app2'>
			<cpn></cpn>
		</div>
		
		<script src="../js/vue.js"></script>
		<script>
			
			//1.创建组件
			const cpnC = Vue.extend({
				template:`<div>
					<h2>我是标题</h2>
					<p>我是内容hhhhhhhhh</p>
				</div>`
			})
			//2.注册组件(这个是全局组件，也就是意味着能在多个Vue的实例化下使用)
			// Vue.component('cpn',cpnC)
			
			
			const app = new Vue({
				el:'#app',
				data:{
					message:'hello'
				},
				components:{
					cpn : cpnC
				}
			})
			
			const app2 = new Vue({
				el:'#app2'
			})
		</script>
	</body>
```



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191220160725.png)

## 父子组件的使用



```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<!-- 以下这种方法在Vue2.X是看不到了，基本上都是使用语法糖去写 -->
		<div id='app'>
			<cpn></cpn>
		</div>
		
		
		<script src="../js/vue.js"></script>
		<script>
			// 创建组件1:子组件
			const cpnC1 = Vue.extend({
				template:`<div>
					<h2>我是标题1</h2>
					<p>我是内容11111111</p>
				</div>`
			})

			// 创建组件2:父组件
			const cpnC2 = Vue.extend({
				template:`<div>
					<h2>我是标题2</h2>
					<p>我是内容222222222</p>
					<cpn1></cpn1>
				</div>`
				,
				components:{
					cpn1:cpnC1
				}
			})
			
			//root组件
			const app = new Vue({
				el:'#app',
				data:{
					message:'hello'
				},
				components:{
					cpn : cpnC2
				}
			})
			
		</script>
	</body>
```



<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191220161941.png" style="zoom:50%;" />

这个其实在使用过程中，子组件就已经被编译好了，因为在编译cpn2的时候发现有个cpn1，如果在局部组件发现没有cpn1，那么就去全局组件里面找



## 组件注册的语法糖写法

### 全局注册的语法糖写法

可以用Vue新版本的语法糖写法， 省去extend的步骤来去注册组件

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<cpn></cpn>
		</div>
		
		<script src="../js/vue.js"></script>
		<script>
			// const cpnC = Vue.extend()
			
			// 1.注册全局组件的语法糖
			Vue.component('cpn',{
				template:`<div>
					<h2>我是标题</h2>
					<p>我是内容hhhhhhhhh</p>
				</div>`
			})
			
			const app = new Vue({
				el:'#app',
				data:{
					message:'hello'
				}
			})
		</script>
	</body>
```



### 局部组件注册的语法糖

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<cpn2></cpn2>
		</div>
		
		<script src="../js/vue.js"></script>
		<script>
			
			const app = new Vue({
				el:'#app',
				data:{
					message:'hello'
				},
				// 注册局部组件的语法糖
				components:{
					'cpn2':{
						template:`<div><h2>标题2</h2></div>`
					}
				}
			})
		</script>
	</body>
```



## 组件模板的分离写法

上面代码看起来就很乱，在js里面添加html的代码看起来十分的不美观，这里介绍把html代码分离出去的写法

### 方法1：把代码写到script里面

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<cpn1></cpn1>
		</div>
		
		
		<script src="../js/vue.js"></script>
		
		<!-- 写法1，使用script -->
		<script type="text/x-template" id='cpn1'>
			<div>
				<h2>{{title}}</h2>
			</div>
		</script>
        
		<script>
			Vue.component('cpn1',{
				template:'#cpn1',
				data(){
					return{
						title:'abc'
					}
				}
			})

			const app = new Vue({
				el:'#app',
				data:{
					message:'hello'
				}
			})
		</script>
	</body>
</html>

```



### 方法2：写到template里面

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<cpn2></cpn2>
		</div>
		
		
		<script src="../js/vue.js"></script>
		

		<!-- template标签 -->
		<template id='cpn2'>
			<div>
				<h2>我是方法二的标题</h2>
			</div>
		</template>
		
		<script>
			Vue.component('cpn2',{
				template:'#cpn2',
			})

			const app = new Vue({
				el:'#app',
				data:{
					message:'hello'
				}
			})
		</script>
	</body>
</html>

```



## 组件数据存放的问题

上面代码都是写死的，如果每个组件都需要有自己的值的话就需要在注册的时候添加自己的data值

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<cpn></cpn>
		</div>
		
		
		<script src="../js/vue.js"></script>
		
		<template id='cpn'>
			<div>
				<h2>{{title}}</h2>
			</div>
		</template>
		
		<script>
			Vue.component('cpn',{
				template:'#cpn',
				data(){
					return{
						title:'abc'
					}
				}
			})
			
			const app = new Vue({
				el:'#app',
				data:{
					message:'hello'
				}
			})
		</script>
	</body>
</html>

```



这里的data不能是个对象类型，不然会报错，如下错误代码所示：

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<cpn></cpn>
		</div>
		
		
		<script src="../js/vue.js"></script>
		
		<template id='cpn'>
			<div>
				<h2>{{title}}</h2>
			</div>
		</template>
		
		<script>
			Vue.component('cpn',{
				template:'#cpn',
				data(){
					title:'abc'
				}
			})
			
			const app = new Vue({
				el:'#app',
				data:{
					message:'hello'
				}
			})
		</script>
	</body>
</html>

```



![image-20191220164939991](C:\Users\LY\Pictures\Camera Roll\image-20191220164939991.png)



因为如果直接这样写

```
data(){
	
}
```

这样不明摆着就是一个ES6语法来写的增强函数吗，所以这个要添加个返回值才可以

### 为什么data必须设计成一个函数

详情看：https://www.bilibili.com/video/av59594689?p=57

这里拿个加减法的变量来演示

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<!-- 1. 那么问题就来了，那么多个组件，理论上来说应该是公用一个counter对象，但是一个选择的加减，另外的两个都不变 -->
		<div id='app'>
			<cpn></cpn>
			<cpn></cpn>
			<cpn></cpn>
		</div>

		<template id="cpn">
			<div>
				<h2>当前计数：{{counter}}</h2>
				<button @click="add">+</button>
				<button @click="sub">-</button>
			</div>
		</template>
		
		<script src="../js/vue.js"></script>
		<script>
			Vue.component('cpn', {
				template: '#cpn',
				data() {
					// 2.问题就在这,每次调用一个对象的时候,都会调用一个data函数,每次调用的时候都会给他一个新的值
					return {
						counter: 0
					}
				},
				methods: {
					add(){
						console.log('add被执行')
						this.counter++
					},
					sub(){
						console.log("减法被执行"),
						this.counter--
					}
				}
			})

			const app = new Vue({
				el: '#app',
				data:{
					message:'hello'
				}
			})
		</script>

	</body>
</html>

```



这里的代码是有个返回值的，如果没有返回值的话，那么每个count+1的时候。每个count都+1了，因为他们作用域是同一个，而return的话，每次都有一个新的对象返回

如代码所示：

```js
<script type="text/javascript">
			const obj={
				name:'LY',
				age:21
			}

			function abc(){
				return obj
			}

			let obj1=abc()
			let obj2=abc()
			let obj3=abc()

			obj1.name='LL'
			console.log(obj1)
			console.log(obj2)
			console.log(obj3)
</script>
```



每个实例化后的值的返回值都是一样的，如果obj1修改了其中的一个属性，那么全部obj2和obj3的属性都会发生修改，因为他们的作用域是一样的。



