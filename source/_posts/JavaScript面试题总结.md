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

#### 如何阻止冒泡事件和默认事件

（1） 阻止冒泡事件

```javascript
function stopBubble(e) {  
  if(e && e.stopPropagation){  
      e.stopPropagation();  
  } else {  
    window.event.cancelBubble = true;  
  }  
}; 
```

（2） 阻止默认事件

```javascript
function stopDefault(e){  
  if(e && e.preventDefault) {  
    e.preventDefault();  
  } else {  
    window.event.returnValue = false;  
  }  
  return false;  
}; 
```

> 此题详解 http://caibaojian.com/javascript-stoppropagation-preventdefault.html

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

### 你能描述一下渐进增强和优雅降级之间的不同吗?

**优雅降级**：Web站点在所有新式浏览器中都能正常工作，如果用户使用的是老式浏览器，则代码会检查以确认它们是否能正常工作。由于IE独特的盒模型布局问题，针对不同版本的IE的hack实践过优雅降级了,为那些无法支持功能的浏览器增加候选方案，使之在旧式浏览器上以某种形式降级体验却不至于完全失效.
**渐进增强**：从被所有浏览器支持的基本功能开始，逐步地添加那些只有新式浏览器才支持的功能,向页面增加无害于基础浏览器的额外样式和功能的。当浏览器支持时，它们会自动地呈现出来并发挥作用。

### 线程与进程的区别

一个程序至少有一个进程，一个进程至少有一个线程。线程的划分尺度小于进程，使得多线程程序的并发性高。

另外，进程在执行过程中拥有独立的内存单元，而多个线程共享内存，从而极大地提高了程序的运行效率。

线程在执行过程中与进程还是有区别的。每个独立的线程有一个程序运行的入口、顺序执行序列和程序的出口。但是线程不能够独立执行，必须依存在应用程序中，由应用程序提供多个线程执行控制。

从逻辑角度来看，多线程的意义在于一个应用程序中，有多个执行部分可以同时执行。但操作系统并没有将多个线程看做多个独立的应用，来实现进程的调度和管理以及资源分配。这就是进程和线程的重要区别。

### 为什么利用多个域名来提供网站资源会更有效？

- 1.CDN缓存更方便
- 2.突破浏览器并发限制（一般每个域名建立的链接不超过6个）
- 3.Cookieless，节省带宽，尤其是上行带宽一般比下行要慢
- 4.对于UGC的内容和主站隔离，防止不必要的安全问题(上传js窃取主站cookie之类的)。正是这个原因要求用户内容的域名必须不是自己主站的子域名，而是一个完全独立的第三方域名。
- 5.数据做了划分，甚至切到了不同的物理集群，通过子域名来分流比较省事。这个可能被用的不多。

PS:关于Cookie的问题，带宽是次要的，安全隔离才是主要的。关于多域名，也不是越多越好，虽然服务器端可以做泛解释，浏览器做dns解释也是耗时间的，而且太多域名，如果要走https的话，还有要多买证书和部署的问题。

### 请说出三种减少页面加载时间的方法。（加载时间指感知的时间或者实际加载时间）

1. CDN加速
2. 减少不必要的打包文件，如使用tree shaking
3. 优化css
4. 提高带宽
5. 精灵切图

### 什么是FOUC（无样式内容闪烁）？你如何来避免FOUC？

FOUC(Flash Of Unstyled Content)--文档样式闪烁

<style type="text/css"media="all">@import"../fouc.css";</style>而引用CSS文件的@import就是造成这个问题的罪魁祸首。IE会先加载整个HTML文档的DOM，然后再去导入外部的CSS文件，因此，在页面DOM加载完成到CSS导入完成中间会有一段时间页面上的内容是没有样式的，这段时间的长短跟网速，电脑速度都有关系。解决方法简单的出奇，只要在<head>之间加入一个<link>或者<script>元素就可以了。
### data-属性的作用是什么？

data-* 属性用于存储页面或应用程序的私有自定义数据。data-* 属性赋予我们在所有 HTML 元素上嵌入自定义 data 属性的能力。存储的（自定义）数据能够被页面的 JavaScript 中利用，以创建更好的用户体验（不进行 Ajax 调用或服务器端数据库查询）。

data-* 属性包括两部分：

- 属性名不应该包含任何大写字母，并且在前缀 "data-" 之后必须有至少一个字符
- 属性值可以是任意字符串

### 请描述一下cookies，sessionStorage和localStorage的区别？

