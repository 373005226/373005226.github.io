---
title: Vuex
summary: 讲解Vuex的各种属性和方法
date: 2020-01-01
categories:
    - 前端
tags:
    - Vue
---

> 以下皆是由https://www.bilibili.com/video/av59594689?from=search&seid=2169751232721048853
>
> 视频中的老师操作代码和讲义总结而成

## Vuex是什么

`Vuex`在很多网站讲解都是状态管理工具，这个说白了其实就是如果有很多个组件需要传递变量，可以通过`Vuex`来存储这些变量，**也就是说Vuex就是存储全局变量的地方**

如果两个组件之间变量状态传递，那么可以通过`props`传递，如果是有很多个组件或者组件之间没有联系的话，通过`props`传递就会很麻烦

这个一般是创建一个文件夹，里面创建一个`js`文件去填写配置

如图所示：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200101224536.png)

然后在编写文件之后要去**main.js**挂载才能正常使用

顺带一提，在**main.js**挂载其实本质就是在**main.js**执行

```
Vue.prototype.$store = store
```

## Vue的单一状态树

Vue单一状态树其实就是个单一数据源，通俗的讲就是所有数据都只有一个数据源提交。

如果拿生活中的例子就是，按照以前的公积金政策，每个档案的信息都是分布的，个人档案分到个人档案管理中心，社保分在社保局。但是如果要换个城市入户的话，就要在哪个城市每个地方都提交一份档案，最后再去一个地方提交所有信息，如果有一个部门的信息错误就要回去重新提交一份，原本的方法显得十分的低效。如果换成代码的话，那么就是如果状态信息都保存到多个Store对象中，那么之后的维护和管理都是十分的困难的。

这也就是我们国家正在完善公积金系统，无论是有多少个城市的公积金，都可以在一个城市里面办理，放在state里面也就是说，无论是有多少个仓库信息，都只调用一个state。无论多少个方法属性都只在一个store存放

也就是说无论有多少个数据，都只建议创建一个store

## Vuex的结构

一般的话默认是有如下结构的

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200101231128.png" style="zoom:67%;" />



### state

这个就是存储全局变量的地方了

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200101231403.png" style="zoom:67%;" />

如果在这里定义变量的话，那么如果要调用它的话，那么就要使用到`$store.state.变量`

调用方法如图所示：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200101232112.png" style="zoom:67%;" />

### mutations

这个其实就相当于vue里面的methods方法

#### 使用$store.state.counter改变值（错误写法）

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200101232500.png" alt="官方给出的图" style="zoom:67%;" />

这个`devtools`是官方推出的插件，**只要使用如上图的流程**，使用这个插件可以跟踪到属性的更改

比如一个案例：要使用`vuex`的属性去在页面显示加减法，如果使用如下方法的话，那么达到目的是肯定可以的

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200101233545.png" style="zoom:67%;" />

`vuex`的变量也可以跟着去修改



<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200101233519.png" style="zoom:67%;" />



这个方法其实就是`state`和`Vue Components`之间互相改变，虽然是可以达到目的，但是这样就无法使用`devtools`工具去监听数据的改变了

所以这个时候要使用`mutations`去改变`vuex`属性的值

#### 使用mutations去改变值（正确写法）

先在Vuex里面定义方法

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200101234657.png" style="zoom:67%;" />

然后在组件的使用方法如图

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200101235100.png" style="zoom:67%;" />

在组件内使用要使用到`this.$store.commit`去调用vuex的方法

此时在**devtools**工具也能监听到属性的改变了

#### mutations传入一个参数

这里拿个案例来说明，这里需要在组件里面传入值的话，那么就可以在mutations里面添加一个形参

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102231831.png" style="zoom:67%;" />

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102232128.png" style="zoom:67%;" />



在组件内使用mutations方法的话跟emit方法一样的

#### mutations传入多个参数

传入对象的话方法还是一样的

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102232705.png" style="zoom:67%;" />

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102232735.png" style="zoom:67%;" />

在组件的methods传入的值有个专属的名词叫**payload：负载**

#### mutations的另外一种提交风格

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102233351.png" style="zoom:67%;" />

但是这种方法传递到vuex的值就不一样了，就不仅仅是一个数字了，而是一个对象，因为这个时候传入过去的就是一个payload对象了

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102233533.png" style="zoom:67%;" />



#### mutations响应规则

Vuex里面所有的state方法都是响应式的，只要state的数据发生了改变，那么Vue组件就会自动更新

但是做到响应式有两个前提：

- 在store里面定义好所需要的属性
- 在state中对象添加属性的时候，使用如下的方式
  - 方法1：使用Vue.set(obj,'newProp',123)
  - 方法2：用新的对象给旧对象重新赋值

这里拿个案例来说明

先定义一个新属性

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102234336.png" style="zoom:67%;" />

在mutations中添加方法

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102234527.png" style="zoom:67%;" />





最后在组件内添加方法

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102234741.png" style="zoom:50%;" />

这样子的方法是属于响应式的，这个是每个对象都有一个Dep，一个Dep定义着好多个watcher，Dep来监听属性的变化

Dep就是个观察者模式，先观看数据有没有变化，如果属性发生了变化，就会通知使用到该属性的地方发生改变



