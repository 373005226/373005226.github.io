---
title: webpack之Tree Shaking
date: 2020-05-23 19:30:19
summary: 由浅入深讲解webpack的作用和特点
categories:
    - 前端
tags:
    - webpack
---

## Tree Shaking是什么

在打包的时候会出现如下字段

![image-20200523193505234](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200523193510.png)

这个的意思是在preset-env设置了`useBuiltIns`后，就已经达到了按需引入ES6语法的目的了，不用再去写`import "@babel/polyfill";`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200523193859.png)

意思是可以把多余的`import "@babel/polyfill";`代码去掉

这里先创建一个方法来演示

![项目结构](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200523194619.png)

```javascript
/* math.js */
export const add = (a,b) =>{
    console.log(a+b)
}

export const minus =(a,b) =>{
    console.log(a-b)
}

```

```javascript
/* index.js */
import {add} from "./math";

add(1,12)
```

然后使用webpack进行打包`npx webpack`

打包出来的结果会发现两个方法都有，虽然只引入了一个方法，但是会把math.js里面的所有代码都进行引入

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200523194821.png)

如果要实现按需引入的功能，在webpack2.0以后提供了tree Shaking的功能，可以中文理解为摇树的概念，意味着把没有用的东西都摇掉



## 怎么用Tree Shaking

`Tree Shaking`只支持`ES module`方式的引入

为什么呢,因为`ES module`引入的底层是通过静态文件的引入方式，`Commonjs`是通过动态引入的方式

现在配置的是`development`模式，默认是没有这个功能的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200523224829.png)

所以要添加一些配置

首先要在webpack配置中添加`usedExports`配置，如下图所示

![webpack.config.js](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200523225237.png)

在大部分的情况下，这样做已经是足够了的，只配置webpack已经可以做到业务代码按需打包了

但是有一些特殊情况，比如前面文章的pollyfill，因为pollyfill没有导出任何的内容，内部为了实现补充低版本浏览器不支持promise、map等方法，是在内部去定义了windows.promise之类的变量。但是如果只是如上的配置，用了tree Shaking，会发现怎么没有导出任何的内容，可能就直接把pollyfill忽略掉了。所以为了防止这种情况的出现，要对其做一个特殊的处理

此时为了防止上述情况的出现，所以要把pollyfill的引入的babel单独写进去，具体如下图所示

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200523230732.png)

sideEffects里面配置了`@babel/polly-fill`，那么在tree Shaking的时候就不会对他进行任何作用

如果不需要任何单独配置的话，那么就把sideEffects的值改为false

但是一般的话打包需要有个css文件，css文件是必须全部导入进去的

所以一般都是填上css

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200523231153.png)

再重新进行打包，可以看到这么一行解释

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200523231348.png)

英文的意思说只使用了add，也就是我只用的这个方法

但是实际上我没有用到的代码也还在，因为还是在`development`的模式下，即便是用了`tree Shaking`也不会把多余的文件去除掉，因为在开发的环境下需要做一些调试，如果直接删了就调试不了了

当然如果是在`production`的模式下，是会直接把代码删除掉的

并且在`production`模式下，连webpack配置文件的`optimization`都不用设置，但是`package.json`里面的`"sideEffects": false,`还是要设置的