sessionStorage和localStorage是HTML5 Web Storage API提供的，可以方便的在web请求之间保存数据。有了本地数据，就可以避免数据在浏览器和服务器间不必要地来回传递。sessionStorage、localStorage、cookie都是在浏览器端存储的数据，其中sessionStorage的概念很特别，引入了一个“浏览器窗口”的概念。sessionStorage是在同源的同窗口（或tab）中，始终存在的数据。也就是说只要这个浏览器窗口没有关闭，即使刷新页面或进入同源另一页面，数据仍然存在。关闭窗口后，sessionStorage即被销毁。同时“独立”打开的不同窗口，即使是同一页面，sessionStorage对象也是不同的cookies会发送到服务器端。其余两个不会。Microsoft指出InternetExplorer8增加cookie限制为每个域名50个，但IE7似乎也允许每个域名50个cookie。

- Firefox每个域名cookie限制为50个。
- Opera每个域名cookie限制为30个。
- Firefox和Safari允许cookie多达4097个字节，包括名（name）、值（value）和等号。
- Opera允许cookie多达4096个字节，包括：名（name）、值（value）和等号。
- Internet Explorer允许cookie多达4095个字节，包括：名（name）、值（value）和等号。

## 凡科面试

> 本文引用于https://www.cnblogs.com/ypppt/p/12868392.html

### css样式覆盖问题

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200528213206)

答案都是blue

因为无论是red blue 还是blue red，都是一样的，只看css样式的先后顺序，越后面优先级越高

### 5种以上垂直居中的css样式

> 垂直方法远远不止5中，可以参考这篇博客https://www.cnblogs.com/hutuzhu/p/4450850.html

#### 第一种，table样式

这个方法把一些 div 的显示方式设置为表格，因此我们可以使用表格的 vertical-align property 属性。

```html
<div id="wrapper">
	<div id="cell">
		<div class="content">Content goes here</div>
	</div>
</div>
#wrapper {
	display: table;
}

#cell {
	display: table-cell;
	vertical-align: middle;
}
```

**优点：**

- content 可以动态改变高度(不需在 CSS 中定义)。当 wrapper 里没有足够空间时， content 不会被截断

**缺点：**

- Internet Explorer(甚至 IE8 beta)中无效，许多嵌套标签(其实没那么糟糕，另一个专题)



#### 方法二：

这个方法使用绝对定位的 div，把它的 top 设置为 50％，top margin 设置为负的 content 高度。这意味着对象必须在 CSS 中指定固定的高度。

因为有固定高度，或许你想给 content 指定 overflow:auto，这样如果 content 太多的话，就会出现滚动条，以免content 溢出。

```html
<div class="content"> Content goes here</div>
#content {
	position: absolute;
	top: 50%;
	height: 240px;
	margin-top: -120px; /* negative half of the height */
}
```

**优点：**

- 适用于所有浏览器
- 不需要嵌套标签

**缺点：**

- 没有足够空间时，content 会消失(类似div 在 body 内，当用户缩小浏览器窗口，滚动条不出现的情况)



#### 方法三

这种方法，在 content 元素外插入一个 div。设置此 div height:50%; margin-bottom:-contentheight;。
content 清除浮动，并显示在中间。

```html
<div id="floater">
	<div id="content">Content here</div>
</div>

#floater {
	float: left;
	height: 50%;
	margin-bottom: -120px;
}

#content {
	clear: both;
	height: 240px;
	position: relative;
}
```

**优点：**

- 适用于所有浏览器
- 没有足够空间时(例如：窗口缩小) content 不会被截断，滚动条出现

**缺点：**

- 唯一我能想到的就是需要额外的空元素了(也没那么糟，又是另外一个话题)



#### 方法四

这个方法使用了一个 position:absolute，有固定宽度和高度的 div。这个 div 被设置为 top:0; bottom:0;。但是因为它有固定高度，其实并不能和上下都间距为 0，因此 margin:auto; 会使它居中。使用 margin:auto;使块级元素垂直居中是很简单的。

```html
<div id="content"> Content here</div>
#content {
	position: absolute;
	top: 0;
	bottom: 0;
	left: 0;
	right: 0;
	margin: auto;
	height: 240px;
	width: 70%;
}
```

**优点：**

- 简单

**缺点：**

- IE(IE8 beta)中无效
- 无足够空间时，content 被截断，但是不会有滚动条出现



#### 方法五

这个方法只能将单行文本置中。只需要简单地把 line-height 设置为那个对象的 height 值就可以使文本居中了。

```html
<div id="content"> Content here</div>
#content {
	height: 100px;
	line-height: 100px;
}
```

**优点：**

- 适用于所有浏览器
- 无足够空间时不会被截断

**缺点：**

- 只对文本有效(块级元素无效)
- 多行时，断词比较糟糕

这个方法在小元素上非常有用，例如使按钮文本或者单行文本居中。

### JS判断数据类型的方法和区别

