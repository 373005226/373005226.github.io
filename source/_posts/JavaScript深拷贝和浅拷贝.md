---
title: JavaScript深拷贝和浅拷贝
date: 2020-07-07 18:21:34
summary: 详解JavaScript中的深拷贝和浅拷贝
categories:
    - 前端
tags:
    - JavaScript
    - 转载
    - 前端面试题
---

> 作者：super_wei
> 链接：https://www.jianshu.com/p/0b66b8462860
> 来源：简书
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

我们通常在对数据进行拷贝的时候会踩到数据拷贝的坑中，主要是对数据和指针的引用区别，这篇文章就主要分析深、浅拷贝的区别和方式

## 堆&栈

如下图所示，两者都是存放临时数据的地方。

栈是先进后出的，就像一个桶，后进去的先出来，它下面本来有的东西要等其他出来之后才能出来。

堆是在程序运行时，而不是在程序编译时，申请某个大小的内存空间。即动态分配内存，对其访问和对一般内存的访问没有区别。对于堆，我们可以随心所欲的进行增加变量和删除变量，不用遵循次序。

数据类型分为基础类型和引用类型，在我们声明数据的时候，基础类型时存放到栈里面，引用类型时存放到堆里面，在拷贝的时候是直接从栈这边去取，获取基本类型时直接获取值本身，而引用类型只是在栈这边存的是一个指向堆相应地址的指针。

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200707182252)

## 赋值、深浅拷贝的区别

赋值：当我们把一个对象赋值给一个新的变量时，**赋的其实是该对象的在栈中的地址，而不是堆中的数据**。也就是两个对象指向的是同一个存储空间，无论哪个对象发生改变，其实都是改变的存储空间的内容，因此，两个对象是联动的。

浅拷贝：浅拷贝是创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值，如果属性是引用类型，拷贝的就是指向堆的地址 ，所以**如果一个拷贝对象改变了其中的一个地址，则另一个拷贝对象的该地址也会发生改变**。

深拷贝： 深拷贝是将一个对象从内存中完整的拷贝一份出来,从堆内存中开辟一个新的区域存放新对象,且**修该拷贝后的对象不会影响到原有的对象；**

赋值：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200707182305)

浅拷贝：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200707182314)

深拷贝：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200707182324)

## 深拷贝和浅拷贝详解

> 本文知识点引用于：http://caibaojian.com/javascript-array-copy.html

案例：

array1：

```js
let array1 = [1,'a',true,null,undefined];
```

### js的原生方法

#### slice()方法

```js
let c1 = array1.slice();
```

#### concat()方法

```js
let cc1 = array1.concat();
```

#### from()方法

```js
let fc1 = Array.from(array1);
```

#### push()方法

```js
let pc1 = [];
Array.prototype.push.apply(pc1,array1);
```

#### map()方法

```js
let mc1 = array1.map(function(item){
    return item;
    });
```

以上几种方法都能实现数组的`浅拷贝`，即数组的每一项只能是原始类型的数据，如果数组的项包含引用类型，如数组（即js中的二维数组），对象等，以上方法复制的项只是引用。

