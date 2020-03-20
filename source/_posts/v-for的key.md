---
title: v-for里面的:key作用
summary: :key在v-if当中的作用
date: 2019-12-19
author: LY
categories:
    - 前端
tags:
    - Vue
---

> 以下皆是由https://www.bilibili.com/video/av59594689?from=search&seid=2169751232721048853
>
> 视频中的老师操作代码和讲义总结而成

## 性能优化问题

v-for里面有个key属性，这里拿个案例来说明

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<!-- app.movie.splice(2,0,'异度空间')  -->
			<!-- 默认的插入算法是如果要在第二个插入新的值，就把新的值的ID改成2，第三个原来的ID改为原来的第二个值，依次替换下去，效率极低 -->
			<!-- 使用diff算法效率才是最高的，也就是在第二个插入的数据他的ID是7，时间复杂度就是O(1) -->
			<ul>
				<!-- key里面数据一定要保证唯一性，要与值一一对应 -->
				<li v-for="(item,index) in movie">
					{{item}}
				</li>
			</ul>
		</div>
		<script src="../js/vue.js"></script>
		<script>
			const app = new Vue({
				el: '#app',
				data: {
					movie: ['霸王别姬', '阿飞正传', '胭脂扣', '英雄本色', '新上海滩', '东邪西毒', '倩女幽魂'],
					cindex: 0
				}
			})
		</script>
	</body>
</html>

```

splice(2,0,'异度空间')的意思就是从下标为2后面插入异度空间

Vue渲染页面是通过一个虚拟DOM间接去去渲染页面的，大概的结构图如下：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191219220248.png)

**中间的就是虚拟DOM，如果要从下标2开始直接插入的话，那么从第二个开始的下标就会发生改变，新插入的值变成下标3，剩下的下标依次往下重新排序，这样的时间复杂度极大。**

最理想的排序应该是新插入的值以新的下标去排序，如图：

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20191219220526.png)





这样时间复杂度就为1，这个可以通过`diff`算法来实现这个目的，在代码中实现也就是要在每个for循环里面添加一个`:key`，这个值要为遍历循环的值，比如代码所示：

```html
<!DOCTYPE html>
<html>
	<head>
		<meta charset="utf-8">
		<title></title>
	</head>
	<body>
		<div id='app'>
			<!-- app.movie.splice(2,0,'异度空间')  -->
			<!-- 默认的插入算法是如果要在第二个插入新的值，就把新的值的ID改成2，第三个原来的ID改为原来的第二个值，依次替换下去，效率极低 -->
			<!-- 使用diff算法效率才是最高的，也就是在第二个插入的数据他的ID是7，时间复杂度就是O(1) -->
			<ul>
				<!-- key里面数据一定要保证唯一性，要与值一一对应 -->
				<li v-for="(item,index) in movie" :key="item">
					{{item}}
				</li>
			</ul>
		</div>
		<script src="../js/vue.js"></script>
		<script>
			const app = new Vue({
				el: '#app',
				data: {
					movie: ['霸王别姬', '阿飞正传', '胭脂扣', '英雄本色', '新上海滩', '东邪西毒', '倩女幽魂'],
					cindex: 0
				}
			})
		</script>
	</body>
</html>

```

`:key`里面的值要跟双括号里面的值一样，因为遍历的是item，所以`:key`的值也应该为item。这样就可以使用`diff`算法来优化代码了

