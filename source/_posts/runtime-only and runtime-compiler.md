---
title: runtime-compiler和runtime-only区别
author: LY
date: 2019-11-12
summary: 详细介绍这两者的区别
categories:
    - 前端
tags:
    - Vue
---

> 在使用Vue cli2创建项目的时候，会出现选择runtime-compiler和runtime-only的选项，这里我介绍这两者的区别

> 以下皆是由https://www.bilibili.com/video/av59594689?from=search&seid=2169751232721048853
>
> 视频中的老师操作代码和讲义总结而成

## runtime-compiler和runtime-only的区别

两者的区别主要是在`main.js`里面

**runtime-compiler**

```js
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  components: { App },
  template: '<App/>',
})

```

**runtime-only**

```js
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false

new Vue({
  el: '#app',
  render: h => h(App)
  }
    //箭头函数的本质
    // render:function(createElement){
    //   return createElement(App)
    // }
        
    //使用ES6语法的本质
    //render:h=>{
    //    return h(App)
    //  	}
)
```

一个是使用了render箭头函数，一个是使用正常的组件注册写法

他们两个的使用的性能也是不同的

## 两者性能区别

在**runtime-compiler**里面运行流程如下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191112101329.png)

在template传递给Vue的时候，先在vm.options.template里面做一个保存，然后parse(解析)成一个ast（abstract syntax tree抽象语法数），然后再compile（编译）成一个render函数，最后再翻译成一个virtual dom（虚拟dom），最后再把虚拟dom树渲染成为一个真正的界面

整体来说，**runtime-compiler**的流程就是：**template -> ast -> render ->vdom ->UI**

而在runtime-only中，他的流程只是render->vdom

所以runtime-only使用更少的代码去实现同样的功能，所以runtime-only性能更高

## runtime-only代码解析

在**runtime-compiler**中，使用的是正常的组件化注册

而在上面我所说的，**runtime-only**没有经历过template这个流程，也就是说template没有用，其原理如下：

render函数内部实现的原理是这样的

```js
  render: h => h(App)  
  //可以写成下面的写法

  //render函数内部实现原理
  render:function(createElement,{标签的属性},['标签的内容，比如<h2>hello world</h2>里面的hello world']){
    return createElement('标签')
  }
```

我们可以随便使用**runtime-only**的main.js修改一下

### 方法1（使用createElement注册组件）:

```js
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false

/* eslint-disable no-new */

new Vue({
  el: '#app',
  // render: h => h(App)
  //render->vdom
  //所以这个性能更高

  // 方法1
   render:function(createElement){
    return createElement('h2',{class:'box'},['hello world'])
    }
  }
)
```

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191112110149.png" style="zoom:50%;" />

### 方法2（在里面再添加一个createElement注册组件）：

下面是在里面再去注册一个button组件

```js
  render: function(createElement) {
    return createElement('h2', {
      class: 'box'
    }, ['hello world', createElement('button', ['按钮'])])}
```

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191112110204.png" style="zoom:50%;" />

方法2就是在里面再嵌套一层createElement函数

### 方法3（使用组件化注册）：

```js
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false

/* eslint-disable no-new */
const cpn = {
  template: `<div>{{message}}</div>`,
  data() {
    return {
      message: '我是组件message'
    }
  }
}

new Vue({
  el: '#app',
  // render: h => h(App)
  //render->vdom
  //所以这个性能更高

    // 方法3：使用组件去传入值
    render:function(createElement){
      return createElement(cpn)
    }
  }
)

```

这个代码运行要在webpack.base.conf里面修改配置

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191112110219.png" style="zoom:50%;" />

### 方法4（使用App的组件去注册）：

这个是使用创建项目自带App.vue的hello world的界面，直接导入这个界面的模板

```js
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false

/* eslint-disable no-new */
// const cpn = {
//   template: `<div>{{message}}</div>`,
//   data() {
//     return {
//       message: '我是组件message'
//     }
//   }
// }

new Vue({
  el: '#app',
  // render: h => h(App)
  //render->vdom
  //所以这个性能更高

    render:function(createElement){
      return createElement(App)
    }
  }
)

```

运行得出的结果是

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191112110240.png" style="zoom:50%;" />

而runtime-only的流程只有：render->vdom，花更少的代码去运行同样的结果，所以runtime-only的性能会更高

runtime-only省去了template和ast这两部分，因为到render的时候，template组件什么的都已经渲染成为render函数了，使用代码测试一下

```js
import Vue from 'vue'
import App from './App'

Vue.config.productionTip = false

/* eslint-disable no-new */
// const cpn = {
//   template: `<div>{{message}}</div>`,
//   data() {
//     return {
//       message: '我是组件message'
//     }
//   }
// }
console.log(App)
new Vue({
  el: '#app',
  // render: h => h(App)
  //render->vdom
  //所以这个性能更高

    render:function(createElement){
      return createElement(App)
    }
  }
)

```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191112110300.png)

在控制台打印出来的已经没有template这个函数了，这部分解析的是由vue-template-compiler帮我们解析好了