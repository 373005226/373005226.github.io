---
title: const语法
author: LY
date: 2019-11-04
categories:
    - 前端
tags:
    - ES6语法
---

> 以下皆是由https://www.bilibili.com/video/av59594689?from=search&seid=2169751232721048853
>
> 视频中的老师操作代码和讲义总结而成

## const定义常量

const在C和C++等语言都有定义，意为定义常量，在ES6语法中也是如此

const定义的常量之后就不可在被更改，而且在定义的时候必须定义它的值，不然会报错

```js
//错误写法
const demo;
//Uncaught SyntaxError: Missing initializer in const declaration
```

const只是定义指向的对象不可再被更改，但是他内部的属性还是可以改变的

```js
	const obj = {
        name : "LY",
        age : 21
    }
    obj.age=22;
    console.log(obj)
```

## const的属性增强写法

### 属性增强写法

以下拿ES5的语法来比较

```js
    const name="LY";
    const age=21;
    const height=175;

    const obj={
        name:name,
        age:age,
        height:height
    }
    console.log(obj)
```

ES6语法：

```js
    const name="LY";
    const age=21;
    const height=175;

    const obj={
        name,
        age,
        height
    }
    console.log(obj)
```

### 函数增强写法

ES5

```js
    const obj={
        run:function(){
          console.log('我在跑')
        }
    }
    console.log(obj)
```

ES6

```javascript
    const obj={
        run(){
        	console.log('我在跑')
        }
    }
    console.log(obj)
```

