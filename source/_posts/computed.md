---
title: 为什么要使用computed
summary: 讲解了为什么要使用计算属性以及计算属性的使用场景
date: 2019-12-19
author: LY
categories:
    - 前端
tags:
    - Vue
---

> 以下皆是由https://www.bilibili.com/video/av59594689?from=search&seid=2169751232721048853
>
> 视频中的老师操作代码和讲义总结而成

## 计算属性的基本使用

计算属性在我们要拼接属性的时候可以用到，比如要拼接两个姓氏和名字

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			{{fullName}}
		</div>
		<script src="../js/vue.js"></script>
		<script>
			const app = new Vue({
				el:'#app',
				data:{
					name:'ly',
					age:21
				},
				computed:{
					fullName:function(){
						return this.name+' 空格 '+this.age
					}
				}
			})
		</script>
	</body>
</html>
```

虽然说methods方法也可以，但是如果用methods方法的话要在里面加个括号，比如

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			{{getfullname()}}
		</div>
		<script src="../js/vue.js"></script>
		<script>
			const app = new Vue({
				el:'#app',
				data:{
					name:'ly',
					age:21
				},
				methods:{
					getfullname:function () {
						return this.name+' 空格 '+this.age
					}
				}
			})
		</script>
	</body>
</html>
```

但是这样就要使用到括号，我们想要一个属性结果但是显示的是一个函数这样就会显得...（emmm强行解释一波）

此外计算属性computed还可以用在计算类的情景上面

## 计算属性的复杂应用

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<h2>{{totalprice}}</h2>
		</div>
		<script src="../js/vue.js"></script>
		<script>
			const app = new Vue({
				el:'#app',
				data:{
					books:[
						{name:'book1',price:100},
						{name:'book2',price:200},
						{name:'book3',price:300},
						{name:'book4',price:400},
					]
				},
				computed:{
					totalprice:function(){
						let res=0
						for(let i =0;i<this.books.length;i++){
							res += this.books[i].price
						}
						return res
					}
				}
			})
		</script>
	</body>
</html>
```

如代码所示，通常用在这个方面的情景上，因为计算属性是有缓存的，计算一次之后vue就会把它缓存，第二次使用就可以调用缓存来使用了，这个在下面有说



## 计算属性computed的本质

computed的一般使用方法是

```
fullname(){
	
}
```

这个其实只是计算属性的简写

他的本质是有一个set和一个get方法

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191219150354.png)



因为一般只用到一个get方法，所以就简写成

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191219150450.png)



如果一定要使用set方法也可以的，比如在控制台或者其他需要修改计算属性的时候就可以调用到set方法

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title></title>
</head>
<body>
<div id='app'>
    {{fullname}}
</div>
<script src="../js/vue.js"></script>
<script>
    const app = new Vue({
        el:'#app',
        data:{
            firname:'lly',
            lastname:'yyl',
            message:'hello'
        },
        computed:{
            // 1. 就是因为一般是没有set方法的,所以默认是只读操作get的,等同于如下代码
            // fullname:function(){
            //     return this.firname+' '+ this.lastname
            // }

            //2.计算属性共有两个方法
            // fullname:{
            //     set(){
            //         //而默认只要实现get方法就好了，set方法是不需要实现的，也不想别人有一个set方法
            //     },
            //     get(){
            //         //默认使用的就是get方法，所以浏览器显示的是abc
            //         return this.firname+' '+ this.lastname
            //     }
            // }

            //3.而不要set方法也不会被报错
            // fullname:{
            //     get(){
            //         //默认使用的就是get方法，所以浏览器显示的是abc
            //         return this.firname+' '+ this.lastname
            //     }
            // }

            //4.所以最终版是可以这样的，fullname:function就是get方法
            // fullname:function(){
            //     return this.firname+' '+ this.lastname
            // }

            //5.如果实在是要使用set方法的话，也是可以实现的

            //然后在浏览器的控制台实现app.fullname = 'abc'
            //就可以打印---set方法实现--，但是这个一般要用的话都是要加入一个变量来保存变量来改变值的，如第六点所示
            // fullname:{
            //     set(){
            //         console.log('---set方法实现--')
            //     },
            //     get(){
            //         //默认使用的就是get方法，所以浏览器显示的是abc
            //         return this.firname+' '+ this.lastname
            //     }
            // }

            //6.使用新的值去代替原来的值
            fullname:{
                set(newValue){
                    const names = newValue.split(' ')
                    this.firname = names[0]
                    this.lastname = names[1]
                },
                get(){
                    //默认使用的就是get方法，所以浏览器显示的是abc
                    return this.firname+' '+ this.lastname
                }
            }
        }
    })
</script>
</body>
</html>

```

这里我直接用控制台去修改变量

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191219151013.png)

控制台调用app.fullname(我的计算属性名字就可以调用到set方法



## 计算属性computed的缓存

虽然很多地方methods方法也可以同样实现，但是我们一般使用的都是computed方法，因为computed方法有缓存，如果要计算的值是一样的话vue会添加到缓存里面，第二次调用就会直接去调用缓存，而不用去第二次计算

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
<!--&lt;!&ndash;			方法1：直接拼接字符串(语法过于繁琐)&ndash;&gt;-->
<!--			<h2>{{firname}} {{lastname}}</h2>-->

<!--&lt;!&ndash;			方法2：使用methods方法调用(于情不通，要显示的是属性，但是这个是方法,而且是每次调用都要初始化)&ndash;&gt;-->
<!--			<h2>{{getfullname()}}</h2>-->

<!--&lt;!&ndash;			方法3:使用computed方法调用&ndash;&gt;-->
<!--			<h2>{{fullname}}</h2>-->



			<!-- 计算方法只被调用一次，剩下的都是靠缓存区编写 -->
			{{getfullname()}}
			{{getfullname()}}
			{{getfullname()}}


			{{fullname}}
			{{fullname}}
			{{fullname}}

<!--			方法每次都要调用，但是计算属性只调用一次-->
<!--			所以理所当然计算属性的性能更高，这里虽然看不出来，但是如果是methods方法有很多for方法，那么如果每次都要调用一次的话性能就很低了-->
		</div>
		<script src="../js/vue.js"></script>
		<script>
			const app = new Vue({
				el:'#app',
				data:{
					firname:'lly',
					lastname:'yyl',
					message:'hello'
				},
				methods:{
					getfullname:function(){
						console.log('方法属性调用')
						return this.firname+' '+ this.lastname
					}
				},
				computed:{
					fullname:function(){
						console.log('计算方法调用')
						return this.firname+' '+ this.lastname
					}
				}
			})
		</script>
	</body>
</html>

```

如上面代码所示，控制台的打印出来的结果是



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191219152147.png)

只要fullname的firname和lastname两个属性的值不变，那么就只调用一次