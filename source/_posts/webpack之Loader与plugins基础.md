---
title: webpack之Loader与plugins基础
date: 2020-05-20 14:27:21
summary: 略讲webpack之Loader与plugins的使用方式
categories:
    - 前端
tags:
    - webpack
---

## Loader的引入

webpack不只是可以转义浏览器不能识别的js代码，还可以打包图片等文件

如果直接引入图片的文件是会报错的，如下所示

```javascript
var test = require('./test01')
var img = require('./test.png')
new test()
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521103656.png)



webpack能识别js是因为默认是有转义js的规则，而图片没有，所以这里需要一个loader，也就是规则去告诉webpack怎么去做

```javascript
const path = require('path')

module.exports = {
    mode: "development",
    entry: {
        main:'./src/index.js'
    },
    module: {
        rules: [{
            test: /\.jpg$/,
            use: {loader: "file-loader"}
        }]
    },
    output: {
        filename: "bundle.js",
        path: path.resolve(__dirname,'dist')
    }
}

```

![image-20200521104944044](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521104944.png)

图中的test是正则匹配字符串，use是指定使用什么loader，这个file-loader是没有指定的

所以要用npm去下载`cnpm install file-loader -D`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521104859.png)

如图打包成功

打包出来会打包出两个文件

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521105038.png)

而这个png就是我刚刚的图片

具体的说明文档是官网介绍的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521105327.png)

由此可知，loader其实可以说是一种打包方案

这个可以同比于vue

```
import Header from './header.vue'
```

这个方法肯定是不识别的，所以肯定是需要用到特定的lodaer去执行

 ![vue官方文档](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521110127.png)

## Loader之图片篇

在上面的实验中可以看到图片的名字变为了一长串的字符串，然而图片还是那个图片

如果不想变动图片的名字怎么办，这里可以在配置中进行修改

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521110639.png)

这个意思就是打包后的名字，前缀还是跟以前一样，后缀也是跟以前一样

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521110723.png)

这个其实是叫`placeholders`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521110837.png)

如果是想把图片等资源都放在一个image的目录下，那么就可以用到`outputPath`

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521111041.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521111118.png)

打包出来的目录如下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521111130.png)

 再打开index.html文件其实还是可以运行的，无论路径是否改变

因为打包图片会进行三步

第一步会把图片打包移动到指定的文件夹

第二步是修改文件名

第三步是把文件相对于dist的路径返回给打包前的js文件的变量



打包图片使用的是file-loader，与之相类似的还有个叫做url-loader，url-loader可以执行file-loader的方法，而且还可以额外执行一些事情

先下载url-loader，`cnpm install url-loader -D`，然后修改文件

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521111724.png)

然后把原来所有的打包文件都删除，再执行一次打包

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521111829.png)

会发现原来的images没有了

 但是运行html文件还是可以照常的显示图片

这里看一下源码可以发现

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521112001.png)

再看下浏览器的图片地址

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521112036.png)

可以看到这个图片并不是地址，而是一个base64的内容

这样好处是可以省去一次http来请求图片

但是如果这个图片特别的大，加载js的时间就会变得很长

最佳的方案其实是如果图片小，那么就以base64的形式去存储，如果图片大就存储图片的路径，如需要实现这个功能的话，只需要在options里面再配置一个参数即可

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521112529.png)

这里我把2048修改成38KB，因为我的图片就是这么大

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521112621.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521112610.png)



html文件打开可以正常显示图片

如果我把limit改成204800

把limit设置成很大，就意味着要大于204800才能存储图片，否则是存储成base64的形式

url-loader对比于file-loader可以实现更多的功能

## Loader之样式篇

### css篇

 这里如果要对图片的大小样式进行修改

```css
/* index.css */
.avatar{
    width: 150px;
    height: 150px;
}
```

```javascript
/* index.js */
import avatar from './test.png'
import './index.css'

var img = new Image()
img.src = avatar
img.classList.add('avatar')

