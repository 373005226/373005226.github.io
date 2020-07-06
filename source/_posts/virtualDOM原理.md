---
title: virtual DOM原理
date: 2020-07-02 11:07:40
summary: 详解Vue的virtual DOM原理
categories:
    - 转载
tags:
    - Vue
---

> 文章内有版权所在，不方便转载，所以只贴出链接
>
> [推荐文章](https://blog.csdn.net/dichu2296/article/details/102233749?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase)
>
> [参考文章1](https://blog.csdn.net/weixin_41223912/article/details/83385820?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-3.nonecase)
>
> [参考文章2](https://blog.csdn.net/dichu2296/article/details/102233749?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.nonecase)

## 原理简介

1，数据和模板相结合生成虚拟dom

2，虚拟dom转化成真实dom渲染到页面上

3，当数据发生改变时，新的数据和模板相结合会生成新的虚拟dom

4，新旧虚拟dom进行比对找差异

5，找到差异后根据差异改变dom

6，老的虚拟dom被清除，新的虚拟dom变成老的虚拟dom

## 1、定义：

虚拟DOM其实就是一棵以 `JavaScript 对象`( VNode 节点)作为基础的树，用对象属性来描述节点， 实际上它只是一层对真实 DOM 的抽象。最终可以通过一系列操作使这棵树映射到真实环境上。

相当于在js与DOM之间做了一个缓存，利用patch`（diff算法）`对比新旧虚拟DOM记录到一个对象中按需更新， 最后创建真实的DOM

## 2、虚拟dom原理流程

**`模板 ==> 渲染函数 ==> 虚拟DOM树 ==> 真实DOM`**

vuejs通过编译将模板（template）转成渲染函数（render），执行渲染函数可以得到一个虚拟节点树

在对 Model 进行操作的时候，会触发对应 Dep 中的 Watcher 对象。Watcher 对象会调用对应的 update 来修改视图。

图解：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702112853)

> **渲染函数：** 渲染函数是用来生成`Virtual DOM`的。
> **VNode虚拟节点：** 它可以代表一个真实的dom节点。通过 `createElement` 方法能将 `VNode` 渲染成 `dom` 节点。简单地说，vnode可以理解成`节点描述对象`，它描述了应该怎样去创建真实的DOM节点
> **patch(也叫做patching算法)：** 虚拟DOM最核心的部分，它可以将vNode渲染成真实的DOM，这个过程是`对比新旧虚拟节点`之间有哪些不同，然后根据对比结果找出需要更新的的节点进行更新

## 3、虚拟DOM好处

具备跨平台的优势–由于 Virtual DOM 是以 `JavaScript 对象`为基础而不依赖真实平台环境，所以使它具有了跨平台的能力，比如说浏览器平台、Weex、Node 等。
操作 DOM 慢，js运行效率高。我们可以将DOM对比操作放在`JS层`，提高效率。运用patching算法来计算出真正需要更新的节点，最大限度地`减少DOM操作`，从而显著提高性能。Virtual DOM 本质上就是在 JS 和 DOM 之间做了一个缓存。
提升渲染性能 Virtual DOM的优势不在于单次的操作，而是在大量、频繁的数据更新下，能够对视图进行`合理、高效的更新`
虚拟DOM就是为了`解决浏览器性能问题`而被设计出来的