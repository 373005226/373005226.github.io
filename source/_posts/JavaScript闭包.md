---
title: JavaScript闭包
date: 2020-07-10 16:46:54
summary: 讲解JavaScript中的闭包问题以及常见的面试题
categories:
    - 前端
tags:
    - Vue
    - JavaScript
    - 前端面试题
---

> 本文转载于：https://www.cnblogs.com/heyushuo/p/9975911.html
>
> 本文仅做加以解释

## 闭包是什么

高级程序设计三中:闭包是指有权访问另外一个函数作用域中的变量的函数.可以理解为(**能够读取其他函数内部变量的函数**)

**闭包的作用:** 正常函数执行完毕后,里面声明的变量被垃圾回收处理掉,但是闭包可以让作用域里的 变量,在函数执行完之后依旧保持没有被垃圾回收处理掉

## 闭包的实例

### 一般调用方法

一般的闭包调用方法如下：

```js
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

### 闭包实现计时器

如果是不适用闭包来实现的话

```js
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



```js
function addCount() {
    var count = 0;
    return function() {
        count = count + 1;
        console.log(count);
    };
}

var fun1 = addCount();
fun1(); //1
fun1(); //2
var fun2 = addCount();
fun2(); //1
fun2(); //2
```

## 闭包常见面试题  

### 1. for 循环中打印

```javascript
for (var i = 0; i < 4; i++) {
  setTimeout(function() {
    console.log(i);
  }, 300);
}
//4
//4
//4
//4
```

**原因**:js 执行的时候首先**会先执行主线程,异步相关的会存到异步队列里**,当主线程执行**完毕开始执行异步队列**, 主线程执行完毕后,此时 i 的值为 4,说以在执行异步队列的时候,打印出来的都是 4(这里需要大家对 **event loop 有所了解(js 的事件循环机制)**)

那么如何去修改上面的代码使其正常打印呢

#### 方法1：

```js
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
```

#### 方法2：

```js
for (var i = 0; i < 4; i++) {
  (function(i) {
    setTimeout(function() {
      console.log(i);
    }, 300);
  })(i);
}
// 大部分都认为方法一和方法二都是闭包,我认为方法一是闭包,而方法二是通过创建一个自执行函数,使变量存在这个自执行函数的作用域里
```

### 2.真实的获取多个元素并添加点击事件

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