var root = document.getElementById('root')
root.append(img)
```

这样直接打包的话肯定是不能识别css的，因为还要使用loader，官网文档如下图所示

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521113819.png)

依照官网文档进行修改

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521114026.png)

再进行重新打包可以看到样式生效

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521114011.png)



css-loader的作用是把所有的css文件之间，如果是有关联的话，比如`@import './avatar.css'`,就全部结合并为一个css文件

style-loader是在css-loader得到内容后，再把内容件挂载在页面上

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521114529.png)





### less、scss篇

把原来的css文件删除修改成为less文件

```less
/* index.less */
body{
  .avatar{
    width: 150px;
    height: 150px;
  }
}
```

再把webpack匹配的表达式进行修改成scss

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521115230.png)

再进行打包发现是可以的，但是运行出来的结果发现样式没有改变，而且css的样式还是原本的scss语法，并没有翻译成浏览器可识别的css语法

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521115157.png)

去官网文档查看如何使用sass-loader

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521115452.png)

先进行下载

```bash
cnpm install sass-loader node-sass webpack --save-dev
```

然后配置一下sass-loader

再进行打包查看结果

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521115632.png)

发现样式已经发生改变

 

### 实现兼容浏览器前缀

比如在刚刚的图片样式里面，添加一个偏移的前缀

```css
body{
  .avatar{
    width: 150px;
    height: 150px;
    transform: translate(100px,100px);
  }
}
```

图片可以发生迁移的

但是我们一般这么写的时候都会要求一个前缀来兼容浏览器

比如-webkit-transform，但是这样写会很麻烦，要一个一个加前缀

这个是有个loader去自动添加厂商前缀的功能：postcss-loader

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521143248.png)

依照文档的说法，要创建一个postcss-config.js文件

```javascript
/* postcss-config.js */

module.exports = {
    plugins: [
        require('autoprefixer')({
            "browsers": [
                "defaults",
                "not ie < 11",
                "last 2 versions",
                "> 1%",
                "iOS 7",
                "last 3 iOS versions"
            ]
        })
    ]
};
```

然后webpack.config.js里面也要添加postcss.config.js

```javascript
use: ['style-loader', 'css-loader','sass-loader','postcss-loader']
```

这个autoprefixer是一个插件

然后重新打包可以看到会自动帮我们添加了前缀

![Google浏览器](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521150706.png)

### css-loader的拓展

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521151030.png)

在很多脚手架工具里面都可以看到单独配置的这一类代码

这个实际上是这个问题的

比如我在`index.scss`里面使用一条命令`@import './other.scss'`

在一个scss里面再去引入其他的scss文件

 有可能引入的other.scss就不会去走postcss-loader和scss-loader了，而是直接去走css-loader

上图代码的意思就是如果是引入其他的scss之类的文件，引入之前也要去走两个loader

### css打包的模块化

这里先创建一个其他的js文件createAvatar.js

```javascript
/* createAvatar.js */

import avatar from "./test.png";

function createAvatar() {
    var img = new Image()
    img.src = avatar
    img.classList.add('avatar')

    var root = document.getElementById('root')
    root.append(img)
}
export default createAvatar
```

```javascript
/* index.js */

import './index.scss'
import avatar from "./test.png";
import createAvatar from "./createAvatar";

createAvatar()
var img = new Image()
img.src = avatar
img.classList.add('avatar')

var root = document.getElementById('root')
root.append(img)
```

创建两个一模一样功能的文件，可以看到两个的样式是一样的

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521153931.png)

偏移量都是一样的，这个说明了在index.scss里面引入的index.scss是全局变量

可是我们想要实现的是一个css文件，只在他当前引入的文件里面有效，就类似于vue里面定义的css样式，只在当前的vue文件有效

这个就是css的模块化，需要做到这个功能，需要先在webpack文件中修改

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521154215.png)



然后在使用的时候，也需要改一下方法

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521154350.png)

引入使用的时候也是引入style.avatar

然后重新打包

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521154435.png)

可以发现两个样式都是不一样的了

以上的所有webpack打包，都在webpack有教程

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521160130.png)

## plugins

### HtmlWebpackPlugin

顾名思义plugins 就是插件的意思，比如我们打包的时候dist目录下是没有index.hml的，要我们去手动的生成，这个时候可以用插件帮我们去做这件事

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521160500.png)

以如上的方式去修改webpack文件，即可自动生成html文件

但是直接创建的话，只会导入js文件之类的，比如如下类型，就不会自动的生成

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521163409.png)

我在js里面绑定的是root的这个id，但是这个不会自动生成，我们可以自定义一个模板，然后接下来的打包的html文件都是按照这个模板去生成

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200521163529.png)

### clean-webpack-plugin

这个插件并不是官方的插件，所以并不能在官网找到

在下载完成后`cnpm install clean-webpack-plugin -D`

```javascript
plugins: [new HtmlWebpackPlugin(),new Cleanwebpackplugin(['dist'])]
```

具体生命周期需要看插件的官方文档
