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
		<div id = 'app2'>
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



## 父子组件的通信

在开发中，都是在一个大的组件中请求完数据，然后子组件再使用v-for去遍历数据的值。因为如果每个组件的数据都要去向后台发送请求数据的话，那么对服务器的压力是很大的。



如果是父组件向子组件传递数据，那么只需要通过`props`的方式去传递数据。如果是子组件向父组件传递数据的话，那么就需要使用到发送事件`($emit Events)`

### 父组件传递值到子组件

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div id="app">
    
    <cpn :cmovie="movie" :cmessage="message"></cpn>
</div>

<script src="../js/vue.js"></script>

<template id="cpn">
    <div>
        <p>{{cmovie}}</p>
        <h2>{{cmessage}}</h2>
    </div>
</template>
<script>
    const cpn={
      template:`#cpn`,
      props:['cmovie','cmessage'],
    }
    const app = new Vue({
      el:'#app',
      data:{
          message:'hello',
          movie: ['霸王别姬', '阿飞正传', '胭脂扣', '英雄本色', '新上海滩', '东邪西毒', '倩女幽魂'],
      },
      components:{
        cpn
      }
    })
</script>
</body>
</html>
```

#### props可以有类型限制

props不仅仅可以使用数组的方式的写法，还可以使用对象的方式

比如：

```html
 <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div id="app">
    <cpn :cmovie="movie" :cmessage="message"></cpn>
</div>

<script src="../js/vue.js"></script>

<template id="cpn">
    <div>
        <p>{{cmovie}}</p>
        <h2>{{cmessage}}</h2>
    </div>
</template>
<script>
    const cpn={
      template:`#cpn`,
      // props:['cmovie','cmessage'],//这里一定不能使用驼峰式命名
      props:{
        cmovie: Array,
        cmessage: String
      }
    }
    const app = new Vue({
      el:'#app',
      data:{
          message:'hello',
          movie: ['霸王别姬', '阿飞正传', '胭脂扣', '英雄本色', '新上海滩', '东邪西毒', '倩女幽魂'],
      },
      components:{
        cpn
      }
    })
</script>
</body>
</html>
```

比如如上代码，cmovie就只能为Array，cmessage就只能是String方法

props支持的类型限制有：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191220180150.png" style="zoom:67%;" />



#### props设置默认值

props除了可以设置类型限制之外还可以设置默认值

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div id="app">
<!--    <cpn :cmovie="movie" :cmessage="message"></cpn>    -->
    <cpn ></cpn>

</div>

<script src="../js/vue.js"></script>

<template id="cpn">
    <div>
        <p>{{cmovie}}</p>
        <h2>{{cmessage}}</h2>
    </div>
</template>
<script>
    const cpn={
      template:`#cpn`,
      // props:['cmovie','cmessage'],//这里一定不能使用驼峰式命名
      props:{
        cmovie: {
          type:Array,
          default(){
            return ['霸王别姬']	//这个数组的话最好是要用一个返回值去写，因为在vue的2.5.17的时候如果不使用返回值就会报错
          }
        },
        cmessage: {
          type:String,
          default:'hello world'
        }
      }
    }
    const app = new Vue({
      el:'#app',
      data:{
          message:'hello',
          movie: ['霸王别姬', '阿飞正传', '胭脂扣', '英雄本色', '新上海滩', '东邪西毒', '倩女幽魂'],
      },
      components:{
        cpn
      }
    })
</script>
</body>
</html>
```



这里的返回值必须是一个函数，因为类型是**对象或者是数组**的时候，默认值必须是一个函数，也就是必须如下的写法

```js
default(){
	return{
        
    }
}
```



如果不使用函数式的写法的话就会报错，如下代码所示：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<div id="app">
    <!--    <cpn :cmovie="movie" :cmessage="message"></cpn>    -->
    <cpn ></cpn>

</div>

<script src="../js/vue.js"></script>

<template id="cpn">
    <div>
        <p>{{cmovie}}</p>
        <h2>{{cmessage}}</h2>
    </div>
</template>
<script>
  const cpn={
    template:`#cpn`,
    // props:['cmovie','cmessage'],//这里一定不能使用驼峰式命名
    props:{
      cmovie: {
        type:Array,
        default:['霸王别姬']	//这里修改了个错误的写法
      },
      cmessage: {
        type:String,
        default:'hello world'
      }
    }
  }
  const app = new Vue({
    el:'#app',
    data:{
      message:'hello',
      movie: ['霸王别姬', '阿飞正传', '胭脂扣', '英雄本色', '新上海滩', '东邪西毒', '倩女幽魂'],
    },
    components:{
      cpn
    }
  })
