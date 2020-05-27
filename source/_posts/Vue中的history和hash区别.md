---
title: Vue中的history和hash区别
date: 2020-05-27 13:26:58
summary: 介绍Vue中的history和hash区别、优缺点和使用场景
author: LY
categories:
    - 前端
tags:
    - Vue
---

## hash和history的区别

1. `hash`的话在地址栏会有一个`#`的符号。特点在于即便`hash`出现在`URL`中，但是不会包括在`http`请求中，对于后端没有影响，改变了`hash`也不会重新加载页面
2. `history`其实就是字面意思的可以记录历史页面，利用了 `HTML5 History Interface` 中新增的 `pushState()` 和 `replaceState()` 方法。(需要特定浏览器支持）这两个方法应用于浏览器的历史记录栈，在当前已有的 `back、forward、go` 的基础之上，它们提供了对历史记录进行修改的功能。只是当它们执行修改时，虽然改变了当前的 `URL`，但浏览器不会立即向后端发送请求。



`hash` 模式和 `history` 模式都属于浏览器自身的特性，`Vue-Router` 只是利用了这两个特性（通过调用浏览器提供的接口）来实现前端路由

## 使用场景

一般情景下，`hash`和`histoury`都可以，除非你更在意颜值，`#`符号夹杂在`URL`里看起来确实有些不太美丽。如果不想要很丑的`hash`，我们可以用路由的`history`模式，这种模式充分利用`history`。`pushState API`来完成`URL`跳转而无须重新加载页面。

调用`history.pushState()`相比于直接修改`hash` ，存在以下优势：
1：`pushState()`设置的新`URL`可以是与当前`URL`同源的任意`URL`；而`hash`只可修改#后面的部分，因此只能设置与当前`URL`同文档的URL；
2：`pushState()`设置的新`URL`可以与当前`URL`一模一样，这样也会把记录添加到栈中；而`hash`设置的新值必须与原来不一样才会触发动作将记录添加到栈中；
3：`pushState()`通过`stateObject`参数可以添加任意类型的数据到记录中；而`hash`只可添加短字符串；
4：`pushState()`可额外设置`title`属性供后续使用。





当然`history`也不是样样都好。`SPA`虽然在浏览器里游刃有余，单真要通过`URL`向后端发起`HTTP`请求时，两者的差异就来了。尤其在用户手动输入`URL`后回车，或者刷新（重启）浏览器的时候。
1：`hash` 模式下，仅`hash`符号之前的内容会被包含在请求中，如http://www.abc.com ，因此对于后端来说，即使没有做到对路由的全覆盖，也不会返回404错误。
2：`history`模式下，前端的`URL`必须和实际向后端发起请求的`URL`一致。如http://www.abc.com/book/id ，如果后端缺少对/book/id 的路由处理，将返回404错误。

