---
title: package-lock.json 冲突解决
date: 2020-06-16 21:20:04
summary: 解决github上的漏洞警告
author: LY
categories:
    - 工具
tags:
    - github
---

> 建议读文章[npm的package.json和package-lock.json更新策略](https://blog.csdn.net/weixin_43820866/article/details/105232066)

## 漏洞出现原因

github上的前端项目通常会出现这种依赖警告

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200616212136.png)

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200616212603.png)

这个是因为在开发过程中使用的是cnpm，cnpm是不会修改package-lock.json里面的依赖

cnpm install不受package-lock.json影响，只会根据package.json进行下载

cnpm i xxx@xxx不会跟新到package-lock.json中去而npm会

## 解决办法

第一种办法就是一个包一个包的的npm install去更新，这样package-lock.json的依赖也会更新

第二种办法就是npm install prune ddp，这样就可以把package.json的包版本跟package-lock.json的依赖同步，然后如果github上还是出现高危漏洞警告就可以一个个npm install去更细包了