</script>
</body>
</html>
```



<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191220183449.png" style="zoom:67%;" />

#### props的其他写法

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191220183813.png" style="zoom:67%;" />

甚至还可以使用自定义对象去创建

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191221102053.png" style="zoom:67%;" />

#### props的驼峰标识

props不能随便使用驼峰标识，因为这个可能会有点会报错，原因如下：

如下代码是正常的写法：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title></title>
</head>
<body>
<div id='app'>
    <cpn :cinfo="info"></cpn>
</div>

<template id='cpn'>
    <h2>{{cinfo}}</h2>
</template>

<script src="../js/vue.js"></script>
<script>

  const cpn={
    template:'#cpn',
    props:{
      cinfo:{
        type:Object,
        default(){
          return {

          }
        }
      }
    }
  }

  const app = new Vue({
    el:'#app',
    data:{
      message:'hello',
      info:{
        name:'LY',
        age:21
      }
    },
    components:{
      cpn
    }
  })
</script>
</body>
</html>
```

如果使用驼峰标识的话会不能正常显示，代码如下：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title></title>
</head>
<body>
<div id='app'>
    <cpn :cInfo="info"></cpn>
</div>

<template id='cpn'>
    <h2>{{cInfo}}</h2>
</template>

<script src="../js/vue.js"></script>
<script>

  const cpn={
    template:'#cpn',
    props:{
      cInfo:{
        type:Object,
        default(){
          return {

          }
        }
      }
    }
  }

  const app = new Vue({
    el:'#app',
    data:{
      message:'hello',
      info:{
        name:'LY',
        age:21
      }
    },
    components:{
      cpn
    }
  })
</script>
</body>
</html>
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191221104912.png)

这个是因为`v-bind`是不支持驼峰的，至少是到我写这篇博客的`Vue`正式发布版本都还没支持，如果要`v-bind`要使用驼峰的标识的话要对驼峰的部分用`-`分开，比如如下代码：

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title></title>
</head>
<body>
<div id='app'>
    <cpn :c-info="info"></cpn>
</div>

<template id='cpn'>
    <h2>{{cInfo}}</h2>
</template>

<script src="../js/vue.js"></script>
<script>

  const cpn={
    template:'#cpn',
    props:{
      cInfo:{
        type:Object,
        default(){
          return {

          }
        }
      }
    }
  }

  const app = new Vue({
    el:'#app',
    data:{
      message:'hello',
      info:{
        name:'LY',
        age:21
      }
    },
    components:{
      cpn
    }
  })
</script>
</body>
</html>
```

如下代码就不会报警告和能正常显示

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191221105231.png" style="zoom: 50%;" />

如下代码也是同理：(`childMyMessage`可以拆分为`child-My-Message`)

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title></title>
</head>
<body>
<div id='app'>
    <cpn :c-Info="info" :child-My-Message='message'></cpn>
</div>

<template id='cpn'>
    <div>
        <h2>{{cInfo}}</h2>
        <h2>{{childMyMessage}}</h2>
    </div>
</template>

<script src="../js/vue.js"></script>
<script>

  const cpn={
    template:'#cpn',
    props:{
      cInfo:{
        type:Object,
        default(){
          return {}
        }
      },
      childMyMessage:{
        type:String,
        default:''
      }
    }
  }

  const app = new Vue({
    el:'#app',
    data:{
      message:'hello',
      info:{
        name:'LY',
        age:21
      }
    },
    components:{
      cpn
    }
  })
</script>
</body>
</html>
```



### 子组件传递数据到父组件

#### 应用场景

子组件传递父组件是需要自定义事件来去传递的

子组件向父组件传递数据一般是用在类似京东的移动端网页



<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191221113225.png" style="zoom:67%;" />

出现在子组件点击手机数码的时候，要向最大的那个父组件告诉他我点击了`catelogy=“手机数码”`，然后父组件返回这个数据给子组件显示。

#### 子组件自定义数据

如下代码所示，先子组件渲染页面，先能在自己组件中打印出值

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title></title>
</head>
<body>
<!--父组件模板-->
<div id='app'>
     <cpn></cpn>
</div>

<!--子组件模板-->
<template id='cpn'>
    <div>
        <button type="button" v-for="item in categories" @click="btnclick(item)">{{item.name}}</button>
    </div>
</template>


<script src="../js/vue.js"></script>
<script>
  // 1.子组件
  const cpn = {
    template: `#cpn`,
    data() {
      return {
        categories: [{
          'id': 1,
          name: "热门推荐"
        },
          {
            'id': 2,
            name: "手机数码"
          },
          {
            'id': 3,
            name: "电脑办公"
          },
        ]
      }
    },
    methods: {
      btnclick(item) {
        console.log(item)
      }
    }
  }

  // 父组件
  const app = new Vue({
    el: '#app',
    data: {
      message: 'hello',
      movie: ['霸王别姬', '阿飞正传', '胭脂扣', '英雄本色', '新上海滩', '东邪西毒', '倩女幽魂']
    },
    components: {
      cpn
    }
  })
