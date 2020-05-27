---
title: Vue生命周期详解
date: 2020-05-27 16:48:33
summary: 讲解Vue的生命手气
categories:
    - 转载
tags:
    - Vue
---

> 本文章大部分引用于https://www.cnblogs.com/chenyablog/p/9095677.html

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200527165603.png"/>

<img src="https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200527165715.png"/>

**注意：**

1. **created阶段的ajax请求与mounted请求的区别：前者页面视图未出现，如果请求信息过多，页面会长时间处于白屏状态**
2. **mounted 不会承诺所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染**