JavaScript总共有7种数据类型

string、Boolean、Array、Object、Number、Null、undefined

#### typeof

```javascript
console.log(typeof "");
console.log(typeof 1);
console.log(typeof true);
console.log(typeof null);
console.log(typeof undefined);
console.log(typeof []);
console.log(typeof function(){});
console.log(typeof {});
```

看看控制台输出什么

![img](https://images2017.cnblogs.com/blog/1248022/201710/1248022-20171003140910630-149117849.png)

可以看到，typeof对于基本数据类型判断是没有问题的，但是遇到引用数据类型（如：Array）是不起作用的。

#### instanceof

```javascript
console.log("1" instanceof String);
console.log(1 instanceof Number);
console.log(true instanceof Boolean);
//            console.log(null instanceof Null);
//            console.log(undefined instanceof Undefined);
console.log([] instanceof Array);
console.log(function(){} instanceof Function);
console.log({} instanceof Object);
```

暂且不考虑null和undefined（这两个比较特殊），看看控制台输出什么

![img](https://images2017.cnblogs.com/blog/1248022/201710/1248022-20171003141627521-927563737.png)

可以看到前三个都是以对象字面量创建的基本数据类型，但是却不是所属类的实例，这个就有点怪了。后面三个是引用数据类型，可以得到正确的结果。如果我们通过new关键字去创建基本数据类型，你会发现，这时就会输出true,如下:

![img](https://images2017.cnblogs.com/blog/1248022/201710/1248022-20171003142217802-1412540288.png)

接下再来说说为什么null和undefined为什么比较特殊，实际上按理来说，null的所属类就是Null，undefined就是Undefined，但事实并非如此：控制台输出如下结果：

![img](https://images2017.cnblogs.com/blog/1248022/201710/1248022-20171003142534599-1150711103.png)

l浏览器压根不认识这两货，直接报错。在第一个例子你可能已经发现了，typeof null的结果是object，typeof undefined的结果是undefined

![img](https://images2017.cnblogs.com/blog/1248022/201710/1248022-20171003142840724-2044428988.png)

尤其是null，其实这是js设计的一个败笔，早期准备更改null的类型为null，由于当时已经有大量网站使用了null，如果更改，将导致很多网站的逻辑出现漏洞问题，就没有更改过来，于是一直遗留到现在。作为学习者，我们只需要记住就好。

#### constructor

```javascript
console.log(("1").constructor === String);
console.log((1).constructor === Number);
console.log((true).constructor === Boolean);
//console.log((null).constructor === Null);
//console.log((undefined).constructor === Undefined);
console.log(([]).constructor === Array);
console.log((function() {}).constructor === Function);
console.log(({}).constructor === Object);
```

![img](https://images2017.cnblogs.com/blog/1248022/201710/1248022-20171003143707771-1810672206.png)

（这里依然抛开null和undefined）乍一看，constructor似乎完全可以应对基本数据类型和引用数据类型，都能检测出数据类型，事实上并不是如此，来看看为什么：

```javascript
function Fn(){};

Fn.prototype=new Array();

var f=new Fn();

console.log(f.constructor===Fn);
console.log(f.constructor===Array);
```

![img](https://images2017.cnblogs.com/blog/1248022/201710/1248022-20171003144258646-2100132702.png)

我声明了一个构造函数，并且把他的原型指向了Array的原型，所以这种情况下，constructor也显得力不从心了。

看到这里，是不是觉得绝望了。没关系，终极解决办法就是第四种办法，看过jQuery源码的人都知道，jQuery实际上就是采用这个方法进行数据类型检测的。

#### Object.prototype.toString.call()

```javascript
var a = Object.prototype.toString;

console.log(a.call("aaa"));
console.log(a.call(1));
console.log(a.call(true));
console.log(a.call(null));
console.log(a.call(undefined));
console.log(a.call([]));
console.log(a.call(function() {}));
console.log(a.call({}));
```

![img](https://images2017.cnblogs.com/blog/1248022/201710/1248022-20171003145117021-1708327412.png)

可以看到，所有的数据类型，这个办法都可以判断出来。那就有人质疑了，假如我把他的原型改动一下呢？如你所愿，我们看一下：

![img](https://images2017.cnblogs.com/blog/1248022/201710/1248022-20171003145523786-1597998789.png)

可以看到，依然可以得到正确的结果。好了，今天就说到这里，欢迎关注我的博客，一起交流学习前端知识。

### 闭包

#### 什么是闭包

高级程序设计三中:闭包是指有权访问另外一个函数作用域中的变量的函数.可以理解为(**能够读取其他函数内部变量的函数**)

**闭包的作用:** 正常函数执行完毕后,里面声明的变量被垃圾回收处理掉,但是闭包可以让作用域里的 变量,在函数执行完之后依旧保持没有被垃圾回收处理掉

#### 闭包的实例

```javascript
// 创建闭包最常见的方式函数作为返回值
function foo() {
  var name = "kebi";
  return function() {
    console.log(name);
  };
}
var bar = foo();
bar(); //打印kebi    --外部函数访问内部变量
```

**接下来通过一个实例来感受一下闭包的作用:**

接下来实现一个计数器大家肯定会觉得这不是很简单吗

```javascript
var count = 0;

function add() {
  count = count + 1;
  console.log(count);
}
add(); //确实实现了需求
//但是如果需要第二个计数器呢?
//难道要如下这样写吗?
var count1 = 0;

function add1() {
  count1 = count1 + 1;
  console.log(count1);
}
add1(); //确实实现了需求
```

**当我们需要更多地时候,这样明显是不现实的,这里我们就需要用到闭包.**

```javascript
function addCount() {
  var conut = 0;
  return function() {
    count = count + 1;
    console.log(count);
  };
}
```

这里解释一下上边的过程: addCount() 执行的时候, **返回一个函数**, 函数是可以**创建自己的作用域的**, 但是此时返回的这个函数内部需要引用 addCount() **作用域下的变量 count**, **因此这个 count 是不能被销毁的**.接下来需要几个计数器我们就定义几个变量就可以,**并且他们都不会互相影响,每个函数作用域中还会保存 count 变量不被销毁,进行不断的累加**

```javascript
var fun1 = addCount();
fun1(); //1
fun1(); //2
var fun2 = addCount();
fun2(); //1
fun2(); //2
```

#### 闭包常见面试题

```javascript
//闭包for循环
for (var i = 0; i < 4; i++) {
  setTimeout(function() {
    console.log(i);
  }, 300);
}
```

上边打印出来的都是 4, 可能部分人会认为打印的是 0,1,2,3

**原因**:js 执行的时候首先**会先执行主线程,异步相关的会存到异步队列里**,当主线程执行**完毕开始执行异步队列**, 主线程执行完毕后,此时 i 的值为 4,说以在执行异步队列的时候,打印出来的都是 4(这里需要大家对 **event loop 有所了解(js 的事件循环机制)**)

**1.如何修改使其正常打印:(使用闭包使其正常打印)**

```javascript
//方法一:
for (var i = 0; i < 4; i++) {
  setTimeout(
    (function(i) {
      return function() {
        console.log(i);
      };
    })(i),
    300
  );
}
// 或者
for (var i = 0; i < 4; i++) {
  setTimeout(
    (function() {
      var temp = i;
      return function() {
        console.log(temp);
      };
    })(),
    300
  );
}
//这个是通过自执行函数返回一个函数,然后在调用返回的函数去获取自执行函数内部的变量,此为闭包

//方法发二:
for (var i = 0; i < 4; i++) {
  (function(i) {
    setTimeout(function() {
      console.log(i);
    }, 300);
  })(i);
}
// 大部分都认为方法一和方法二都是闭包,我认为方法一是闭包,而方法二是通过创建一个自执行函数,使变量存在这个自执行函数的作用域里
```

**2.真实的获取多个元素并添加点击事件**

```javascript
var op = document.querySelectorAll("p");
for (var j = 0; j < op.length; j++) {
  op[j].onclick = function() {
    alert(j);
  };
}
//alert出来的值是一样的
// 解决办法一:
for (var j = 0; j < op.length; j++) {
  (function(j) {
    op[j].onclick = function() {
      alert(j);
    };
  })(j);
}
// 解决办法二:
for (var j = 0; j < op.length; j++) {
  op[j].onclick = (function(j) {
    return function() {
      alert(j);
    };
  })(j);
}
//解决方法三其实和二类似
for (var j = 0; j < op.length; j++) {
  op[j].onclick = (function() {
    var temp = j;
    return function() {
      alert(j);
    };
  })();
}

//这个例子和例子一几乎是一样的大家可以参考例子一
```

**3.闭包的缺陷:**

通过上边的例子也发现, 闭包会导致内存占用过高,因为变量都没有释放内存

### promise有哪几种状态，promise.all怎么用

promise有三种状态：pending/reslove/reject 。pending就是未决，resolve可以理解为成功，reject可以理解为拒绝。

promise.all一般是需要好几个异步请求返回后才正常显示才会用到的

```javascript
let p1 = wake(3000)
let p2 = wake(2000)

Promise.all([p1, p2]).then((result) => {
  console.log(result)       // [ '3秒后醒来', '2秒后醒来' ]
}).catch((error) => {
  console.log(error)
})
```

