---
title: Vue的父子传值
date: 2020-05-26 21:12:37
summary: 详解vue的父子组件传值问题
author: LY
categories:
    - 前端
tags:
    - Vue
---

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

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200526211356.png)

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

