---
title: v-router详解
summary: 讲解v-router的各种属性
date: 2019-12-27
categories:
    - 前端
tags:
    - Vue
---

> 以下皆是由https://www.bilibili.com/video/av59594689?from=search&seid=2169751232721048853
>
> 视频中的老师操作代码和讲义总结而成

## vue-router的安装

[vue-router](https://router.vuejs.org/zh/)是官方推荐的路由插件，官方的路由管理器。它和 Vue.js 的核心深度集成，让构建单页面应用变得易如反掌

使用命令

```js
npm install vue-router --save
```

## vue-router的使用

### 配置文件解析

一开始使用脚手架方法安装的代码是这样的

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228130938.png" style="zoom:67%;" />



但是实际上可以分为好几个步骤

### <img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228131942.png" style="zoom:67%;" />



<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228142155.png" style="zoom:67%;" />



### vue-router基本使用

先添加几个子组件

先要导入子组件

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228143705.png)

然后在路由里面导入

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228151332.png" style="zoom:67%;" />

填写这两个配置`（path、components）`

最后在`App.vue`里面配置`router-link`，并且填写相应的配置即可

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228151414.png" style="zoom:67%;" />

最后是需要`router-view`才可以展示配置的路由

`router-link`是导入`vue-router`后注册的全局属性

### redirect重定向

这个比如打开网页的默认显示界面的路径

直接配置一个`redirect`就好了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200101221619.png)

### vue-router的mode

在导航栏出现类似这种的情况，地址栏带着个#

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228153917.png)

这个是因为默认是哈希模式的

如果要解决这种情况可以选择 

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228154435.png" style="zoom:67%;" />

然后导航栏的#就消失了

这个的原因就是项目的配置默认的是哈希模式，但是路由用的是history模式，这个时候就会出现导航栏出现#的问题

## router-link其他属性

### 渲染标签

如上述代码所示，默认`router-link`渲染出来就是个a标签，如果要改成其他标签类似button之类的，就可以做修改

方法很简单，只需要加一个tag属性即可

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228155022.png)



这样浏览器渲染出来就是一个button标签了

### 跳转方式

默认跳转方式是类似于小程序的navigate，也就是跳转后可以返回

如果要修改成replace的话，也就是跳转后不能返回，只需要加入一个replace字段

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228155419.png)

此时就没有返回功能了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228155436.png)



### router-link的活跃状态

#### 局部修改

router-link默认点击哪个路由，哪个路由就会变成活跃状态

如图所示：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228161537.png)

活跃的路由默认就会带一个样式

这个一般用于点击后显示样式等功能

当然这个名字是可以修改的，修改方式如下图所示：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228160639.png)



再添加一个字体变红的样式，效果就会很明显了

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228160748.png" style="zoom:67%;" />

效果如下图：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228160835.png)



但是这样是有个弊端的，因为每个路由都要加一个`active-class="active"`字段，如果要修改的路由很多的话就会显得十分的麻烦

这里就可以在全局里面修改

#### 全局配置

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228161141.png" style="zoom:67%;" />

先把原来配置的`active-class="active"`删掉，只需要添加一个`linkActiveClass`字段，就可以把默认的全局活跃样式修改了

## 其他标签实现跳转功能

### push方法

之前用的都是使用`router-link`的方式去实现跳转功能，如果是要实现用其他标签来跳转的话，就要使用事件的方法去跳转

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228163148.png" style="zoom:67%;" />



`$router`这个属性是`vue-router`在所有的组件都添加了的

`$router`的`push`方法就是`pushSate`，也就是这个是可以返回的，如果要不想返回，可以使用`replace`属性 

### replace方法

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228163749.png" style="zoom:67%;" />

## 动态路由

当然在实际项目开发中不可能通过写死的路由去跳转，一般都是用于带着参数去跳转的方式开发

### 配置路由

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228165734.png" style="zoom:67%;" />



### 在组件上配置参数

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228184048.png" style="zoom:67%;" />

当然肯定要在跳转过去的页面中要获取传过去的ID

### 在新页面获取传递过去的值

#### 方法1：使用计算属性获取

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228184854.png" style="zoom:67%;" />

**注意这个`params`后面的那个属性是跟全局路由注册过的一样的名字，也就是`path:'/user/:userid'`**

#### 方法2：直接使用mustach方法获取

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228185554.png" style="zoom:67%;" />

#### $route和$router的区别

`$route`是获取上个页面的活跃路由

而`$router`是在路由注册过的全局`router`对象

在这里打印下这两者

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228185646.png" style="zoom:67%;" />

