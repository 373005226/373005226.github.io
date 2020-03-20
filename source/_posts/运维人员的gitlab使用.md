---
title: 运维人员的gitlab基本操作
author: LY
summary: 这里讲解gitlab的管理员创建项目、添加开发人员和开发负责人的信息、开发人员上传分支、负责人合并分支等操作
date: 2019-09-22
categories:
    - 自动化运维
tags:
    - gitlab
---

>  本篇讲解了运维人员在后端的基本使用：创建开发项目、定义开发总监、开发人员等

## 管理员的操作
点击上面的扳手图标可以进入后台管理界面
![](https://img-blog.csdnimg.cn/20190922092307422.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)

## 查看日志
点击左边的monitoring标签
![](https://img-blog.csdnimg.cn/20190922092435431.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
第一个system info是服务器的资源使用情况
![](https://img-blog.csdnimg.cn/20190922092634854.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
logs就是gitlab的应用日志和使用日志

## 创建账号
以下两种方法都可以添加账号
点击overview的new user按钮，可以为使用者创建账号
![](https://img-blog.csdnimg.cn/20190922093118594.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)

![](https://img-blog.csdnimg.cn/20190922093433289.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
点击右上角的new user按钮就可以给项目主负责人创建账号

## 给项目添加人员
我这里原先就已经创建好了一个名为test的仓库
我这里给test仓库添加人员
点击project1
![](https://img-blog.csdnimg.cn/20190922093735602.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
再点击你需要操作的仓库
![](https://img-blog.csdnimg.cn/20190922093800821.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
然后点击manage access
![](https://img-blog.csdnimg.cn/20190922093832764.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
这里你要给项目添加的是什么权限可以自定义
![](https://img-blog.csdnimg.cn/20190922094119996.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
master就是有合并分支的权限，developer就是可以用自己的分支写代码的权限
![](https://img-blog.csdnimg.cn/20190922094248435.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)

## 给人员修改自己的密码
点击扳手按钮重回管理界面，点击user
![](https://img-blog.csdnimg.cn/20190922094452932.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
这里就可以给人员信息进行修改了
![](https://img-blog.csdnimg.cn/20190922094506428.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
可以修改密码
![](https://img-blog.csdnimg.cn/20190922094616363.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)

## 模拟企业开发者和负责人的使用流程
这里我不用git原始命令操作，因为比较麻烦，我贪方便就用sourcetree操作，原理是一样的

开发者使用自己的分支来编写代码

黏贴这个地址
![](https://img-blog.csdnimg.cn/20190922095222895.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
我随便写了一个test文件，我上传上去的时候会让我给账号密码，就是你刚刚设置的devloper账号密码
![](https://img-blog.csdnimg.cn/20190922104059340.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
这里就可以显示我刚刚上传的分支了
![](https://img-blog.csdnimg.cn/20190922104108895.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
然后点右上角蓝色的create merge request
![](https://img-blog.csdnimg.cn/20190922104243370.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
assign是项目负责人的账号就行了，其他的你随便填
然后用同样的方法打开负责人账号lead
![](https://img-blog.csdnimg.cn/20190922104425940.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
右上角有个橙色的消息，点开
![](https://img-blog.csdnimg.cn/20190922104503516.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
就可以看到开发者提交的申请
![](https://img-blog.csdnimg.cn/20190922104537582.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)
点击merge就可以合并分支
这里还可以给开发者回复
![](https://img-blog.csdnimg.cn/20190922104737164.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)

重新返回项目，就可以看到你刚刚操作的文件合并到主分支了
![](https://img-blog.csdnimg.cn/20190922104839441.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zOTM3NzI3Mw==,size_16,color_FFFFFF,t_70)