还有一种方法是，使用[JSON](http://caibaojian.com/t/json)进行转换，先将数组序列化为json字符串，然后再将字符串转换成json对象即可。

### JSON方法的详解

```js
let jsonc = JSON.parse(JSON.stringify(array1));
```

这种方法可以变相的实现`深拷贝`,但是这种方法也有其限制：[·](http://caibaojian.com/javascript-array-copy.html)

- 首先，数组中的项如果是`undefined`，那么转换后将变为`null`
- 如果数组的项为对象，那么对象之间不可相互引用。会造成循环引用，无法JSON序列化。

## 总结：性能分析

以上几种方法都可以实现数组的拷贝，那么，每种方法的性能如何呢，我使用`console.time()`和`console.timeEnd()`跟踪当数组大小为100-10000000时，每个方法所用的时间。注意，这里数组每一项都是随机的5种原始值的一个，不包含引用类型

| 数组大小 | forof      | slice    | concat    | from       | push                             | map        | json       |
| :------- | :--------- | :------- | --------- | ---------- | -------------------------------- | ---------- | ---------- |
| 100      | 0.593ms    | 0.038ms  | 0.034ms   | 0.404ms    | 0.054ms                          | 0.193ms    | 0.042ms    |
| 1000     | 1.606ms    | 0.052ms  | 0.044ms   | 1.311ms    | 0.090ms                          | 0.897ms    | 0.124ms    |
| 10000    | 2.272ms    | 0.097ms  | 0.093ms   | 3.294ms    | 0.145ms                          | 1.845ms    | 0.772ms    |
| 100000   | 14.665ms   | 0.901ms  | 0.730ms   | 22.283ms   | 4.002ms                          | 15.894ms   | 9.101ms    |
| 1000000  | 175.663ms  | 16.051ms | 8.400ms   | 235.900ms  | Maximum call stack size exceeded | 144.058ms  | 97.946ms   |
| 10000000 | 1597.242ms | 83.860ms | 124.781ms | 2425.540ms | Maximum call stack size exceeded | 1424.344ms | 1043.772ms |

> 当数组大小超过1000000时，push方式就挂了，报错：`Maximum call stack size exceeded`

webkit浏览器使用cocat().非webkit使用slice()。[·](http://caibaojian.com/javascript-array-copy.html)

补上之前文章的另外另种实现：

### 简单的引用复制

```js
function shallowClone(copyObj) {
  var obj = {};
  for ( var i in copyObj) {
    obj[i] = copyObj[i];
  }
  return obj;
}
var x = {
  a: 1,
  b: { f: { g: 1 } },
  c: [ 1, 2, 3 ]
};
var y = shallowClone(x);
console.log(y.b.f === x.b.f);     // true
```

### Object.assign()

Object.assign() 方法可以把任意多个的源对象自身的可枚举属性拷贝给目标对象，然后返回目标对象。[·](http://caibaojian.com/javascript-array-copy.html)

```
//code from http://caibaojian.com/javascript-array-copy.html
var x = {
  a: 1,
  b: { f: { g: 1 } },
  c: [ 1, 2, 3 ]
};
var y = Object.assign({}, x);
console.log(y.b.f === x.b.f);     // true
```

### 深度拷贝

### 方法1：使用 JSON 方法

JSON.stringify(array) 然后再 JSON.parse()。示例：[·](http://caibaojian.com/javascript-array-copy.html)



```js
//code from http://caibaojian.com/javascript-array-copy.html
var arr1 = [1, 2, [3, 4], {a: 5, b: 6}, 7],
arr2 = JSON.parse(JSON.stringify(arr1));

console.log(arr1, arr2);
arr2[1] = 10;
arr2[3].a = 20;
console.log(arr1[1], arr2[1]);
console.log(arr1[3], arr2[3]);
```

此方法存在对古老浏览器的兼容性问题。如确需要作兼容，可引入如下兼容文件解决：[·](http://caibaojian.com/javascript-array-copy.html)

https://github.com/douglascrockford/JSON-js/blob/master/json2.js

**注意：**如果数组值为函数，上述方法还是不行的



**注意：**如果数组值为函数，上述方法还是不行的。[·](http://caibaojian.com/javascript-array-copy.html)

### 方法2：深度复制的完全实现

考虑到多维数组的嵌套，以及数组值为对象的情况，可以作如下原型扩展（当然写为普通函数实现也是可以的，原型扩展是不建议的方式）：

```js
Object.prototype.clone = function () {
var o = {};
for (var i in this) {
o[i] = this[i];
}
return o;
};
Array.prototype.clone = function () {
var arr = [];
for (var i = 0; i < this.length; i++)
if (typeof this[i] !== 'object') {
arr.push(this[i]);
} else {
arr.push(this[i].clone());
}
return arr;
};

//测试1 Object
var obj1 = {
name: 'Rattz',
age: 20,
hello: function () {
return "I'm " + name;
}
};
var obj2 = obj1.clone();
obj2.age++;
console.log(obj1.age);

//测试2 Array
var fun = function(log) {console.log},
arr1 = [1, 2, [3, 4], {a: 5, b: 6}, fun],
arr2 = arr1.clone();

console.log(arr1, arr2);

arr2[2][1]= 'Mike';
arr2[3].a = 50;
arr2[4] = 10;
console.log(arr1, arr2);
```

原文链接：http://caibaojian.com/javascript-array-copy.html

### 方法3：使用 [jQuery](http://caibaojian.com/jquery/) 的 extend 方法

如果你在使用 jQuery，那么最简单的方法是使用 extend 插件方法。示例：[·](http://caibaojian.com/javascript-array-copy.html)

```
//code from http://caibaojian.com/javascript-array-copy.html
var arr1 = [1, 2, [3, 4], {a: 5, b: 6}, 7],
arr2 = $.extend(true, [], arr1);

console.log(arr1, arr2);
arr2[1] = 10;
console.log(arr1, arr2);
```




