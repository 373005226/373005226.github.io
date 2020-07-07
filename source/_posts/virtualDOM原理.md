---
title: virtual DOM原理
date: 2020-07-02 11:07:40
summary: 详解Vue的virtual DOM原理
categories:
    - 前端
tags:
    - Vue
    - 转载
---

> 文章内有版权所在，不方便转载，所以只贴出链接
>
> [推荐文章](https://blog.csdn.net/dichu2296/article/details/102233749?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase)
>
> [参考文章1](https://blog.csdn.net/weixin_41223912/article/details/83385820?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.nonecase)
>
> [参考文章2](https://blog.csdn.net/dichu2296/article/details/102233749?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase)
>
> [详解文章](https://blog.csdn.net/u013369549/article/details/86287418?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase)

## 原理简介

1，数据和模板相结合生成虚拟dom

2，虚拟dom转化成真实dom渲染到页面上

3，当数据发生改变时，新的数据和模板相结合会生成新的虚拟dom

4，新旧虚拟dom进行比对找差异

5，找到差异后根据差异改变dom

6，老的虚拟dom被清除，新的虚拟dom变成老的虚拟dom

## 简明原理

### 1、定义：

虚拟DOM其实就是一棵以 `JavaScript 对象`( VNode 节点)作为基础的树，用对象属性来描述节点， 实际上它只是一层对真实 DOM 的抽象。最终可以通过一系列操作使这棵树映射到真实环境上。

相当于在js与DOM之间做了一个缓存，利用patch`（diff算法）`对比新旧虚拟DOM记录到一个对象中按需更新， 最后创建真实的DOM

### 2、虚拟dom原理流程

**`模板 ==> 渲染函数 ==> 虚拟DOM树 ==> 真实DOM`**

vuejs通过编译将模板（template）转成渲染函数（render），执行渲染函数可以得到一个虚拟节点树

在对 Model 进行操作的时候，会触发对应 Dep 中的 Watcher 对象。Watcher 对象会调用对应的 update 来修改视图。

图解：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702112853)

> **渲染函数：** 渲染函数是用来生成`Virtual DOM`的。
> **VNode虚拟节点：** 它可以代表一个真实的dom节点。通过 `createElement` 方法能将 `VNode` 渲染成 `dom` 节点。简单地说，vnode可以理解成`节点描述对象`，它描述了应该怎样去创建真实的DOM节点
> **patch(也叫做patching算法)：** 虚拟DOM最核心的部分，它可以将vNode渲染成真实的DOM，这个过程是`对比新旧虚拟节点`之间有哪些不同，然后根据对比结果找出需要更新的的节点进行更新

### 3、虚拟DOM好处

具备跨平台的优势–由于 Virtual DOM 是以 `JavaScript 对象`为基础而不依赖真实平台环境，所以使它具有了跨平台的能力，比如说浏览器平台、Weex、Node 等。
操作 DOM 慢，js运行效率高。我们可以将DOM对比操作放在`JS层`，提高效率。运用patching算法来计算出真正需要更新的节点，最大限度地`减少DOM操作`，从而显著提高性能。Virtual DOM 本质上就是在 JS 和 DOM 之间做了一个缓存。
提升渲染性能 Virtual DOM的优势不在于单次的操作，而是在大量、频繁的数据更新下，能够对视图进行`合理、高效的更新`
虚拟DOM就是为了`解决浏览器性能问题`而被设计出来的

## 详细原理

#### 原理讲解

Vue的核心是双向绑定和虚拟DOM

- 虚拟Dom（vdom） 参考：Vue原理解析之Virtual Dom， ppt

  Dom操作是比较浪费时间和性能的，当数据量很大时，更新DOM是非常耗费性能的操作。
  当我们使用Javascript来修改我们的页面，浏览器已经做了一些工作，以找到DOM节点进行更改，例如：

  document.getElementById('myId').appendChild(myNewNode);
  1
  在现代的应用中，会有成千上万数量个DOM节点。所以在数据更新的时候产生的计算非常昂贵。
  琐碎且频繁的更新会使页面缓慢，同时这也是不可避免的。虚拟Dom技术就是用于提高页面更新的速度。

  简单来讲虚拟Dom技术的核心就是使用js对象来替代dom节点。
  举个栗子：

  DOM节点在HTML文档中的表现通常是这样的

  ```html
  <ul id='list'>
    <li>Item 1</li>
    <li>Item 2</li>
  <ul>
  ```

  DOM节点也可以表示为一个JavaScript对象，就像这样

  ```js
  //用Javascript代码表示DOM节点的伪代码
  Let domNode = {
    tag: 'ul'
    attributes: { id: 'list' }
    children: [
   //这里是 li
    ]
  };
  当我们更新虚拟节点时
  //更新虚拟DOM的代码
  domNode.children.push('<ul>Item 3</ul>');
  ```

  如果我们用一个虚拟DOM，而不是直接调用像.getElementById的方法，这样只操作JavaScript对象，这样是相当方便的。

  然后，再把更改的部分更新到真正的DOM，方法如下：

  ```js
  //这个方法是调用DOM API来更改真正DOM的 它会分批执行从而获取更高的效率
  sync(originalDomNode, domNode);
  ```

- 双向绑定

  几种实现双向绑定的做法

   目前几种主流的mvc(vm)框架都实现了单向数据绑定，而我所理解的双向数据绑定无非就是在单向绑定的基础上给可输入元素（input、textare等）添加了change(input)事件，来动态修改model和 view，并没有多高深。所以无需太过介怀是实现的单向或双向绑定。

  - 发布者-订阅者模式（backbone.js）
  - 脏值检查（angular.js） 
  - 数据劫持（vue.js）

  **发布者-订阅者模式:** 一般通过sub, pub的方式实现数据和视图的绑定监听，更新数据方式通常做法是 vm.set('property', value)，这里有篇文章讲的比较详细，有兴趣可点这里

  这种方式现在毕竟太low了，我们更希望通过 vm.property = value这种方式更新数据，同时自动更新视图，于是有了下面两种方式

  **脏值检查:** angular.js 是通过脏值检测的方式比对数据是否有变更，来决定是否更新视图，最简单的方式就是通过 setInterval() 定时轮询检测数据变动，当然Google不会这么low，angular只有在指定的事件触发时进入脏值检测，大致如下：

  - DOM事件，譬如用户输入文本，点击按钮等。( ng-click )
  - XHR响应事件 ( $http )
  - 浏览器Location变更事件 ( $location )
  - Timer事件( timeout,timeout , timeout,interval )
    执行 $digest() 或 $apply()

  **数据劫持:** vue.js 则是采用数据劫持结合发布者-订阅者模式的方式，通过Object.defineProperty()来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调。

  ![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200707162855)

  - 当前台显示view发生变化了，它会实时反应到viewModel上，如果有需要，viewModel 会通过ajax等方法将改变的数据 传递给后台model
  - 从后台model获取过来的数据，通过vm将值响应到前台UI上

  ![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200707162907)

  vm的核心是view 和 data

  - 当data 有变化的时候它通过Object.defineProperty(）方法中的set方法进行监控，并调用在此之前已经定义好data 和view的关系了的回调函数，来通知view进行数据的改变
  - 而view 发生改变则是通过底层的input 事件来进行data的响应更改

  vue是通过Object.defineProperty()来实现数据劫持的

  Object.defineProperty( )是用来做什么的？它可以来控制一个对象属性的一些特有操作，比如读写权、是否可以枚举，这里我们主要先来研究下它对应的两个描述属性get和set

  ```js
  var Book= {} 
  var name= '';
     Object.defineProperty(Book, 'name', {
         set:function(value) {
              name= value;
              console.log('你取了一个书名叫做'+ value);
         },
         get:function() {
              return'《'+ name+ '》'
         }
     }
     console.log(Book)
     Book.name= 'vue权威指南'; // 你取了一个书名叫做vue权威指南
     console.log(Book.name); // 《vue权威指南》
     // get 是在读取那么属性的时候触发的
     // set 是在设置属性值的时候触发的
  ```

  实现方法： **观察者模式**

  ![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200707162951)

  Observer（Objec.defineProperty中的set）

  Observer（Objec.defineProperty中的set）监听data的变化，当data有变化的时候通知观察者列表Dep（里面有与data变化对应的update函数）,watcher负责向观察者列表里添加（订阅）对应的更新函数，Dep里的更新函数执行完了之后将最新的值更新到view上。

  具体的代码实现可参考：https://www.cnblogs.com/libin-1/p/6893712.html

  ### 实现

   需预备es6一些语法，比较常用的es6的语法, es5的写法也可以，尽量使用javascript新标准

  #### 什么是ES5

   作为ECMAScript第五个版本（第四版因为过于复杂废弃了），增加特性如下。

- strict模式

  严格模式，限制一些用法，‘use strict’;

- Array增加方法

  增加了every、some 、forEach、filter 、indexOf、lastIndexOf、isArray、map、reduce、reduceRight方法

  PS： 还有其他方法 Function.prototype.bind、String.prototype.trim、Date.now

- Object方法

  Object.getPrototypeOf

  Object.create

  Object.getOwnPropertyNames

  Object.defineProperty

  Object.getOwnPropertyDescriptor

  Object.defineProperties

  Object.keys

  Object.preventExtensions / Object.isExtensible

  Object.seal / Object.isSealed

  Object.freeze / Object.isFrozen

  PS：只讲有什么，不讲是什么。

#### 什么是ES6

 ECMAScript6在保证向下兼容的前提下，提供大量新特性

#### ES6特性如下

- 块级作用域 关键字let, 常量const

- 赋值解构

  ```js
  let singer = { first: "Bob", last: "Dylan" };
  let { first: f, last: l } = singer; //  f = "Bob", l = "Dylan"
  let [a, b, c] = [1,2, 3] // a =1  ,b =2 , c =3
  let [all, year, month, day] =  /^(\d\d\d\d)-(\d\d)-(\d\d)$/.exec("2015-10-25");
  let [x, y] = [1, 2, 3]; // x = 1, y = 2
  ```

- 函数参数 - 默认值、参数打包、 数组展开（Default 、Rest 、Spread）

  ```js
  //es6
  function findArtist(name='lu', age='26') {
      ...
  }
  //es5
  function findArtist(name, age) {
    var _name = name || 'lu';
    var _age = age || '26';
      ...
  }
  
  //Rest
  function f(x, ...y) {
    // y is an Array
    console.log(y);
    return x * y.length;
  }
  // ['hello', true]
  f(3, "hello", true) == 6
  
  //Spread
  function f(x, y, z) {
    return x + y + z;
  }
  // Pass each elem of array as argument
  f(...[1,2,3]) == 6
  ```

- 箭头函数 Arrow functions

  简化了[代码](http://caibaojian.com/c/code)形式，默认return表达式结果

  ```js
  cont b = a => a + '(demo)';
  
  cont b = function(a) {
    return a + '(demo)'
  }
  ```

- 字符串模板 Template strings

  ```js
  var name = "Bob", time = "today";
  `Hello ${name}, how are you ${time}?`
  // return "Hello Bob, how are you today?"
  ```

- Iterators（迭代器）+ for…of

  ```js
  for (var n of ['a','b','c']) {
    console.log(n);
  }
  // 打印a、b、c
  
  for (const [index, info] of ['a','b','c'].entries()} {
    
  }
       
  for (const [key, val] of Object.entries({a:1, b:2})} {
    
  }
  
  ```

- Class

  Class，有constructor、extends、super，但本质上是语法糖（对语言的功能并没有影响，但是更方便[程序员](http://caibaojian.com/a-programmer/)使用）。

  ```js
  //es6
  class Artist {
      constructor(name) {
          this.name = name;
      }
      perform() {
          return this.name + " performs ";
      }
  }
  class Singer extends Artist {
      constructor(name, song) {
          super.constructor(name);
          this.song = song;
      }
      perform() {
          return super.perform() + "[" + this.song + "]";
      }
  }
  
  //es5
  function Artist(name) {
      this.name = name;
      this.perform = function() {
          return this.name + " performs ";
      }
  }
  Artist.prototype.perform = function () {
       return this.name + " performs ";
  };
  
  function Singer(song, name) {
      this.song = song;
      Artist.call(this, name);
      this.perform = function() {
           return this.perform() + "[" + this.song + "]";
      }
  }
  
  // es6
  let james = new Singer("Etta James", "At last");
  james instanceof Artist; // true
  james instanceof Singer; // true
  
  james.perform(); // "Etta James performs [At last]"
  
  //es5
   Singer.prototype = Object.create(Artist.prototype);
   Singer.prototype.constructor = Artist;
   var james = new Singer("Etta James", "At last");
  
  ```

- Modules

  ES6的内置模块功能借鉴了CommonJS和AMD各自的优点：

  (1).具有CommonJS的精简语法、唯一导出出口(single exports)和循环依赖(cyclic dependencies)的特点。

  (2).类似AMD，支持异步加载和可配置的模块加载。

  ```js
  // lib/math.js
  export function sum(x, y) {
    return x + y;
  }
  export var pi = 3.141593;
  
  // app.js
  import * as math from "lib/math";
  alert("2π = " + math.sum(math.pi, math.pi));
  
  // otherApp.js
  import {sum, pi} from "lib/math";
  alert("2π = " + sum(pi, pi));
  
  Module Loaders:
  // Dynamic loading – ‘System’ is default loader
  System.import('lib/math').then(function(m) {
    alert("2π = " + m.sum(m.pi, m.pi));
  });
  
  // Directly manipulate module cache
  System.get('jquery');
  System.set('jquery', Module({$: $})); // WARNING: not yet finalized
  
  //es5 
  document.write("<script language=javascript src='*.js'></script>");
  ```

  

- Map + Set + WeakMap + WeakSet

  四种集合类型，WeakMap、WeakSet作为属性键的对象如果没有别的变量在引用它们，则会被回收释放掉。

  ```js
  // Sets
  var s = new Set();
  s.add("hello").add("goodbye").add("hello");
  s.size === 2;
  s.has("hello") === true;
  
  // Maps
  var m = new Map();
  m.set("hello", 42);
  m.set(s, 34);
  m.get(s) == 34;
  
  //WeakMap
  var wm = new WeakMap();
  wm.set(s, { extra: 42 });
  wm.size === undefined
  
  // Weak Sets
  var ws = new WeakSet();
  ws.add({ data: 42 });//Because the added object has no other references, it will not be held in the set
  ```

  

- Math + Number + String + Array + Object APIs

  一些新的API

  ```js
  Number.EPSILON
  Number.isInteger(Infinity) // false
  Number.isNaN("NaN") // false
  
  Math.acosh(3) // 1.762747174039086
  Math.hypot(3, 4) // 5
  Math.imul(Math.pow(2, 32) - 1, Math.pow(2, 32) - 2) // 2
  
  "abcde".includes("cd") // true
  "abc".repeat(3) // "abcabcabc"
  
  Array.from(document.querySelectorAll('*')) // Returns a real Array
  Array.of(1, 2, 3) // Similar to new Array(...), but without special one-arg behavior
  
  [0, 0, 0].fill(7, 1) // [0,7,7]
  [1, 2, 3].find(x => x == 3) // 3
  [1, 2, 3].findIndex(x => x == 2) // 1
  [1, 2, 3, 4, 5].copyWithin(3, 0) // [1, 2, 3, 1, 2]
  ["a", "b", "c"].entries() // iterator [0, "a"], [1,"b"], [2,"c"]
  ["a", "b", "c"].keys() // iterator 0, 1, 2
  ["a", "b", "c"].values() // iterator "a", "b", "c"
  
  Object.assign(Point, { origin: new Point(0,0) })
  ```

  

- Proxies

  使用代理（Proxy）监听对象的操作，然后可以做一些相应事情。对象属性拦截修改。

  ```js
  var target = {};
  var handler = {
    get: function (receiver, name) {
      return `Hello, ${name}!`;
    }
  };
  
  var p = new Proxy(target, handler);
  p.world === 'Hello, world!';
  ```

  可监听的操作： get、set、has、deleteProperty、apply、construct、getOwnPropertyDescriptor、defineProperty、getPrototypeOf、setPrototypeOf、enumerate、ownKeys、preventExtensions、isExtensible。

- Symbols

  Symbol是一种基本类型。Symbol 通过调用symbol函数产生，它接收一个可选的名字参数，该函数返回的symbol是唯一的。

  ```js
  var key = Symbol("key");
  var key2 = Symbol("key");
  key == key2  //false
  ```

- Promises

  Promises是处理异步操作的对象，使用了 Promise 对象之后可以用一种链式调用的方式来组织代码，让代码更加直观（类似[jQuery](http://caibaojian.com/jquery/)的deferred 对象）。

  这里我建议使用es6 新更新 的 async 异步操作，更简洁方便

  ```js
  function fakeAjax(url) {
    return new Promise(function (resolve, reject) {
      // setTimeouts are for effect, typically we would handle XHR
      if (!url) {
        return setTimeout(reject, 1000);
      }
      return setTimeout(resolve, 1000);
    });
  }
  
  // no url, promise rejected
  fakeAjax().then(function () {
    console.log('success');
  },function () {
    console.log('fail');
  });
  
  
  // Ajax 是异步的，异步函数调用异步函数
  async a () {
    const response = await Ajax();
    return response.id;
  }
  
  async c() {
      const result = await this.a();
  }
  
  // 调用异步函数
  b () {
    a().then(result => {
      console.log(result);
    })
  }
  ```

  