</script>
</body>
</html>
```



然后要使用`$emit`发送一个事件给父组件，然后父组件就可以收到这个事件了

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title></title>
</head>
<body>
<!--父组件模板-->
<div id='app'>
    <!--1. 是不可以直接使用父组件的movie的，要去通过props去间接调用 -->
    <cpn @itemclick='cpnclick'></cpn>
    <!-- <cpn></cpn> -->
</div>

<!--子组件模板-->
<template id='cpn'>
    <div>
        <button type="button" v-for="item in categories" @click="btnclick(item)">{{item.name}}</button>
    </div>
</template>


<script src="../js/vue.js"></script>
<script>
  // 1.子组件
  const cpn = {
    template: `#cpn`,
    data() {
      return {
        categories: [{
          'id': 1,
          name: "热门推荐"
        },
          {
            'id': 2,
            name: "手机数码"
          },
          {
            'id': 3,
            name: "电脑办公"
          },
        ]
      }
    },
    methods: {
      btnclick(item) {
        this.$emit('itemclick',item)
      }
    }
  }

  // 父组件
  const app = new Vue({
    el: '#app',
    data: {
      message: 'hello',
      movie: ['霸王别姬', '阿飞正传', '胭脂扣', '英雄本色', '新上海滩', '东邪西毒', '倩女幽魂']
    },
    components: {
      cpn
    },
    methods:{
      cpnclick(item){
        console.log('cpnclick触发',item)
      }
    }
  })
</script>
</body>
</html>

```

注：这里事件不能使用驼峰标识，只有在脚手架里面可以使用，而在这个场景中是不可以使用驼峰标识的，因为在脚手架中在`template`等所有的组件对象都渲染成一个`render`函数，是可以正常解析的，但是在`html`文件里面引用的时候解析不了

子组件向父组件传递数据的流程如下：

1. 在子组件中，通过`$emit`来触发事件
2. 在父组件中，通过`v-on`监听子组件的事件

## 父子组件之间的互相访问

### 父组件访问子组件

#### $children访问子组件的方法

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
    <cpn></cpn>
    <cpn></cpn>
    <button @click="btnclick">button</button>
</div>

<template id="cpn">
    <div>
        我是子组件
    </div>
</template>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el:'#app',
    data:{
      message:'hello'
    },
    methods:{
      btnclick(){
        console.log(this.$children)
        this.$children[0].showmessage()
      }
    },
    components:{
      cpn:{
        template:'#cpn',
        data(){
          return{
            name:'我是子组件的name'
          }
        },
        methods:{
          showmessage(){
            console.log('showmessage')
          }
        }
      }
    }
  })
</script>
</body>
</html>

```

然后就可以父组件就可以通过下标来获取子组件的方法了

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191221145323.png" style="zoom:67%;" />

但是这个方式不经常用，因为如果中间插入了其他的组件的话，比如

```html
<div id='app'>
    <cpn></cpn>
    <cpn></cpn>
    <mycpn></mycpn>
    <cpn></cpn>
    <button @click="btnclick">button</button>
</div>
```

那么下标的值也跟着改变，所以$children的方式是不经常用的，经常用的是下面的这个方法：

#### $refs

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title></title>
</head>
<body>

<div id='app'>
    <cpn ref="aaa"></cpn>
    <cpn ref="bbb"></cpn>
    <cpn ref="ccc"></cpn>
    <button @click="btnclick">button</button>
</div>

<template id="cpn">
    <div>
        我是子组件
    </div>
</template>

<script src="../js/vue.js"></script>
<script>
  const app = new Vue({
    el:'#app',
    data:{
      message:'hello'
    },
    methods:{
      btnclick(){
        console.log(this.$refs)
      }
    },
    components:{
      cpn:{
        template:'#cpn',
        data(){
          return{
            name:'我是子组件的name'
          }
        },
        methods:{
          showmessage(){
            console.log('showmessage')
          }
        }
      }
    }
  })
</script>
</body>
</html>
```

refs是每次都可以获取想要获取的值

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191221150705.png" style="zoom:67%;" />

### 子组件访问父组件

这个子组件访问父组件用的是十分的少的，而且开发中也不建议这样去使用

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
		
		<template id="cpn">
			<div>
				<h2>我是cpn组件</h2>
				<ccpn></ccpn>
			</div>
		</template>
		
		<template id='ccpn'>
			<div>
				<h2>我是子组件</h2>
				<button type="button" @click="btnclick">button</button>
			</div>
		</template>
		
		<script src="../js/vue.js"></script>
		<script>
			const app = new Vue({
				el:'#app',
				data(){
					return{
						name:'LY'
					}
				},
				components:{
					cpn:{
						template:'#cpn',
						data(){
							return{
								name:"CCpn LY"
							}
						},
						methods:{
							// btnclick(){
							// 	//访问父组件 $parent
							// 	console.log(this.$parent)
							// }
						},
						components:{
							ccpn:{
								template:'#ccpn',
								methods:{
									btnclick(){
										//访问父组件 $parent
										console.log(this.$parent)
										console.log(this.$parent.name)
                                        //这个甚至可以直接访问root组件，也就是Vue实例
										console.log(this.$root.name)
									}
								}
							}
						}
					}
				}
			})
		</script>
	</body>
</html>

```

子访问父组件的`$parents`的方法十分的简单，但是也用得少，因为这样的耦合度太高

