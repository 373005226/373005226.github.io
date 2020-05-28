---
title: 前端面试题总结
date: 2020-05-28 12:00:00
summary: 总结我看过的JavaScript面试题和答案
categories:
    - 转载
tags:
    - 前端面试题
---

## 多益2017面试题总结

> 本文引用于
>
> https://blog.csdn.net/chengzihy/article/details/78206546
>
> https://www.cnblogs.com/fmixue/p/12022296.html

### svg画四边形

svg画四边形，四个点的坐标分别是（220，100）（300，210）（170，250）（123，234）

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200528112210.png"/>

```html
<svg height="300">
    <polygon points="220,100 300,210 170,250 123,234" style="fill:blue;stroke:black;stroke-width:1;">
</svg>
```

> 关于SVG的问题可以参考菜鸟教程：
>
> https://www.runoob.com/svg/svg-polygon.html

> `<polygon>` 标签用来创建含有不少于三个边的图形。
>
> 如`points="220,10 300,210 170,250 123,234"`，里面就是填写坐标
>
> 样式里面的`fill`填的是颜色，如blue
>
> `stroke`是边框颜色
>
> `stroke-width`是边框宽度
>
> `fill-rule`就是交叉的地方是否填充颜色，默认为`nonzero`填充颜色，`evenodd`为不填充颜色

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200528120028.png)

### 写出promise构造函数

```javascript
var promise=new Promise(
    function (resolve,reject) {
    if("异步处理成功"){
        resolve ();
    }else{
        reject();
    }

});
promise.then(
    function A () {
        console.log("事件处理成功时的操作")
    },
    function B() {
        console.log("事件处理失败时的操作")
    }
)
```

> 本题详解https://www.jb51.net/article/111741.htm

> promise其实就是一个对象，跟JavaScript的其他对象一样的；其次promise起到一个代理的作用，`充当异步操作与回调函数之间的中介`，它使得异步操作具备同步操作的接口，使得程序具备正常的同步运行的流程，回调函数不必再一层层嵌套。

### promise.all的使用

```javascript
var p = Promise.all(promises).then(function (posts) {
 // ...
}).catch(function(reason){
 // ...
});

// 我自己的写法
let p = Promise.all(promises).then(res=> {
 // ...
}).catch(error=>{
 // ...
});
```

**Promise.all方法的参数可以不是数组，但必须具有Iterator接口，且返回的每个成员都是Promise实例**

### 如何让动态插入的div响应绑定事件

```javascript
<div id="testdiv">
  <ul></ul>
</div>
//jquery 1.7版以后使用on动态绑定事件
$("#testdiv ul").on("click","div", function() {
     //do something here
 });
```



## 面试必问

### **Vue2的数据响应式原理**

1、什么是`defineProperty`?

**`defineProperty`是设置对象属性，利用属性里的`set`和`get`实现了响应式双向绑定；**

语法：`Object.defineProperty`(要设置的对象,要修改的对象的属性,属性描述)

属性描述：
1、configurable - 表示此属性能否被delete，默认false;
2、enumerable - 表示此属性能否被枚举，默认为false;
3、value - 设置此属性对应的值，默认为undefined;
4、writable - 设置value属性能否被修改值，为true时方可被改变，默认为false；
5、get - 给属性提供getter方法，默认为undefined，访问该属性时，该方法会被执行，默认参数为this对象；
6、set - 给属性提供setter方法，默认为undefined，属性值修改时，会执行该方法，唯一参数为新的值；

获取对象所拥有属性的方法：`Object.getOwnPropertyDescriptor`(对象，对象某一属性)

```javascript
var obj = {
    a: 1,
    b: 2
}
var value = obj.b;
Object.defineProperty(obj,'b',{
    configurable: false,//不能被deleted
    enumerable: false,//不能被删除
    writable： true,//可以被修改值
    value: '22',//设置b为22
    set: function(newValue){
       console.log('新的value为'+ newValue);
       value = newValue;
    },
    get: function(){
      console.log('你设置了value');
      return value;
    },
})

console.log(Object.getOwnPropertyDescriptor(obj,'a')); //{value: 1, writable: true, enumerable: true, configurable: true}
```

