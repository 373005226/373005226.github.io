---
title: JS继承的实现方法
date: 2020-07-02 16:20:53
summary: 详JS的ES5和ES6继承方法
categories:
    - 前端
tags:
    - JavaScript
    - ES6语法
    - 转载
    - 前端面试题
---

> 本文转载于：https://www.cnblogs.com/cythia/p/11175343.html

## ES5的继承方式

### 方式一:借助构造函数实现继承

这里使用的原理就是在Child里面,把Parent的this指向改为是Child的this指向,从而实现继承

```js
function Parent(name) {
	this.name = name;
}
Parent.prototype.saiHi = function() {
	console.log("hello")
}

function Child(name, age, gender) {
	Parent.call(this, name)
	this.age = age;
	this.gender = gender;
}
let child = new Child("王磊", 20, "男")
console.log(child.name); // 王磊
child.sayHi(); // Uncaught TypeError:child.sayHi is not a function
```

**缺点:只能解决属性的继承,使用属性的值不重复,但是父级类别的方法不能继承**

### 方式二:借助原型链实现继承

第二种方式就是把Child的原型改为是Parent的实例,从而实现继承

```js
function Parent(name, gender) {
	this.name = name;
	this.gender = gender;
	this.list = [1, 2, 3]
}
Parent.prototype.eat = function() {
	console.log("晚餐时间到")
}

function Child(age) {
	this.age = age;
}
Child.prototype = new Parent("李白", "男");
var child = new Child(20);
var child2 = new Child(30);
child.eat();
console.log(child.list, child2.list); // [1,2,3] [1,2,3]
child.list.push(4)
console.log(child.list); // [1,2,3,4]        
console.log(child2.list); // [1,2,3,4]
```

**缺点:因为Child的原型对象都是New Parent,所以实例化出来的对象的属性都是一样的,而且Parent上面的引用类型只要有一个实例对象修改了,其他也会跟着修改.因为他们原型对象都是共用的**

### 方式三:组合型

方式三的话是结合了方式一和方式二来实现继承

```js
function Person(school) {
	this.school = school;
}
Person.prototype.skill = function() {
	console.log("学习");
}

function Student(school, name, age, gender) {
	Parent.call(this, school);
	this.name = name;
	this.age = age;
	this.gender = gender;
}
Student.prototype = Person.prototype;
let student = new Student("广铁一中", "王菲菲", 14, "女");
console.log(Student.prototype === Person.prototype)
console.log(student.constructor)
```

**缺点:父类的原型对象调用了两次,没有必要,而且student实例的构造函数是来自于Person**

### 方式四:组合方式优化

```js
function Parent(name, play) {
	this.name = name;
	this.play = play;
}

function Child(name, play, age) {
	Parent.call(this, name, play);
	this.age = age;
}
Child.prototype = Parent.prototype;
let child = new Child("张三", "玩", 20);
let child2 = new Child("李四", "吃", 10)
console.log(child, child2)
console.log(child.prototype === child2.prototype);
true
console.log(child.constructor); // 构造函数指向的是Parent
```

**缺点:child实例的构造函数来自于Parent**

### 方式五: 组方式优化

只是这种方式的话,你必须得理解Object.create()方法的使用,他创建的对象是在原型上面的

```js
function Parent(name, play) {
	this.name = name;
	this.play = play;
}

function Child(name, play, age) {
	Parent.call(this, name, play);
	this.age = age;
}
Child.prototype = Object.create(Parent.prototype); // 隔离了父类和子类的构造函数,父类的添加到了__proto__属性上
Child.prototype.constructor = Child
let child = new Child("张三", "玩", 20);
let child2 = new Child("李四", "吃", 10)

console.log(child.constructor)
```

