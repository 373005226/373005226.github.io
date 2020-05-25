---
title: Webpack基本概念
summary: 由浅入深讲解webpack的作用和特点
date: 2020-5-19 16:00
updated: 2020-5-19 17:00
author: LY
categories:
    - 前端
tags:
    - webpack
---

> 本博客为观看慕课网webpack学习视频笔记，本文的部分截图、代码都来自于视频

##  webpack的引入

以前如果是要对html文件操作，如果是在一个js代码里面写的话

![index.js](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200519161625.png)

会造成代码十分的臃肿，修改起来十分的不易

那么对于这种情况，可以创建多个项目文件进行管理

也就是对于三个DOM操作的文件进行分离

比如:

```javascript
/* header.js */
function Header(){
	var header = document.createElement('div');
	header.innerText = 'header';
	dom.append(header);
}

/* siderbar.js */
function Header(){
	var header = document.createElement('div');
	siderbar.innerText = 'siderbar';
	dom.append(siderbar);
}

/* content.js */
function Header(){
	var header = document.createElement('div');
	content.innerText = 'content';
	dom.append(content);
}
```

三份代码都如上所示单独以构造函数的形式去编写，然后在index.js总文件里面调用

```javascript
var dom = document.getElementById('root')

new Header()
new Sidebar()
new Content()
```

 这样的做法是显而易见的方便的，只需要哪一块出了问题就去寻找哪一块的代码，整体的html文件就如下

![index.html](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200519162138.png)

但是这样会有新的问题

1. 新增了多个js文件，以前只需要加载一个js文件，现在要加载四个js文件，加载速度会变慢
2. 即便是创建了header、sidebar之类的，但是不能明眼看出这个类是来源于哪个文件，文件又在哪里，查询还要倒回去html文件来寻找路径
3. 很难去查错，加入content.js位置放错了，放在了index.js之下，那么浏览器是自上往下执行的，就不会识别到new Content()

如过是以如下形式写

![index.js](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200519162603.png)

那么也只是需要引入一个index.js文件即可，也可以解决如上的三个问题

 当然直接这么打开html文件肯定是报错的，因为浏览器不识别import

这个方式叫做ES Moudule模块引入方式

使用webpack只需要

```javascript
npm init

npm install webpack-cli --save-dev	

npm install webpack --save			

npx webpack index.js				#翻译index.js
```

打包出来后会生成一个dist目录，里面有个main.js

然后再引入main.js即可

但是这样直接打开的话是会报错的，因为ES Moudule仅仅是导入的话是不可以的，还需要一个导出



比如：

```javascript
/* header.js */
function Header(){
    var dom = document.createElement('root');
	var header = document.createElement('div');
	header.innerText = 'header';
	dom.append(header);
}
export default Header

/* siderbar.js */
function Siderbar(){
    var dom = document.createElement('root');
	var header = document.createElement('div');
	siderbar.innerText = 'siderbar';
	dom.append(siderbar);
}
export default Siderbar

/* content.js */
function Content(){
    var dom = document.createElement('root');
	var header = document.createElement('div');
	content.innerText = 'content';
	dom.append(content);
}
export default Content
```



```javascript
import Header from './header.js'
import Sidebar from './siderbar.js'
import Content from './content.js'

new Header()
new Sidebar()
new Content()
```

然后再执行`npx webpack index.js`

webpack识别的是ES Moudle模块引入方式，此外还有CommonJS、CMD、AMD等语法，webpack同样是可以识别的，比如

```javascript
/* header.js */
function Header(){
    var dom = document.createElement('root');
	var header = document.createElement('div');
	header.innerText = 'header';
	dom.append(header);
}
module.exports = Header

/* siderbar.js */
function Siderbar(){
    var dom = document.createElement('root');
	var header = document.createElement('div');
	siderbar.innerText = 'siderbar';
	dom.append(siderbar);
}
module.exports = Siderbar

/* content.js */
function Content(){
    var dom = document.createElement('root');
	var header = document.createElement('div');
	content.innerText = 'content';
	dom.append(content);
}
module.exports = Content
```



```javascript
/* index.js */
var Header = require('./header')
var Sidebar = require('./siderbar')
var Content = require('./content')

new Header()
new Sidebar()
new Content()
```



直接输入webpack 的话，使用的是全局变量，所以不建议在全局安装webpack，这样会导致两个依赖的webpack版本和全局的webpack版本混淆

那么如果是要使用依赖里面的webpack版本的话，那么可以使用命令npx webpack index.js（要打包的总文件）



## webpack配置项

### webpack.config.js配置项

当我们使用命令`npx webpack index.js`的时候，会默认打包成一个main.js文件，这个是默认的配置文件帮我们定义好的，这个是开发者为了方便我们，默认写好了很多配置，如果是我们想要自己去定义配置文件的时候，可以去定义一个名为`webpack.config.js`

比如说之前的命令都是`npx webpack index.js`，如果是不指定index.js的话就会报错。开发者自己知道打包哪个文件是没问题，但是别人也要拿这份代码打包的话也可能不知道要打包哪个文件，因为webpack不知道默认指定的是打包哪个文件，这个时候可以在配置文件定义

