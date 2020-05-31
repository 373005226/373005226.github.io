---
title: ES5原型链实现类与继承
date: 2020-05-29 11:33:28
summary: 前端面试中-es5原型链实现类与继承的几种方法
categories:
    - 转载
tags:
    - 前端面试题
---

> 作者：东方既白233
> 链接：https://www.imooc.com/article/254600
> 来源：慕课网
> 本文原创发布于慕课网 ，转载请注明出处，谢谢合作

## 1.借助构造函数实现继承

```javascript
/**
* 借助构造函数实现继承
*/
function Parent1 () {
this.name = 'parent1';
}
Parent1.prototype.say = function () {
//Parent1 原型链上的方法并没有被 Child1 所继承
};
function Child1 () {
Parent1.call(this);//apply；改变函数运行的上下文（将父级的构造函数this指向子构造函数的实例上）
this.type = 'child1';
}
console.log(new Child1(), new Child1().say());//Uncaught TypeError: (intermediate value).say is not a function
//缺点就是父类原型链上的方法没有被child1继承了，因为只是调用了父类的构造函数，将属性赋值给子类的实例

```

## 2.借助原型链实现继承

```javascript
/**
* 借助原型链实现继承
*/
function Parent2() {
    this.name = 'parent2';
    this.play = [1, 2, 3];
}
function Child2() {
    this.type = 'child2';
}
Child2.prototype = new Parent2();//new Child2().__proto__===Child2.prototype
var s1 = new Child2();
var s2 = new Child2();
console.log(s1.play, s2.play);//(3) [1, 2, 3] (3) [1, 2, 3]
s1.play.push(4);
console.log(s1.play, s2.play);//(4) [1, 2, 3, 4] (4) [1, 2, 3, 4]
//缺点，虽然父类的实例成为了子类的原型对象，子类实例都能使用到父类的方法跟数据，但是每次实例化子类，都共用同一个父类实例
//改变原型对象会改变父类实例的数据，从而导致两边的原型对象数据都改变
```

## 3.组合

```javascript
function Parent3() {
    this.name = 'parent3';
    this.play = [1, 2, 3];
}
function Child3() {
    Parent3.call(this); //在子构造函数中执行父级构造函数
    this.type = 'child3';
}
Child3.prototype = new Parent3();
var s3 = new Child3();
var s4 = new Child3();
s3.play.push(4);
console.log(s3.play, s4.play); //(4) [1, 2, 3, 4] (3) [1, 2, 3]
// 缺点：在实例化1个子类的时候，父类的构造函数执行了2次）,同时，子类实例有了父类实例的数据，
// 子类的原型对象同时也是父类的示例，也有了一份一样的父类实例数据

```

## 4.组合优化一

```javascript
function Parent4() {
    this.name = 'parent4';
    this.play = [1, 2, 3];
}
function Child4() {
    Parent4.call(this);
    this.type = 'child4';
}
Child4.prototype = Parent4.prototype;//实例化1个子类的时候，父类的构造函数执行了1次
var s5 = new Child4();
var s6 = new Child4();
console.log(s5, s6);//Child4 {name: "parent4", play: Array(3), type: "child4"} Child4 {name: "parent4", play: Array(3), type: "child4"}
console.log(s5 instanceof Child4, s5 instanceof Parent4);//true true
console.log(s5.constructor);
//缺点：直接把父类的原型对象复制给子类的原型对象，
//那么打印出来的s5的constructor是父类的构造函数。是由父类构造的。
//直接使用s5 instanceof Parent4或者Child5出来的结果都是是true。
//因为无论instanceof的原理是判断实例化对象的_proto_是不是等于构造函数的prototype对象
//但是在这里CHild4.prototype跟Parent4.prototype指向的是同一块内存地址，所以肯定都是为true
```

## 5.组合优化二

```javascript
/**
* 组合继承的优化2
*/
function Parent5() {
    this.name = 'parent5';
    this.play = [1, 2, 3];
}
function Child5() {
    Parent5.call(this);
    this.type = 'child5';
}
Child5.prototype = Object.create(Parent5.prototype);
Child5.prototype.constructor = Child5;
var s7 = new Child5();
console.log(s7 instanceof Child5, s7 instanceof Parent5); //true true
s7.constructor
//通过 Object.create()创建一个中间对象{}，这个中间对象原型指向Parent.prototype
//再讲这个中间对象{}赋值给子类的prototpye,最后再修改子类的prototype的constructor为CHild5构造函数
//这种写法，设置s7的构造函数是Child而不是parent，虽然s7 instanceof child跟parent都未true
//但是s7.constructor=Child5了，而不是parent5
//证明s7是从child实例化的。
```

