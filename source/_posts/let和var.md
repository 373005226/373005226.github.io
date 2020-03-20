---
title: let和var的对比
author: LY
date: 2019-11-04
summary: 对比var和let定义变量的区别
categories:
    - 前端
tags:
    - ES6语法
---

> 以下皆是由https://www.bilibili.com/video/av59594689?from=search&seid=2169751232721048853
>
> 视频中的老师操作代码和讲义总结而成

## var的缺陷

JS的创始人也说过var定义变量的有很多的缺陷，var本身是没有作用域的，下面拿个案例来说明

### var在{}中的块级作用域

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<button>按钮1</button>
		<button>按钮2</button>
		<button>按钮3</button>
		<button>按钮4</button>
		<button>按钮5</button>
		<script>
			// {}中的作用域
			{
				var name = 'LY';
				console.log(name);
			}
			console.log(name);
		</script>
	</body>
</html>
```

在{}内部定义的变量，在外部也能使用

### var在if中的作用域

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<button>按钮1</button>
		<button>按钮2</button>
		<button>按钮3</button>
		<button>按钮4</button>
		<button>按钮5</button>
		<script>
			var func;
			if(true){
				var name = 'why';
				func=function(){
					console.log(name);
				}
				//变量是可以随时改变的,十分的不安全
				name = 'LY'
				func()
			}
			func()
		</script>
	</body>
</html>
```

var在if内部定义的变量在外部也能声明

### var在for里面的作用域

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<button>按钮1</button>
		<button>按钮2</button>
		<button>按钮3</button>
		<button>按钮4</button>
		<button>按钮5</button>
		<script>
			//以下代码无论点击什么按钮都会显示"第五个按钮被点击"
			var btns=document.getElementsByTagName('button');
			for(var i=0;i<btns.length;i++){
				btns[i].addEventListener('click',function(){
					console.log('第'+i+'个按钮被点击');
				})
			}
		</script>
	</body>
</html>
```

### ES5中对于for循环作用域的解决办法：闭包

对于for循环没有作用域的问题我们可以通过闭包的办法来解决

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<button>按钮1</button>
		<button>按钮2</button>
		<button>按钮3</button>
		<button>按钮4</button>
		<button>按钮5</button>
		<script>
			var btns=document.getElementsByTagName('button');
			for(var i=0;i<btns.length;i++){
				(function(i){
					btns[i].addEventListener('click',function(){
						console.log('第'+i+'个按钮被点击');
					})
				})(i)
			}
		</script>
	</body>
</html>
```

其原理如下：

```js
	//i=0;
    {
        //使用var的话每次定义都是在内部定义的，在for循环5次之后传下来的值是i=5，所以每次点击按钮都是5，因为var没有作用域，其实在内部和外部定义都没区别，每次循环i的值都会被改变
        //console.log（）里面想使用i的时候都会用到for循环改变后的i
        //i=0;
        btns[i].addEventListener('click',function(){
        	console.log('第'+i+'个按钮被点击');
        })
    }

	//使用闭包的情况下
	//这个情况就简单了，因为function有自己的作用域 ，每次使用的时候只用属于自己的i，外部for循环的i怎么改变都不会改变到内部的i变量了
    (function(i){
        btns[i].addEventListener('click',function(){
            console.log('第'+i+'个按钮被点击');
        })
    })(i)
```

var定义变量其实是在{}内部定义的	

但是闭包写出来真的对整体代码而言很别扭......

## 使用let来定义

使用let来定义的话就不会出现上面的问题，因为let有块级作用域

```js
    const btns=document.getElementsByTagName('button');
    for(let i=0;i<btns.length;i++){
        btns[i].addEventListener('click',function(){
        	console.log('第'+i+'个按钮被点击');
        })
    }
```

ES5里面要定义函数来定义作用域，而ES6里面let可以定义自己的块级作用域