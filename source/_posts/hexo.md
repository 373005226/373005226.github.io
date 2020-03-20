---
title: 使用hexo搭建个人博客并部署域名
author: LY
date: 2019-09-05
summary: Hexo可以帮助你快速搭建自己的个人网站，这里介绍Hexo的安装、使用、部署方法及如何给自己的博客自定义域名
categories:
    - 工具
tags:
    - Hexo
    - github
---


## 搭建环境准备

1. 安装node

   [https://nodejs.org/zh-cn/download/](https://nodejs.org/zh-cn/download/)

   (电脑是多少位的就下载多少位)

   安装成功之后在cmd输入命令

   ```
   node -v
   ```

   可以看到版本就安装成功

2. 安装Git

   [https://git-scm.com/downloads](https://git-scm.com/downloads)

   git配置百度查一查，这里不多说

3. 安装Hexo

   ```
   $ npm install -g hexo
   ```

   输入 `hexo -v`可以看到版本就安装成功

## 开始第一个博客

在命令行模式下进入个空文件夹，输入

```
hexo init
```

就开始创建hexo博客文件(可能需要一定时间)



然后输入命令安装博客所需要的包

```
npm install
```

此时博客就已经安装好了



输入命令开启你的第一个博客

```
hexo server
#或者hexo s
```

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/01.png)

如图所示，第一个博客就已经搭建好了



​	博客的主题是可以修改的，可以参照网上流传的主题，有很多很漂亮的主题可以使用，比如我这里使用的是[hexo-theme-matery-1.0.1](https://blinkfox.github.io/),主题可以参照这个网站

​	需要修改主题的话只需要把下载下来的主题文件,放到刚刚搭建的博客文件夹的theme文件夹下,然后修改博客根目录的_config.yml文件的theme字段修改成主题的名字即可

```
theme: hexo-theme-matery-1.0.1
```

这里我用的是上面提到的主题，效果如下

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/05.png)

### 添加markdown文章

接下来我要添加自己的字段

在根目录的`_posts`文件夹就是首页展示的文章

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/02.png)

直接在`_posts`文件夹添加markdown文件就可以在前端展示文章



### 添加标签tags页

(如果你的source目录没有`tags/index.md` 文件，那么就看以下步骤)

就使用命令添加`tags`文件夹

```
hexo new page "tags"
```

然后在`tags`文件夹下添加`index.md`文件，并且至少添加内容，

```
---
title: tags
date: 2019-09-4 18:37.25
type: "tags"
layout: "tags"
---
```

然后在`_posts`文件夹下的markdown文件的头部添加tags字段

例如：

```
---
title: hello world
author: LY
tags:
    - Hexo
    - github
---
```

tags最好用这种方法来写，多个标签就填多个



效果截图：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/06.png)



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/07.png)



### 添加分类categories页

(如果你的source目录没有`categories/index.md` 文件，那么就看以下步骤)

使用命令添加`categories`文件夹

```
hexo new page "categories"
```

然后在`categories`文件夹下添加`index.md`文件，并且至少添加内容，

```
---
title: categories
date: 2019-09-4 18:39.36
type: "categories"
layout: "categories"
---
```

然后在markdown文件的头部添加`categories`字段

例如

```
---
title: hello world
author: LY
tags:
    - Hexo
    - github
categories:
    - hexo
---
```



效果如图:

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/08.png)



![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/09.png)



### 新建关于我about页

(如果你的source目录没有`about/index.md` 文件，那么就看以下步骤)

使用命令添加`categories`文件夹

```
hexo new page "about"
```

然后在`about`文件夹下添加`index.md`文件，并且至少添加内容，

```
---
title: about
date: 2019-09-4 18:41.56
type: "about"
layout: "about"
---
```



效果截图:

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/11.png)



about页面的内容是可以通过`themes\hexo-theme-matery-1.0.1\_config.yml`来修改

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/12.png)



### 新建友情连接 friends 页（可选的）

如果在你的博客 `source` 目录下还没有 `friends/index.md` 文件，那么你就需要新建一个，命令如下：

```bash
hexo new page "friends"
```

然后在`friends `文件夹下添加`index.md`文件，并且至少添加内容，

```yaml
---
title: friends
date: 2019-09-4 18:45.36
type: "friends"
layout: "friends"
---
```

同时，在你的博客 `source` 目录下新建 `_data` 目录，在 `_data` 目录中新建 `friends.json` 文件，文件内容如下所示：

```json
[{
    "avatar": "http://image.luokangyuan.com/1_qq_27922023.jpg",
    "name": "码酱",
    "introduction": "我不是大佬，只是在追寻大佬的脚步",
    "url": "http://luokangyuan.com/",
    "title": "前去学习"
}, {
    "avatar": "http://image.luokangyuan.com/4027734.jpeg",
    "name": "闪烁之狐",
    "introduction": "编程界大佬，技术牛，人还特别好，不懂的都可以请教大佬",
    "url": "https://blinkfox.github.io/",
    "title": "前去学习"
}, {
    "avatar": "http://image.luokangyuan.com/avatar.jpg",
    "name": "ja_rome",
    "introduction": "平凡的脚步也可以走出伟大的行程",
    "url": "ttps://me.csdn.net/jlh912008548",
    "title": "前去学习"
}]
```



