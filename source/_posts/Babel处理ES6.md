---
title: Babel处理ES6
date: 2020-05-22 16:53:52
summary: 浅谈Babel处理ES6的方式，介绍polyfill对全局和局部补充的方法以及babel在业务代码和类库之间打包的区别
author: LY
categories:
    - 前端
tags:
    - webpack
---

## Babel处理ES6

这里只在index.js里面使用ES6语法

![index.js](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522171116.png)

那么打包出来的文件

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522171023.png)

会原封不动的把ES6语法的代码放上去

在使用新版本的浏览器，比如谷歌浏览器之类的，是可以成功打印出来结果的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522171101.png)

因为新版本的浏览器与时俱进的兼容了ES6的语法

但如果是IE浏览器，或者是其他一些版本比较老的浏览器，可能就是直接报错，因为他们不支持ES6的代码

如果可以把ES6代码，转换成所有浏览器可识别的ES5

可在官网文档上找到详细的说明

![babeljs官网](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522182051.png)



![官网webpack说明文档](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522182201.png)

按照官网的配置即可

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522182749.png)

这一行的意思是如果js的代码在node_modules里面，那就不转换，因为里面的都是第三方的代码，并且第三方的代码早就做好了转换，没必要再去做一遍

配置好之后再重新打包，可看见代码全都被打包成ES5了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522204012.png)

## presets的额外配置

### useBuiltIns

上面这样远远不够，因为map、promise等函数在老版本的浏览器还是不存在的，要对低版本的浏览器把map、promise之类的方法做一个补充，使之可以识别

要使用到polyfill做一次补充



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522204325.png)

然后再index.js所有程序之前引入polyfil

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522204715.png)

根据文档配置好之后

可以看到打包的文件大小增加了

![不用polyfill](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522204811.png)

![用polyfill](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522204822.png)

这多的都是promise、map之类老版本不兼容的浏览器的语法，这个是所有的语法都在里面了

这个包其实很多都是程序中用不上的，我只用到了promise和map，我也只想要这两个语法的就行

实现这个目的也很简单，只需在webpack添加一个函数

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522205237.png)

如上图所示，把presets里面进行修改即可

这样就可以根据程序中用到的语法再进行打包即可

这个时候程序的大小就少了很多了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522205404.png)

### targets

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522210545.png)

这个代码的意思就是可以打包在浏览器的什么版本之下

如果chorm浏览器在67的版本之上本来就对ES6的兼容性很好了，这个时候就没必要再去把polyfill的额外的东西打包进去了

### 类库的打包

如上所示的配置可以应用在大部分情况下

但是有一些比如UI框架、组件库之类的，如果继续使用如上的方法就会污染全局变量，这个时候要用到的是`plugin-transform-runtime`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522211629.png)

`plugin-transform-runtime`可以很好的帮助解决`polyfill`污染全局变量的问题，因为他用的是闭包或者间接的帮助组件去引入内容，不存在全局污染的概念，所以写类库的时候用这个方法无疑是一个更好的方案

照常按照文档来来写即可，但是要注意里面的corejs必须配置为2，要把false改为2

```
      {
        "absoluteRuntime": false,
        "corejs": 2,
        "helpers": true,
        "regenerator": true,
        "useESModules": false,
        "version": "7.0.0-beta.0"
      }
```



### babel单独配置文件

如上代码，如果全部参数都配置上去的话，options里面的代码就变得特别的多，特别的长

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522212346.png)

这个时候可以把这些配置文件单出来

并且创建一个名为`.babelrc`的文件

```javascript
/* `.babelrc` */

{
    presets: [["@babel/preset-env", {
        useBuiltIns: "usage",
        targets: {
            chrome: "67"
        }
    }
    ]]
}

```

然后再把原来的options删除掉

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200522213046.png)

这样再打包结果还是一样，执行不会有任何的问题

