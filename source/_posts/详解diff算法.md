---
title: 详解diff算法
date: 2020-07-02 10:43:02
summary: 详解VUe中的diff算法
categories:
    - 转载
tags:
    - Vue
    - JavaScript
---

> 本文转载于
>
> https://blog.csdn.net/M6i37JK/article/details/78140159?utm_medium=distribute.pc_relevant_right.none-task-blog-BlogCommendFromMachineLearnPai2-4.nonecase&depth_1-utm_source=distribute.pc_relevant_right.none-task-blog-BlogCommendFromMachineLearnPai2-4.nonecase
>
> 本文只做加以个人解释，文章为转载并非原创

> 作者简介：汪玉林，高级工程师，增值产品部前端Leader，目前团队负责手Q游戏中心、手Q游戏运营、手Q阅读等项目，有丰富的Web前端架构经验。 

## 一、前言

Vue的核心是双向绑定和虚拟DOM（下文我们简称为vdom），关于双向绑定可以参阅木琴的文章[《剖析Vue原理&实现双向绑定MVVM》](http://mp.weixin.qq.com/s?__biz=MzI3NTM1MjExMg==&mid=2247483789&idx=1&sn=e7297ec3443007015117637709f27521&scene=21#wechat_redirect)，vdom是树状结构，其节点为vnode，vnode和浏览器DOM中的Node一一对应，通过vnode的elm属性可以访问到对应的Node。

vdom因为是纯粹的JS对象，所以操作它会很高效，但是vdom的变更最终会转换成DOM操作，为了实现高效的DOM操作，一套高效的虚拟DOM diff算法显得很有必要。

Vue的diff算法是基于snabbdom改造过来的，感兴趣的朋友可以选择查阅。

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702104737.png)

这是一张很经典的图，出自《React’s diff algorithm》，Vue的diff算法也同样，**即仅在同级的vnode间做diff**，递归地进行同级vnode的diff，最终实现整个DOM树的更新。那同级vnode diff的细节又是怎样的呢？正是本文所要讲的。

## 二、例子

我们在下文中将使用这个简化的例子来讲述diff的过程

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702104819.png)

如上图的例子，更新前是1到10排列的Node列表，更新后是乱序排列的Node列表。罗列一下图中有以下几种类型的节点变化情况：

（1）、头部相同、尾部相同的节点：如1、10

（2）、头尾相同的节点：如2、9（处理完头部相同、尾部相同节点之后）

（3）、新增的节点：11

（4）、删除的节点：8

（5）、其他节点：3、4、5、6、7

## 三、简单的diff

简单的diff算法可以这样设计：

逐个遍历newVdom的节点，找到它在oldVdom中的位置，如果找到了就移动对应的DOM元素，如果没找到说明是新增节点，则新建一个节点插入。遍历完成之后如果oldVdom中还有没处理过的节点，则说明这些节点在newVdom中被删除了，删除它们即可。

仔细思考一下，几乎每一步都要做移动DOM的操作，这在DOM整体结构变化不大时的开销是很大的，实际上DOM变化不大的情况现实中经常发生，很多时候我们只需要变更某个节点的文本而已。

接下来我们看一下Vue的diff实现

## 四、Vue的diff实现

上图例子中我画上了oldStart+oldEnd，newStart+newEnd这样2对指针，分别对应oldVdom和newVdom的起点和终点。起止点之前的节点是待处理的节点，Vue不断对vnode进行处理同时移动指针直到其中任意一对起点和终点相遇。处理过的节点Vue会在oldVdom和newVdom中同时将它标记为已处理（标记方法后文中有介绍）。Vue通过以下措施来提升diff的性能。

### （一）、优先处理特殊场景

（1）、头部的同类型节点、尾部的同类型节点

这类节点更新前后位置没有发生变化，所以不用移动它们对应的DOM

（2）、头尾/尾头的同类型节点

这类节点位置很明确，不需要再花心思查找，直接移动DOM就好

处理了这些场景之后，**一方面一些不需要做移动的DOM得到快速处理，另一方面待处理节点变少，缩小了后续操作的处理范围，性能也得到提升**

### （二）、“原地复用”

“原地复用”是指Vue会尽可能复用DOM，尽可能不发生DOM的移动。**Vue在判断更新前后指针是否指向同一个节点，其实不要求它们真实引用同一个DOM节点，实际上它仅判断指向的是否是同类节点（比如2个不同的div，在DOM上它们是不一样的，但是它们属于同类节点），如果是同类节点，那么Vue会直接复用DOM，这样的好处是不需要移动DOM。**再看上面的实例，假如10个节点都是div，那么整个diff过程中就没有移动DOM的操作了。

“原地复用”在Vue的官方文档中有提到，虽然带来了好处，但是也会产生一些问题，朋友们可以复习一下

https://cn.vuejs.org/v2/guide/list.html#key

https://cn.vuejs.org/v2/guide/conditional.html#用-key-管理可复用的元素

## 五、按步解剖实例