2、实现双向绑定：

```javascript
function vue(){
  this.$data = {a: 1};//设置data
  this.el = document.getElementById('app'); //设置根节点
  this.dom = '';//虚拟dom
}

//往原型上绑定监听方法
vue.prototype.observe = function(obj){
    var self = this;
    var value;
    for(var key in obj){
        value = obj[key];
        if(typeof value === 'object'){ //如果是对象  需要递归循环执行此方法
           this.observe(value);
        }else{
          Object.defineProperty(this.$data,key,{
             get: function(){
              //依赖收集 这里略过 代替源码里的 depend
               return value;
             },
             set: function(newVal){
                value = newVal;
                self.render();//触发更新  代替源码里的dep.notify
             }
          })
        }
    }
}

vue.prototype.render = function(){
   this.dom = '我是' + this.$data.a;
   this.el.innerHTML = this.dom;
}
```

3、数组监听实现
vue数组的特性： `push shift unshift`
对象的监听是通过`defineProperty`，而数组是通过`dependArray`

```javascript
Object.create(proto,propertiesObject) //方法创建一个新对象，使用现有的对象来提供新创建的对象的__proto__

var arrPro = Array.prototype;
var arrObj = Object.create(arrPro);
var arr = ['push','pop','shift'];
//装饰者模式
arr.forEach(function(method,index){
   arrObj[method]=function(){
      var ret = arrPro[method].apply(this,arguments);
      dep.notify();
      return ret;
   }
})
```



### **Vue3的数据相应原理**

**vue3通过proxy去实现响应式原理**

 let p = new Proxy(target, handler);
 target: 目标对象
 handler: 是一个对象，其属性是当执行一个操作时定义代理的行为的函数。
 handler中常用的对象方法如下：
 receiver => 此Proxy对象本身，也就是this指向；

1. get(target, propKey, receiver)
2. set(target, propKey, value, receiver)
3. has(target, propKey)
4. construct(target, args)
5. apply(target, object, args)



写法如下：

```javascript
let a = new Proxy({},{
    get: function(target,key,receiver){
         return Reflect.get(target[key]);
       //return target[key];
    },
    set: function(target, key, value, receiver){
         return Reflect.set(target,key,value);
       //return target[key] = value;
    }
})
```

 Object.defineProperty 存在如下缺点：
1. 监听数组的方法不能触发Object.defineProperty方法中的set操作(如果要监听的到话，需要重新编写数组的方法)。
2. 必须遍历每个对象的每个属性，如果对象嵌套很深的话，需要使用递归调用。

Proxy相比defineProperty 区别：
1、参数不一样，defineProperty第二个参数需要指定操作对象的具体key值，而Proxy指定的是最外层对象，这样就省去了循环；
2、defineProperty操作的是对象本身，改变了对象本身，而Proxy是改变对象代理，返回新对象；
3、defineProperty必须在最外层定义一个全局变量value，通过get将其return出去，而Proxy将返回值放到了参数中，直接return target[key];

### **Diff算法和virtual dom**

***注：virtual dom是一个虚拟层，并不正式存在***

diff算法是直接去比对元素，元素里包含props 和 children, 一直进行到根节点，template中所有的节点都会有个diff，挂载了所绑定的一系列属性，通过遍历循环监听到变化，在vue3中作者改变了这种方式，在发布会中讲到对比vue2速度提升了6倍，

vue2的虚拟dom，会遍历所有子节点，全部用diff算法比对一次，遍历速度由dom数量决定；

vue3中会找出变化的项，只更新改变项；



## 遇到的面试题

### Json字符串和JavaScript对象区别

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200528172924)

