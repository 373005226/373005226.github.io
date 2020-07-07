---
title: JavaScript事件委托
date: 2020-06-06 13:50:55
summary: 详细讲解JavaScript事件委托
categories:
    - 前端
tags:
    - 前端面试题
    - JavaScript
    - 转载
---

> 作者：小进进不将就
> 链接：https://www.jianshu.com/p/1cfda28abc79
> 来源：简书
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

## **1、什么是事件委托？**

 事件委托就是利用冒泡的原理，将事件加到 父元素 或 祖先元素上，触发执行效果。

例：在 document 中绑定 click 事件，当 click 的对象为 按钮 btn 时，弹框显示 按钮 btn的值

```html
<body>

<button id="btn" value="嘿嘿">哈哈</button>

<script>
  window.onload=function () {

    let btn=document.getElementById("btn")
    document.onclick=function (e) {
      // console.log(e.target)
      if(e.target===btn){
        alert(btn.value)
      }
    }

  }
</script>
</body>
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200606191737)

## **2、事件委托的优点**

 （1）可以提高 `JS` 性能

 例：在 `ul` 上创建 100 个 `li`，点击每个 `li` 都要弹框显示该 `li` 的 `value`

传统写法：在每个 `li` 上都绑定 `onclick` 事件，并触发 `alert` 事件

事件委托：在 `document` 上绑定 `onclick` 事件，当触发 `onclick` 事件后，判断是不是 `<li>` 标签，如果是，则 `alert ` 一下`<li>`标签的值。

（2）可以动态添加 `DOM` 元素，不需要因为元素的变动而修改事件绑定。

## **3、事件委托需要注意的地方**

 事件委托绑定的元素，最好是被监听元素的父元素，如 `<li>` 上面的 `<ul>`，`<tr>`上面的 `<table>` 等。

因为 事件冒泡 的过程也要耗时，越接近顶层，事件的「事件传播链」就越长，也就越耗时。

## **4、事件冒泡 和 事件捕获**

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200606191746)

如图，事件冒泡是从子元素向父元素传递事件，一层一层，**事件委托就是利用了冒泡原理**；事件捕获是从 DOM 最高层对象一层一层往子元素传播。