**文章还有一个description字段，是显示在主页的摘要，如果不填就是默认取内容的前70个字**


## Hexo-Theme-Matery主题的配置


Hexo-Theme-Matery主题的其他配置可以看他们的[主题文档](https://blinkfox.github.io/2018/09/28/qian-duan/hexo-bo-ke-zhu-ti-zhi-hexo-theme-matery-de-jie-shao/#toc-heading-1)


如果要对Hexo-Theme-Matery主题进行个性化的修改的话，可以参考这个[大神的文章](https://www.jianshu.com/p/2e6ae2123d57)

这里对上面主题修改的文章有说不明白的地方进行补充说明

### 自定义每日一言

如果你不喜欢文章里面的每日一言，想换一个比如[金山每日一言](https://api.ooopn.com/ciba/api.php?type=json)那些的话，就直接在主题文件里面的hexo-theme-matery-1.0.1/layout/index.exj文件添加个ajax代码
例如:

```
<script>
(function () {
        $.ajax({
            url: "https://api.ooopn.com/ciba/api.php?type=json",//向本地的json文件发送请求
            type: "GET",
            success: function (data) {
                ciba=data.ciba  #ciba就是你插入片段的id
                document.getElementById("ciba").innerHTML=ciba
            },
            error: function (error) {
                document.getElementById("ciba").innerHTML=error
            }
        })
    })();
</script>
```

### 评论的gittalk配置

gittalk主题配置说明没有说清楚，我这里补充一下，因为主题配置说推荐gittalk，所以我这里也拿gittalk来说明

首先要申请github授权，[申请地址](https://github.com/settings/applications/new)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200204131222.png)

- Application name：项目名，随便填
- Homepage URL：博客地址，也就是要访问的地址，我的是https://373005226.github.io
- Application description：应用描述，这个随便填
- Authorization callback URL:这个如果你有自己的域名就填自己的域名，没有就和上面的Homepage URL一样就好了



输入完后会给你一个Client ID和Client Secret



打开主题配置文件的_config.yml，找到gittalk的这一行

```
gitalk:
  enable: true		#默认是false，改为true才可以开启评论
  owner: 			#这里是你的github用户名'
  repo: 			#这里是你的博客地址
  oauth:
    clientId: 		#刚刚申请的id
    clientSecret: 	#刚刚申请的密码
  admin: 			#一般都是你的github用户名了
```



然后

```
hexo clean
hexo g
hexo d
```

上传到你的博客就可以看到评论地址搞定了

## hexo的常见命令

常见命令

```
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #部署到GitHub
hexo help  # 查看帮助
hexo version  #查看Hexo的版本
```

缩写：

```
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
```

组合命令：

```
hexo s -g #生成并本地预览
hexo d -g #生成并上传
```



## 将hexo博客部署到github上

首先要注册一个github账号，注册步骤就跳过了

新建一个仓库，名字格式最好如下:

账号名.github.io

比如我的账号是373005226，仓库的名字就是373005226.github.io

然后新建一个index.html文件，内容随便写，如图

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/13.png)



然后输入刚刚的仓库名就可以看到自己刚刚写的东西了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/14.png)



紧接着在博客源文件的_config.yml文件下修改如下字段:

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/15.png)

!!!格式要像我这样一样啊，或者填https://github.com/373005226/373005226.github.io.git

然后再命令行输入这三行代码




```
hexo clean
hexo g
hexo d
```


根据上述步骤就可以上传到github了



## 自定义域名

github可以绑定自己买的域名

域名可以去各大服务商买，这里我用的是阿里云

购买完域名后，进入域名解析

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/16.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/17.png)

这里的记录值好像是不一定的，记录值就是你打开cmd命令行，ping 你的账号.github.io

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/18.png)

先使用`hexo d`上传好代码到github上之后，然后在github上新建一个CNAME(没有后缀)文件，里面填写你购买的域名(加不加www都可以)
![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/19.png)

然后点击settings找到Github Pages填写自己的域名

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/21.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/hexo/22.png)

然后就可以绑定自己的域名了



## markdown图片的存储

写markdown有时候本地编辑没问题，但是上传到服务器的图片有时候是失效的，所以最好图片之类的媒体资源放到**腾讯云、七牛云**等服务商上面。因为typora黏贴文件默认是在本地的，所以`hexo -s`一看图片都是失效的，这里我推荐使用**腾讯云的OSS存储**作为图片存储方法，**腾讯云**有免费的6个月给你使用，而且过了6个月的费用还不贵。这里我介绍一款自动上传到腾讯云等平台的软件：`picGo`，下载地址： [https://github.com/Molunerfinn/PicGo/releases](https://github.com/Molunerfinn/PicGo/releases ) 

如图，直接拖动图片就可以立即上传了

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191025120326.png)

关于这个图床工具的使用，这篇博客有详细介绍： [https://www.jianshu.com/p/9d91355e8418](https://www.jianshu.com/p/9d91355e8418 ) 