打印出来`$route`的结果如下：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228190329.png)

`$router`的结果如下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228190417.png)



由此可以证实上面的结果

## 路由的懒加载

当打包出来的程序，如果打包出来的包十分的大，点开项目界面的时候会出现几秒的空白来加载文件，那么对用户的体验感十分的不好

所以官方推荐以路由懒加载的形式来编写

这里先把上面的代码打包，打包后的结果如图所示

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228191135.png)

第一个配置文件app.....的是业务代码**（即开发者自己编写的代码）**

第二个文件就是对打包的代码做底层支撑的**（即ES6语法的import、export、commentjs等，使之变成对浏览器能够识别的东西）**

第三个文件是第三方代码**（即开发中引用的第三方的东西Vue、Vue-router、axios、Vuex等）**



在项目开发中，也就是要在点击哪个页面的时候，哪个页面就进行加载，这也就是路由的懒加载的思想

使用路由懒加载只需要重新定义上面导入的方法：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228192459.png) 

然后重新打包文件

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228192646.png)



如图所示，就多了三个打包的文件，现在引用哪个文件就会使用哪个路由的打包文件了

## 嵌套子路由

一般话比如个人中心的地址、个人信息等，在地址栏一般是由User/address、User/person等形式表现出来

这个时候就要用到子路由了

在全局路由里面嵌套子路由的方式如下：

先在全局路由里面在要添加子路由的字段加上children

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228201229.png" style="zoom:67%;" />

然后在要添加路由的那个文件里面添加配置

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228201903.png" style="zoom:67%;" />

然后就可以添加子路由了

## 参数传递

### 使用router-link方式跳转

#### 配置router-link

先添加一个子组件Profile

如果要参数传递的话，只能用对象的方式去传递

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228210106.png)

然后在地址栏就会出现参数传递了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228210147.png)

#### 接收参数

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228210805.png" style="zoom:67%;" />

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228210831.png" style="zoom:67%;" />



### 其他标签去跳转

如果不是通过router-link去跳转的话，比如使用的是button，那么方法还是使用事件去传递

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228211246.png" style="zoom:67%;" />

## $router和$route的区别

### $router

这里先在局部打印`$router`和在**main.js**全局打印`$router`

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228212224.png" style="zoom:67%;" />



<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228212553.png" style="zoom:67%;" />

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228212404.png" style="zoom:67%;" />

发现这两者打印的结果是一样的，所以局部打印的`this.$router`是new Vue传的那个`router`是同一个东西 

这里进入源码看看

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228212850.png" style="zoom:67%;" />

因为这里实例化了一个类`VueRouter`，所以后面才可以import导入

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228212957.png)

这里的所有方法就是`$router`可以使用的方法

### $route和$router为什么能用

#### Vue.prototype

这个route和router为什么能用呢，因为vue-router源码里面注册了全局组件

每次`Vue.use`的时候用的其实都是内部的`install`方法，所以要通过install方法去找最终的源码

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228213741.png)



可以使用`this.$route`和`this.$router`是因为所有的组件都是继承于Vue类的原型

这里来证实一下，先在**main.js**定义一个Vue的属性

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228214155.png" style="zoom:67%;" />

然后在子组件中调用这个方法

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228214402.png" style="zoom:67%;" />

如图所示：是可以使用这个方法的

如果定义的是一个属性的话也是一样的

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228221718.png" style="zoom:67%;" />



<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228221805.png" style="zoom:67%;" />



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228222047.png)

两者都可以打印出来

#### 源码解析

再接着看源码

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228215255.png" style="zoom:67%;" />

这个`object.defineProperty`

这个其实是这样的，假如要往一个对象里面添加一个属性

按照以前的方法就是直接添加就好了

```js
const obj={
	name:'LY'
}
```

如果是要`object.defineProperty`的方法就是

```js
const obj={
	name:'LY'
}
//defineProperty的写法
object.defineProperty(obj,'age',8)
```

`defineProperty`这个也就是Vue的响应式实现方法的核心

所以上面图片的那个代码也就是给`Vue.prototype`加一个`$route`,其值为`return this._routerRoot._router`

而`return this._routerRoot._router`里面最后的那个`_router`就是**main.js**里面挂在进去的那个`router`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228220023.png)

而`this._routerRoot`呢就是上面的Vue实例传过去的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228220221.png)

if下面第一行的`this`，就是Vue实例，把Vue实例传递给`this._routerRoot`。

然后又把`this.$options.router`赋值给`this.__router`，这个就相当于给`this._routerRoot`添加了一个`this._router`属性，即`this._routerRoot._router`

