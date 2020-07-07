---
title: JavaScript原型链
date: 2020-05-28 17:43:11
summary: 三张图搞定JavaScript的原型对象于原型链
categories:
    - 前端
tags:
    - Vue
    - JavaScript
    - 前端面试题
    - 转载
---

> 作者：前小白
> 链接：https://www.jianshu.com/p/08c07a953fa0
> 来源：简书
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

JS中原型链，说简单也简单。

首先明确： 函数（Function）才有prototype属性，对象（除Object）拥有__proto__。

首先，我画了一张图。都可以有一个原型_proto_，这个原型还可以有它自己的原型，以此类推，形成一个原型链。查找特定属性的时候，我们先去这个对象里去找，如果没有的话就去它的原型对象里面去，如果还是没有的话再去向原型对象的原型对象里去寻找...... 这个操作被委托在整个原型链上，这个就是我们说的原型链了。

**二、原型指针**

  我们知道了原型的概念，接下来我们就照着上面的图来具体分析一下原型的指针；中间最上面蓝色模块标注的构造函数Foo, 里面有两个属性： _proto_ 和 prototype, 这两个很容易使人混淆，先说说prototype:

**prototype:**

  prototype属性，它是**函数所独有的**，它是从**一个函数指向一个对象**。它的含义是**函数的原型对象**，也就是这个函数（其实所有函数都可以作为构造函数）所创建的实例的原型对象; 这个属性是一个指针，指向一个对象，这个对象的用途就是包含所有实例共享的属性和方法（我们把这个对象叫做原型对象）;

`__proto__`:

  `__proto__`是原型链查询中实际用到的，它总是指向 prototype，换句话说就是指向构造函数的原型对象，它是**对象独有的。**注意，为什么Foo构造也有这个属性呢，因为再js的宇宙里万物皆对象，包括函数；

根据以上的概括我们能知道Foo构造函数`__proto__`指向的是他的构造函数的原型对象，它的构造函数是Function, 也就是说Foo的_proto_指向Function.prototype, 我们再看到左边绿色的a和b函数的`__proto__`指像的是Foo.prototype,因为他们是通过 new Foo实例化出来的，它们的构造函数就是Foo(), 即a._proto_ = Foo.prototype； 接着我们来看看最右边紫色的模块Function.prororype, 它的_proto_指针指向的是Object.prototype,Object._proto_又为null.。于是我们就可以得出：在原型链中的指向是，函数 → 构造行数 → Function.prototype → Object.protype → null ;



**constructor：**

  我们看到途中最中间灰色模块有一个constructor属性，这个又是做什么用的呢？


> 每个函数都有一个原型对象，该原型对象有一个constructor属性，指向创建对象的函数本身。

  此外，我们还可以使用constructor属性，所有的实例对象都可以访问constructor属性，constructor属性是创建实例对象的函数的引用。我们可以使用constructor属性验证实例的原型类型（与操作符instanceof非常类似）。

  由于constructor属性仅仅是原始构造函数的引用，因此我们可以使用该属性创建新的对象，如：

  

![img](https:////upload-images.jianshu.io/upload_images/15932532-8c96be3aa1f32228.png?imageMogr2/auto-orient/strip|imageView2/2/w/403/format/webp)

  通过第一个对象实例化对象的constuctor方法创建第2个实例化对象，说明创建的新对象ninja2 是Ninja的实例，由于ninja和ninja2不是同一个对象可以得出它们是两个截然不同的实例；

**结论：**

​    1、__proto__ 是原型链查询中实际用到的，它总是指向 prototype；

​    2、prototype 是函数所独有的**，**在定义构造函数时自动创建，它总是被 __proto__ 所指。

所有对象都有__proto__属性，函数这个特殊对象除了具有__proto__属性，还有特有的原型属性prototype。prototype对象默认有两个属性，constructor属性和__proto__属性。prototype属性可以给函数和对象添加可共享（继承）的方法、属性，而__proto__是查找某函数或对象的原型链方式。constructor，这个属性包含了一个指针，指回原构造函数。

![img](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200528180124)

所谓原型链，指的就是图中的**proto**这一条指针链！

原型链的顶层就是Object.prototype，而这个对象的是没有原型对象的。

可在chrome的控制台里面输入：

Object.__proto__

输出是:

functionEmpty(){}

原型链，如此而已。



对于新人来说，JavaScript的原型是一个很让人头疼的事情，一来prototype容易与__proto__混淆，二来它们之间的各种指向实在有些复杂，其实市面上已经有非常多的文章在尝试说清楚，有一张所谓很经典的图，上面画了各种线条，一会连接这个一会连接那个，说实话我自己看得就非常头晕，更谈不上完全理解了。所以我自己也想尝试一下，看看能不能把原型中的重要知识点拆分出来，用最简单的图表形式说清楚。

我们知道原型是一个对象，其他对象可以通过它实现属性继承。但是尼玛除了prototype，又有一个__proto__是用来干嘛的？长那么像，让人怎么区分呢？它们都指向谁，那么混乱怎么记啊？原型链又是什么鬼？相信不少初学者甚至有一定经验的老鸟都不一定能完全说清楚，下面用三张简单的图，配合一些示例代码来理解一下。

## 一、prototype和__proto__的区别

![img](https://images2015.cnblogs.com/blog/787416/201603/787416-20160323103557261-114570044.png)

```javascript
var a = {};
console.log(a.prototype);  //undefined
console.log(a.__proto__);  //Object {}

var b = function(){}
console.log(b.prototype);  //b {}
console.log(b.__proto__);  //function() {}
```



```javascript
/*1、字面量方式*/
var a = {};
console.log(a.__proto__);  //Object {}

console.log(a.__proto__ === a.constructor.prototype); //true

/*2、构造器方式*/
var A = function(){};
var a = new A();
console.log(a.__proto__); //A {}

console.log(a.__proto__ === a.constructor.prototype); //true

/*3、Object.create()方式*/
var a1 = {a:1}
var a2 = Object.create(a1);
console.log(a2.__proto__); //Object {a: 1}

console.log(a.__proto__ === a.constructor.prototype); //false（此处即为图1中的例外情况）
```

![什么是原型链？](https://images2015.cnblogs.com/blog/787416/201603/787416-20160322110905589-2039017350.png)

```javascript
var A = function(){};
var a = new A();
console.log(a.__proto__); //A {}（即构造器function A 的原型对象）
console.log(a.__proto__.__proto__); //Object {}（即构造器function Object 的原型对象）
console.log(a.__proto__.__proto__.__proto__); //null
```