### **（一）、整体视图**

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702104840.png)

先看一张整体视图，整个diff分两部分：

（1）、第一部分是一个循环，循环内部是一个分支逻辑，每次循环只会进入其中的一个分支，**每次循环会处理一个节点，处理之后将节点标记为已处理（oldVdom和newVdom都要进行标记，如果节点只出现在其中某一个vdom中，则另一个vdom中不需要进行标记），标记的方法有2种，当节点正好在vdom的指针处，移动指针将它排除到未处理列表之外即可，否则就要采用其他方法，Vue的做法是将节点设置为undefined。**

（2）、循环结束之后，可能newVdom或者oldVdom中还有未处理的节点，如果是newVdom中有未处理节点，则这些节点是新增节点，做新增处理。如果是oldVdom中有这类节点，则这些是需要删除的节点，相应在DOM树中删除之

**整个过程是逐步找到更新前后vdom的差异，然后将差异反应到DOM树上（也就是patch），特别要提一下Vue的patch是即时的，并不是打包所有修改最后一起操作DOM（React则是将更新放入队列后集中处理），朋友们会问这样做性能很差吧？实际上现代浏览器对这样的DOM操作做了优化，并无差别。**

### （二）、逐步解析

（1）、处理头部的同类型节点，即oldStart和newStart指向同类节点的情况，如下图中的节点1

这种情况下，将节点1的变更更新到DOM，然后对其进行标记，标记方法是oldStart和newStart后移1位即可，过程中不需要移动DOM（**更新DOM或许是要的，比如属性变更了，文本内容变更了等等**）

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702104852.png)

（2）、处理尾部的同类型节点，即oldEnd和newEnd指向同类节点的情况，如下图中的节点10

与情况（1）类似，这种情况下，将节点10的变更更新到DOM，然后oldEnd和newEnd前移1位进行标记，同样也不需要移动DOM

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702104912.png)

（3）、处理头尾/尾头的同类型节点，即oldStart和newEnd，以及oldEnd和newStart指向同类节点的情况，如下图中的节点2和节点9

先看节点2，其实是往后移了，移到哪里？移到oldEnd指向的节点（即节点9）后面，移动之后标记该节点，将oldStart后移1位，newEnd前移一位

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702105916.png)

操作结束之后情况如下图

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702105933.png)

同样地，节点9也是类似的处理，处理完之后成了下面这样

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702105949.png)

（4）、处理新增的节点

newStart来到了节点11的位置，在oldVdom中找不到节点11，说明它是新增的

那么就创建一个新的节点，插入DOM树，插到什么位置？插到oldStart指向的节点（即节点3）前面，然后将newStart后移1位标记为已处理（注意oldVdom中没有节点11，所以标记过程中它的指针不需要移动），处理之后如下图

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702110004.png)

（5）、处理更新的节点

经过第（4）步之后，newStart来到了节点7的位置，在oldVdom中能找到它而且不在指针位置（**查找oldVdom中oldStart到oldEnd区间内的节点**），说明它的位置移动了

那么需要在DOM树中移动它，移到哪里？移到oldStart指向的节点（即节点3）前面，**与此同时将节点标记为已处理，跟前面几种情况有点不同，newVdom中该节点在指针下，可以移动newStart进行标记，而在oldVdom中该节点不在指针处，所以采用设置为undefined的方式来标记（一定要标记吗？后面会提到）**

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702110020.png)

处理之后就成了下面这样

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702110040.png)

（6）、处理3、4、5、6节点

经过第（5）步处理之后，我们看到了令人欣慰的一幕，newStart和oldStart又指向了同一个节点（即都指向节点3），很简单，按照（1）中的做法只需移动指针即可，非常高效，3、4、5、6都如此处理，处理完之后如下图

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702110059.png)

（7）、处理需删除的节点

经过前6步处理之后（实际上前6步是循环进行的），朋友们看newStart跨过了newEnd，它们相遇啦！而这个时候，oldStart和oldEnd还没有相遇，说明这2个指针之间的节点（包括它们指向的节点，即上图中的节点7、节点8）是此次更新中被删掉的节点。

OK，那我们在DOM树中将它们删除，**再回到前面我们对节点7做了标记，为什么标记是必需的？标记的目的是告诉Vue它已经处理过了，是需要出现在新DOM中的节点，不要删除它，所以在这里只需删除节点8。**

**在应用中也可能会遇到oldVdom的起止点相遇了，但是newVdom的起止点没有相遇的情况，这个时候需要对newVdom中的未处理节点进行处理，这类节点属于更新中被加入的节点，需要将他们插入到DOM树中。**

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200702110117.png)

至此，整个diff过程结束了

Vue的diff算法与动态规划算法中的经典案例“计算a到b的最小编辑距离”看上去有些相似，实际完全不同，Vue的diff相对来说轻量很多，感兴趣的朋友可以查阅相关资料进行了解。

好啦，感谢你的阅读，希望能帮助你理解Vue的diff算法，在阅读过程中遇到的问题也欢迎一起交流！