也就是说`this._routerRoot._router`就等于`this.$options`中的`options`，而这个`options`就相当于**main.js**里面传入的`router`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228220023.png)

然后再使用Vue注册一个全局组件

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228221105.png)

所以才可以使用`this.$route`和`this.$router`

把上面代码块的object.defineProperty代码翻译过来也就是

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228221338.png)

只不过最后的那个`_route`和是动态的，哪个组件于活跃就把`_route`赋值给谁，而这个`_route`永远指向当前活跃的对象

然后又赋值给Vue的实例，所以才可以在全局使用

#### 结论

$router是Vue的实例，如果想要导航到不同的URL话，就要使用$router.push等方法，所有方法都在源码定义好了

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228222414.png" style="zoom:67%;" />

而$route是当前活跃的路由，可以获取当前的`query、params、path`等属性

## 导航守卫

### 生命周期

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228225153.png" style="zoom: 50%;" />

官网的这个图的重点就是那几个生命周期

create就是在组件被创建的时候的回调函数

mouted就是在组件挂在到dom上面的时候的回调函数，因为vue是先创建组件，然后再把template挂在到dom上面

updated是界面发生更新的时候的回调函数

### 给组件添加标题

现在创建的每个组件显示的都是模板文件的title

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228224635.png)

如果要修改这个标题的话，那么可以使用create去修改

添加如下字段就可以修改title了

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229010608.png" style="zoom: 67%;" />

然后点击About，标题就可以显示成”关于“了

但是这个方法有个弊端，就是需要在每个子组件都要添加这个代码，这样是十分的繁琐的，所以官方有个方法：**全局前置守卫**

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229010921.png)

其部分源码如图：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229011142.png)

其实这个就是给`NavigationGuard`起了个别名，也就是括号里面的内容，也就是说传递的参数本身又有三个参数，`to`、`from`和`next` 

在代码中实现也就是

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229011615.png" style="zoom:67%;" />

**这个next是必须要去调用的，如果不实现beforeEach方法，那么就是默认调用，如果实现了beforeEach方法，那么如果不调用next就不能进行路由跳转**

#### 错误写法

如果要进行跳转后能改变标题的话

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229011907.png" style="zoom:67%;" />



此时的这个`to`是没有`title`这个属性的

`to`的这个源码的类型是`route`类型

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229012017.png)

Route也就是全局路由的配置了

只要在路由里面添加`meta`属性就可以调用title属性了

如下图所示：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229012250.png" style="zoom:67%;" />

只要在每个需要修改主题的路由里面添加`meta`对象的`title`属性，就可以实现了

最后再在`beforeEach`修改字段

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229012504.png" style="zoom:67%;" />

但是如上代码有个隐患，就是在子路由的时候的`title`是`undefined`

打印一下`to`属性先看看

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229012800.png" style="zoom:67%;" />

别的没有子路由的组件都可以正常显示标题，但是有子路由的组件显示是`undefined`，子路由根本就没有`meta`属性，但是每个路由都有一个`matched`，而里面的属性是大家都共有的，所以这个要使用`matched`来获取`title`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229013251.png)

#### 正确写法 

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229013421.png" style="zoom:67%;" />

## keep-alive

### 保持上个点击组件的状态

这个理论上说起来很麻烦，但是实际上就是监听跳转过程，也就是如果我上一次点击的是Home的message界面

，我点击其他界面后再点击回Home，里面显示的还是message，也就是上一次打开的界面，而不是显示默认跳转的news界面

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191228223535.png" style="zoom:67%;" />

也就是避免组件的重新渲染，保留组件的状态

先在home和about加入create回调方法



<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229121226.png" style="zoom:67%;" />

create方法只有在组件被创建的时候调用，如果是按照之前的方法的话，那么每次点开组件都会有一个create方法被调用

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229121420.png)

如果加上keep-alive后

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229121541.png)

那么create方法就只会被调用一次了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229121555.png)

这个其实要用到**组件内守卫**

只需要先把原来全局路由的`Home`下面的`redirect`去掉，然后添加`activated`**（组件活跃时候的回调函数）**

然后再调用**组件内守卫**

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191229122901.png" style="zoom:67%;" />

这样子就可以使用记录上个点击状态的功能了

### 补充点

`activated`和`deactivated`只有在使用了`keep-alive`的时候才会被使用

### keep-alive补充点

keep-alive有两个重要的属性

- include：字符串或正则表达式，只有匹配的组件才会被缓存
- exclude：字符串或正则表达式，任何匹配到的组件都不会被缓存

这个因为如果是个人中心等会变化的页面就需要被时刻修改