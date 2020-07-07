---
title: apply、bind、call区别
date: 2020-07-07 17:23:41
summary: 讲解apply、bind、call区别
categories:
    - 前端
tags:
    - 前端面试题
    - JavaScript
    - 转载
---

> 本文转载于：https://blog.csdn.net/u010176097/article/details/80348447

call()、apply()、bind()是用来改变this的指向的。

一 举个例子

一个叫喵喵的猫喜欢吃鱼，一个叫汪汪的小狗喜欢啃骨头，用代码实现如下：

```js
var cat = {
  name:"喵喵",
  eatFish:function(param1,param2){
    console.log("吃鱼");
	console.log("this的指向=>");
	console.log(this);
	console.log(param1,param2);
  }
}
 
var dog = {
	name:"汪汪",
	eatBone:function(param1,param2){
		console.log("啃骨头");
		console.log("this的指向=>");
		console.log(this);
		console.log(param1,param2)
	}
}
```

有一天，小狗汪汪和喵喵共进午餐的时候，汪汪说自己想尝尝小鱼干的味道，但是因为有刺，喵喵就想了个办法，说自己先吃，完了喂给汪汪

```js
//第一种，用call方法
cat.eatFish.call(dog,"旺财-13岁","call");
```

```js
//第二种，用apply方法，参数不一样
cat.eatFish.apply(dog,["旺财-13岁","apply"]);
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200707172542)

这样，汪汪就吃到了美味的鱼干。可是汪汪每吨都想来点小鱼干，喵喵还要工作去捉老鼠，所以它们又想了一个办法，喵喵把吃鱼的方法教给汪汪。这样，每次汪汪就可以自己吃小鱼干了。

```js
var eatFishFun = cat.eatFish.bind(dog,"旺财-13岁","bind"); //返回的是方法
 
eatFishFun();
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200707172557)

bind()方法在这里再多说一下，bind的时候传的参数会预先传给返回的方法，调用方法时就不用再传参数了。

 

撇开上面的例子，看看一个特殊情况：

如果call()和apply()的第一个参数是null或者undefined，那么this的指向就是全局变量，在浏览器里就是window对象。


![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200707172610)

