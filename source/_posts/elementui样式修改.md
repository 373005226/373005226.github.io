---
title: elementui样式修改
date: 2020-06-24 17:41:33
categories:
    - 前端
tags:
    - Vue
---

> 本文的部分内容引用于https://www.cnblogs.com/my466879168/p/12091439.html，本文做案例翻译和加上个人理解

## 前言

因为在vue的文件里面，为了保护每个vue文件的样式不冲突，在样式里面是有个scoped的，所以导致在vue文件里面直接覆盖elementui的样式是不启作用的，这里讲解一下vue文件内改变全局elementui样式的方法

## 不使用scoped

这个方法很简单不把scored加上即可，但是这样会造成样式污染

```css
<style>
	.my{
		margin: 20px;
	}
	.my .el-input__inner{
		border-radius: 15px;/* 这个样式起效果 */
	}
</style>
<style scoped>
	.my .el-input__inner{
		border-radius: 30px; /* 这个样式不起效果 */
	}
</style>
```

## 使用deep样式穿透

```css
<style scoped>
	.my .el-input__inner{
		border-radius: 30px;/* 这个不起作用 */
	}
	.my /deep/ .el-input__inner{
		border-radius: 30px;/* 这个起作用 */
	}
</style>
```

## 使用>>>穿透

```css
<style scoped>
	.my .el-input__inner{
		border-radius: 30px;/* 这个不起作用 */
	}
	.my >>> .el-input__inner{
		border-radius: 30px;/* 这些起作用 */
		border: 1px solid #eceef2;
		outline: 0;
	}
</style>
```

## !important来增加权重

有些样式是行内样式权重比较高则需要使用上面的几种方法来保证可以修改样式并且添加上!important来增加权重

```vue
<el-input v-model="input" placeholder="请输入内容" style="width: 300px;"></el-input>
<style scoped>
	.my >>> .el-input__inner{
		border-radius: 30px;
		border: 1px solid #eceef2;
		outline: 0;
		width: 400px!important;
	}
</style>
```

> 本文转载于：https://www.cnblogs.com/my466879168/p/12091439.html