```javascript
const path = require('path')

module.exports = {
    entry: './index.js',
    output: {
        filename: "bundle.js",
        path: path.resolve(__dirname,'bundle')
    }
}

```

这里filename可以指定打包出来的名字，path可以指定打包出来的目录，但是这个目录不能写死，因为每个人的路径都不一样，所有可以定义一个path来获取开发者的路径，再把bundle结合

然后执行命令`npx webpack`，就可以看到文件打包在`bundle`文件夹下了

这里不指定参数，因为webpack自动寻找到了`webpack.config.js`这个文件，然后按照文件的定义去打包

`webpack.config.js`是默认识别的配置文件，但是如果要修改这个名字的话，那么命令行也要跟着修改

假如新的文件的名字为`webpackconfig.js`，那么新的命令应该是`npm webpack --config webpackconfig.js`

### 源文件配置项

比如说上面的header.js、sidebar.js、content.js、index.js等文件，不是拿来直接引用的，这类的源代码一般是放在src目录里面

把文件放进去后，再修改一下打包项目即可

```javascript
/* webpackconfig.js */
const path = require('path')

module.exports = {
    entry: './src/index.js',
    output: {
        filename: "bundle.js",
        path: path.resolve(__dirname,'dist')
    }
}
```

### 编写script

当然一般用的命令都是npm，而不是使用npx去使用，所以这里可以编写配置文件的脚本

编写package.json

```javascript
/* package.json */  
...
"scripts": {
    "bundle": "webpack"
  },
...
```

npm run执行的就是scripts里面的命令，里面编写要干什么即可

> Q：这里有个问题，如果是全局也安装了webpack，那么npm run的是哪个版本的webpack呢？
>
> A：实际上在scripts里面执行，会先到工程目录下的node_moudles里面寻找是否安装了webpack

### webpack-cli

这个文件的作用是让我们可以在命令行使用webpack命令，如果不安装这个，就无法使用webpack、npx之类的命令

## webpack打包输出

在webpack打包后会输出这么一大串字符

![webpack打包输出值](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200520140901.png)

图中Hash就是唯一的哈希值

version是打包的版本

Asset是输出的文件

Chunks是打包出来文件的ID

Chunk Names这个其实是package.json里面entry默认的字符串main

全写如下：

```javascript
const path = require('path')

module.exports = {
    entry: {
        main:'./src/index.js'
    },
    output: {
        filename: "bundle.js",
        path: path.resolve(__dirname,'dist')
    }
}
```

那下面的警告是什么呢，是指webpack打包没有指定打包的环境或者打包的模式

可以先看到打包出来的文件是全部代码都压缩成一行的文件

如果没有填写模式mode的话，默认的就是production，如果填写了mode字段

```javascript
const path = require('path')

module.exports = {
    mode: "production",
    entry: {
        main:'./src/index.js'
    },
    output: {
        filename: "bundle.js",
        path: path.resolve(__dirname,'dist')
    }
}
```

那么输出的结果就不会带着警告

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200520141810.png)

打包出来的文件还是一样的

 production就是压缩文件的意思，此外还有一个development，development的意思就是不压缩文件

可以看到打包出来的代码已经是排版好的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200520142010.png)

## Entry 与 Output 的基础配置

webpack中entry默认的打包文件是index.js，默认的输出文件是main.js

假如我要打包的文件有两个，那么输出只有一个是肯定会报错的

这里就可以把输出的文件的名字定义成一个占位符

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521170333.png)

可以看到这里是根据输入文件的定义名字来生成

但是在实际开发中，可能会把生成的index.html放到服务器，把js、css等静态文件放在cdn上

这个时候需要打包的html文件中可以自动生成一个cdn域名前缀

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521171217.png)

然后就会发现可以自动加上域名前缀

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521171238.png)

## SourceMap

SourceMap默认是开启的，所以要先关掉

先在index.js里面写一段错误的代码

```javascript
consele.log('success')
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521174406.png)

这里会告诉我们打包的main.js代码出错了，而不是告诉我们源文件的代码哪里报错

SourceMap是一个映射关系

这里我们知道main.js的第96行报错

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521174726.png)

实际上是index.js第一行代码出错的

如果我们要通过报错去知道我们源文件哪里出错的话，需要定义

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521174855.png)

再刷新一下就可以发现源代码的错误了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521174936.png)

这个配置文件可以在webpack官网文档找到

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521175040.png)



当然这样做的话会生成一个`main.map.js`文件，如果不需要这个文件的话可以使用`inline-source-map`来定义，然后`main.map.js`里面的内容就变成`main.js`里面去了

`cheap`是只管第几行，不精确到哪一个字符，并且没有`module`的都是只管业务代码，而不管`loader`之类的错误

`module`是管loader的代码的，默认是只管业务代码`index.js`的错误，如果加上`module`才算是管理所有的代码错误

建议使用`cheap-module-eval-source-map`，打包速度快，而且报错比较全面

而这里因为我们出来的mode一般都是production，而我们希望如果是在线上报错的话，我们也可以快速定位到源代码的错误点

这样配置就好了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521180513.png)

总而言之

如果是在开发环境

![开发环境配置](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521180625.png)

如果是在生产环境

![生产环境配置](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521180645.png)