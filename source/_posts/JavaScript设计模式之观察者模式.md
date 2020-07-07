---
title: JavaScript设计模式之观察者模式
date: 2020-06-07 17:53:23
summary: 详细讲解JavaScript事件委托
categories:
    - 设计模式
tags:
    - 前端面试题
    - JavaScript设计模式
    - JavaScript
    - 转载
---

> 本文引用于[https://srtian96.gitee.io/blog/2018/05/21/JavaScript%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B9%8B%E8%A7%82%E5%AF%9F%E8%80%85%E6%A8%A1%E5%BC%8F/](https://srtian96.gitee.io/blog/2018/05/21/JavaScript设计模式之观察者模式/)

### 前言

准备研究一下MVVM的一些东西，由于MVVM运用了观察者模式的思想，因此翻开了《JavaScript设计模式与开发实践》一书，将观察者模式学习了一遍，顺便有对一些常用的设计模式进行一些了解，但由于平时很多设计模式并没有用过甚至没有接触过，发现一个很有意思的东西，JavaScript还正是厉害，将很多常用的设计模式都封装成了API，比如ES6出来的迭代器和代理。但总的来说还是有很多不能理解的地方，真是前途漫漫，还需努力啊。

### 一、什么是观察者模式

观察者模式又叫做发布—订阅模式，是我们最常用的设计模式之一（这两种模式其实还是有一定区别，但在《JavaScript设计模式与开发实践》一书中有这么说过，但在经历了一些学习后发现这两种还是存在一定的区别的，具体区别可以谷歌~）。它定义对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都将得到通知和更新。观察者模式提供了一个订阅模型，其中对象订阅事件并在发生时得到通知，这种模式是事件驱动的编程基石，它有利益于良好的面向对象的设计。

看了上面的这段描述，可能还是不懂什么是观察者模式。我们还可以来看看生活中的观察者模式：现在房价这么高，你肯定是想要早点买房，但你看好的楼盘还没开盘，因此你就将你的电话留给售楼小姐，一旦楼盘推出就让她打电话给你。主动权在售楼方，而你只需要提供一个联系方式就行了这样你就不用担心楼盘出来你不知道了，也不需要每天都打电话去问楼盘推出了没。

自己的一些理解：学了Rx.js后对观察者模式有了更为深刻的理解，观察者模式本质上就是将数据的产生于数据的处理逻辑相分离开来，被观察者模式负责数据的产生，观察者则负责处理数据，即产生的数据流与处理数据的逻辑分离开来。（补充）

### 二、观察者模式的使用场景

#### 2.1 DOM事件

实际上，只要我们曾经在DOM节点上面绑定过事件函数，那我们就使用过观察者模式，应为JS和DOM之间就是实现了一种观察者模式。

```javascript
document.body.addEventListener("click", function() {
    alert("Hello World")
}，false )
document.body.click() //模拟用户点击
```

在上面的代码中，需要监听用户点击 document.body 的动作，但是我们是没办法预知用户将在什么时候点击的。因此我们订阅了 document.body 的 click 事件，当 body 节点被点击时，body 节点便会向订阅者发布 “Hello World” 消息。

#### 2.2自定义事件

除了DOM事件外，我们还可以实现一些自定义事件，这种依靠自定义时间完成的观察者模式可以用于任何的JavaScript代码中。

示例：

```javascript
const event = {
    clientList: [],
    listen: function(key, fn) {
        if (this.clientListen[key]) {
            this.clientList[key] = []
        }
        this.clientList[key].push(fn)
    },
    trigger: function() {
        const key = Array.prototype.shift.call(arguments)
        const fns = this.clientList[key]
        if (!fns || fns.length === 0 ) {
            return false
        }
        for (let i = 0, fn ;fn = fns[i++];) {
            fn.apply(this, arguments)
        }
    },
    remove : function(key , fn) {
        const fns = this.clientList[key]
        if (!fns) {
            return false
        }
        if (!fn) {
            fns && (fns.length = 0)
        } else {
            for (let l = fns.length - 1; l>=0; l--) {
                const _fn = fns[l]
                if ( _fn === fn) {
                    fns.splice(l, 1)
                }
            }
        }
}

const installEvent = obj => {
    for (let i in event) {
        obj[i] = event[i]
    }
}
```

然后我们就能增加发布和订阅功能了：

```javascript
const events = {}
installEvent(events)

// 订阅信息
events.listen('newMessage', fn1 = say => {
    console.log('say:' + say)
})

// 发布信息
events.trigger('newMessage', "Hello world")

//移除订阅
events.remove('newMessage', fn1)
```

### 三、观察者模式的不足

观察者模式的有点非常明显：一是时间上的解耦，而是对象之间的解耦。既可用于异步编程中，也可以用帮助我们完成更松耦合的代码编写。但它仍然有所不足：

- 创建订阅者本身要消耗一定的时间和内存
- 当订阅一个消息时，也许此消息并没有发生，但这个订阅者会始终存在内存中。
- 观察者模式弱化了对象之间的联系，这本是好事情，但如果过度使用，对象与对象之间的联系也会被隐藏的很深，会导致项目的难以跟踪维护和理解。