如果里面修改的不是在state里面定义好的属性

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102235443.png" style="zoom:50%;" />

position属性在state里面没有被定义，所以即便是添加了这个属性能在**devtools**工具监听到，也不能提交到响应式系统里面

所以如果是要添加属性的话，最好是使用`Vue.set`方法

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102235826.png" style="zoom:67%;" />

相对应的，如果是要删除方法就使用`Vue.delete`方法

### getters

这个就相当于vue中的计算属性

#### 只需要一个形参state

这里做个案例，比如说是要做一个`counter`的平方

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102214303.png" style="zoom:67%;" />

然后在组件中要引用的话，那么就直接使用

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104124900.png)



`getters`还可以做一些过滤功能，比如要过滤年龄大于20岁的数据

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102214939.png" style="zoom:67%;" />



<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102215037.png" style="zoom:67%;" />



#### 有两个形参：state和getters

`getters`方法是有第二个形参的，第二个形参实际指向的是自己的`getters`方法

比如要调用上述`more20stu`方法的个数

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102225448.png" style="zoom:67%;" />



#### 需要在方法中传入值而非定义好的

如果是需要在组件中传入值，而非在getters里面写死的，那么这个需要在返回值里面定义一个函数，也就是闭包

如果是需要改变通过组件里面传入数据来筛选大于年龄的范围的话，可以使用如下写法

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102230326.png" style="zoom:67%;" />



这个其实是可以简化为箭头函数的写法的

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200102230406.png" style="zoom:67%;" />



### actions

`actions`就是异步方法，简单的说就是如果`mutations`里面有要异步操作的方法，比如网路请求等是异步操作的，官网给出的图是`actions`异步操作在`mutations`之前

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200101232500.png" style="zoom:67%;" />

如果在`mutations`直接去执行异步操作的话，因为不确定执行完的时间，所以如果是异步操作需要时间久的话就不会被**devtools**监听到，所以就有了`actions`方法，`mutations`会等待`actions`的操作执行完毕之后再继续往下走。

其用法如下：

#### 普通写法

`actions`里面默认的第一个形参就是`context`

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104123038.png" style="zoom:67%;" />

在`actions`里面调用`mutations`里面的方法

然后在组建中使用`dispatch`方法调用`actions`的方法就好了

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104123148.png" style="zoom:67%;" />



#### 传递参数

当然`actions`也是可以传递参数的，默认的第二个参数名是`payload`

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104123608.png" style="zoom:67%;" />



<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104123639.png" style="zoom:67%;" />



#### 获取异步操作成功后的信息

如果是要获取异步操作成功的信息的话

使用如下写法也是可以的：

##### 写法1：

Vuex里面的方法：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104125413.png" style="zoom:67%;" />



组件中的方法：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104125505.png" style="zoom:67%;" />

但是上面的方法有点不好



##### 写法2：

vuex的写法：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104125909.png" style="zoom:67%;" />

组件中的写法：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104125952.png" style="zoom:67%;" />



##### 方法3（使用promise）：

但是方法2的写法不够优雅

Vuex的写法：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104130901.png" style="zoom:67%;" />



组件内的写法：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104130714.png" style="zoom:67%;" />

### modules

modules方法就是如果所有的东西都往store里面塞，那么数量多的话就会显得很臃肿，但是因为单一状态树的原因只能创建一个store，所以vuex可以在store里面继续划分模块

#### 使用modelA里面的state

使用方法如下：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104132122.png" style="zoom:67%;" />

如果是`modelA`里面有个state属性的话，那么在组件中调用的方法如下：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104132311.png" style="zoom:67%;" />

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104132437.png" style="zoom:67%;" />

因为`modelA`最终会放在`store`的`state`里面

#### 使用modelA里面的mutations

如果是在modelA里面定义一个方法的话

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104133005.png" style="zoom:67%;" />



组件中的方法：

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104133247.png" style="zoom:67%;" />

这个还是用以前的方法就好了，因为只要名字不是重复的，`commit`会去从`store`里面找，如果找不到再去看看`models`里面有没有这个方法

#### modelA里面的getter

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104133556.png" style="zoom:67%;" />

组件内的写法：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104133815.png)

但是如`stote`的`getters`的话，是有一个`getters`形参的，这个形参是可以调用自己的`getters`方法的

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104134106.png" style="zoom:67%;" />

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104134142.png)



如果是想引用`store`的`state`属性的话，那么可以引用第三个参数`rootState`

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104134539.png" style="zoom: 67%;" />

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104134617.png" style="zoom:67%;" />

#### modelA里面的actions

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104135150.png" style="zoom:67%;" />



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104135350.png)

#### 总结

`modelA`里面的所有方法的形参都是指向自己`modelA`里面的方法的

## Vuex的文件结构

`Vuex`的文件结构是一般都会把`state`的参数用对象传进去

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104140640.png" style="zoom:67%;" />

然后`mutations`、`actions`、`getters`里面的方法，把方法写在新的一个文件导入即可

比如`mutations`

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200104140810.png" style="zoom:67%;" />

如果是`model`里面的方法，就新建一个`model`文件夹，里面存放`store`的`model`方法即可 