---
title: v-model详解
summary: 讲解v-model的使用、本质和修饰符
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

## v-model的使用

v-model一般用在input的场景上，mustache语法只能从vue渲染到页面上，而v-model实现了一个双向绑定，在修改input的值的时候也可以修改data的值

简单的使用如下：

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<!-- 也就是双向绑定 -->
			<input type="text" v-model="message" />
			<h2>
                {{message}}
            </h2>
		</div>
		<script src="../js/vue.js"></script>
		<script>
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

在input修改的时候，h2的值也会改变



## v-model的原理

### 第一步：绑定input的value

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<!-- 1.v-bind:value绑定，但是这个方法不能再输入框里面修改外部也改变 -->
			<input type="text" :value="message" />
			<h2>
                {{message}}
            </h2>
		</div>
		<script src="../js/vue.js"></script>
		<script>
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



先可以通过控制台用app.message去修改值

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191220113018.png)

### 第2步：监听用户的输入

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<!-- 1.v-bind:value绑定，但是这个方法不能再输入框里面修改外部也改变 -->
			<!-- <input type="text" :value="message" /> -->
			
			<!-- 2.可以用input方法去绑定输入事件，然后把输入事件的值绑定到message那里就可以完成双向绑定了，
			所以我们要先获取输入框的value值(即通过event事件去获取)，然后再在methods那里把新的值赋值给message即可 -->
			<!-- <input type="text" :value="message" @input="inputmessage" /> -->
			
			
			<!-- 总结：v-model原理就是使用v-bind绑定一个value属性，然后使用v-on绑定一个input事件 -->
			
			<input type="text" :value="message" @input="inputmessage" />
			{{message}}
		</div>
		<script src="../js/vue.js"></script>
		<script>
			const app = new Vue({
				el:'#app',
				data:{
					message:'hello'
				},
				methods:{
					inputmessage(event){
						this.message=event.target.value;
					}
				}
			})
		</script>
	</body>
</html>

```

总体而言就是：

1. 使用v-bind绑定input里面的值value
2. 使用v-on绑定input的事件



## v-model结合其他类型使用

v-model还可用于其他场景，这里列举几个案例

### v-model结合radio使用

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<label for="male">
				<input type="radio" id="male" value="男" v-model="sex" />男
			</label>
			<label for="female">
				<input type="radio" id="female" value="女" v-model="sex" />女
			</label>
			<h2>{{sex}}</h2>
		</div>
		<script src="../js/vue.js"></script>
		<script>
			const app = new Vue({
				el:'#app',
				data:{
					sex:'还没选择性别'
				}
			})
		</script>
	</body>
</html>

```



### v-model结合checkbox使用

CheckBox分为单选框和多选框

#### 单选框

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<!-- 单选框 -->
		<div id='app'>
			<label for="agree">
				<input type="checkbox" id="agree" v-model="isAgree" />同意协议
			</label>
			<h2>你选择的是{{isAgree}}</h2>
			<button :disabled="!isAgree">下一步</button>
		</div>
		

		<script src="../js/vue.js"></script>
		<script>
			const app = new Vue({
				el:'#app',
				data:{
					isAgree:false,
				}
			})
		</script>
	</body>
</html>

```

#### 多选框

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<!-- 多选框 -->
		<div id='app'>
			<input type="checkbox" value="唱" v-model="hobbies" />唱
			<input type="checkbox" value="跳" v-model="hobbies" />跳
			<input type="checkbox" value="rap" v-model="hobbies" />rap
			<input type="checkbox" value="music" v-model="hobbies" />music
			<h2>你的爱好是{{hobbies}}</h2>
		</div>
		<script src="../js/vue.js"></script>
		<script>
			const app = new Vue({
				el:'#app',
				data:{
					hobbies:[]
				}
			})
		</script>
	</body>
</html>

```

### 选择框的使用

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<!-- 单选 -->
			<select name="abc" v-model="fruit">
				<option value="苹果" >苹果</option>
				<option value="香蕉" >香蕉</option>
				<option value="榴莲" >榴莲</option>
				<option value="葡萄" >葡萄</option>
			</select>
			<h2>{{fruit}}</h2>
			
			
			<!-- 多选 -->
			<select name="abc" v-model="fruits" multiple>
				<option value="苹果" >苹果</option>
				<option value="香蕉" >香蕉</option>
				<option value="榴莲" >榴莲</option>
				<option value="葡萄" >葡萄</option>
			</select>
			<h2>{{fruits}}</h2>
		</div>
		<script src="../js/vue.js"></script>
		<script>
			const app = new Vue({
				el:'#app',
				data:{
					message:'hello',
					fruit:'香蕉',
					fruits:[]
				}
			})
		</script>
	</body>
</html>

```



## v-model修饰符

#### 懒加载（v-model.lazy）

懒加载也就是敲了回车的时候值才会被绑定

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<!-- 不是实时绑定了，在敲回车的时候才去绑定message的值，lazy修饰符只会在失去焦点和回车的时候才会去绑定 -->
			<input type="text" v-model.lazy="message" />
			<br />
			{{message}}
		</div>
		<script src="../js/vue.js"></script>
		<script>
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



### v-model.number

input输入框输入的值，默认都是string类型，v-model-number可以把string类型转换为number类型

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<!-- 输入框默认输入的就是String类型 -->
			<!-- <input type="number" v-model="age" /> -->
			
			<!-- 所以要修改成为number类型才可以 -->
			<input type="number" v-model.number="age" />
			<h2>{{age}}-{{typeof age}}</h2>
		</div>
		<script src="../js/vue.js"></script>
		<script>
			const app = new Vue({
				el:'#app',
				data:{
					message:'hello',
					age:''
				}
			})
		</script>
	</body>
</html>

```



### v-model trim修饰符

trim去除两边的空格

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<!-- 也就是双向绑定 -->
<!--			<input type="text" v-model="name" />-->
			<!-- 默认是不开启去除两边空格的，无论在输入框的左边还是右边输入空格都会显示在console.log -->
			
			<!-- 使用trim修饰符就可以免去空格了 -->
			 <input type="text" v-model.trim="name" />
			
			{{name}}
		</div>
		<script src="../js/vue.js"></script>
		<script>
			const app = new Vue({
				el:'#app',
				data:{
					message:'hello',
					name:''
				}
			})
		</script>
	</body>
</html>

```




