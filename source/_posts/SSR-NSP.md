---
title: CSR、SSR、NSR、ESR傻傻分不清楚，一文帮你理清前端渲染方案！
date: 2020-06-10 20:39:43
categories:
    - 前端
tags:
    - 前端面试题
    - JavaScript
    - 转载
---

> 以下文章来源于奶爸码农 ，作者奶爸码农
>
> 原文地址：https://mp.weixin.qq.com/s/1QPc2EZQs3jjPOK7-K6H7Q



## 引言

随着前端系统的越发复杂，前端的性能也受到越来越多的重视。Google也不断在推进新的性能相关指标，从原先的Performance API中的指标逐步演进成用户性能体验相关的指标。

![img](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200610205022)



对于用户而言，First Paint、First Meaningful Paint和TTI这几个指标可以直接影响到用户体验。关于前端性能优化有非常多的最佳实践可以参考，这篇文章会重点介绍前端渲染的方案及其优劣。

## CSR vs SSR



### CSR：Client Side Rendering

![img](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200610205013)**

浏览器(Client)渲染顾名思义就是所有的页面渲染、逻辑处理、页面路由、接口请求均是在浏览器中发生。其实，现代主流的前端框架均是这种渲染方式，这种渲染方式的好处在于实现了前后端架构分离，利于前后端职责分离，并且能够首次渲染迅速有效减少白屏时间。同时，CSR可以通过在打包编译阶段进行预渲染或者骨架屏生成，可以进一步提升首次渲染的用户体验。



但是由于和服务端会有多次交互（获取静态资源、获取数据），同时依赖浏览器进行渲染，在移动设备尤其是低配设备上，首屏时间和完全可交互时间是比较长的。



### SSR：Server Side Rendering

![img](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200610204600)

服务端渲染则是在服务端完成页面的渲染，在服务端完成页面模板、数据填充、页面渲染，然后将完整的HTML内容返回给到浏览器。由于所有的渲染工作都在服务端完成，因此网站的首屏时间和TTI都会表现比较好。



但是，渲染需要在服务端完成，并不能很好进行前后端职责分离，而且白屏时间也会比较长，同时，对于服务端的负载要求也会比较高。



### 基于Hydration的SSR和CSR融合

![img](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200610204642)

SSR和CSR均有各自的优点和缺点，因此，业界提出前后端渲染同构的方案来整合SSR和CSR。



整个页面的加载和刷新是通过服务端渲染来实现，在渲染生成的HTML中内嵌JavaScript和数据内容。通过这样的实现，可以达到和SSR相同的首屏时间，并且基于Hybration，可以生成前端的虚拟Dom，避免前端触发二次渲染。



SSR和CSR的页面渲染体验对比：

![img](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200610204653)



SSR和CSR不同阶段优劣对比：

![img](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200610204702)



### Serverless SSR

前端使用SSR进行渲染同构有一个非常明显的缺点：服务资源消耗和运维。通常情况下，同构SSR会采用Node服务，需要固定的服务器资源，并且服务器的运维对于前端同学也提出了很高的要求。



随着Serverless技术的出现，这些问题似乎得到了很好的解决。Serverless基于触发器调用云端部署的函数，然后调用后端服务。



![img](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200610204711)



对于前端同学而言，仅仅只需要实现云端函数的实现，对于服务器的运维管理完全不用关心，同时服务器会实现自动弹性伸缩，有效节省了机器资源。



当然，Serverless并不是万能的，服务器的冷启动目前依旧还是一个非常大的问题。



![img](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200610204719)



SSR在性能收益上是完胜CSR，配合Serverless，SSR的开发成本仅仅比CSR有略微的提高，因此，从收益成本来看，Serverless SSR已经有了较大的优势，可能会成为未来的趋势。

## NSR



![img](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200610204728)

UC浏览器在新闻feed流页面加载中采用了NSR（Native Side Rendering），首先在列表页中加载离线页面模板，通过Ajax预加载页面数据，通过Native渲染生成Html数据并且缓存在客户端。

![img](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200610204751)

![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)

NSR本质是分布式SSR，将服务器的渲染工作放在了一个个独立的移动设备中，实现了页面的预加载，同时又不会增加额外的服务器压力。

## ESR

![img](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200610204800)

ESR - Edge Side Rendering，方案的核心思想是，借助边缘计算的能力，将静态内容与动态内容以流式的方式，先后返回给用户。cdn 节点相比于 server，距离用户更近，有着更短的网络延时。在 cdn 节点上，将可缓存的页面静态部分，先快速返回给用户，同时在 cdn 节点上发起动态部分内容请求，并将动态内容在静态部分的响应流后，继续返回给用户。



![img](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200610204805)

从上面结果可以看出，在网速越慢的情况下，通过 cdn 流式渲染的最终主要元素出来的时间比原始 SSR 的方式出来得越早。这与实际推论也符合，因为网络越慢，静态资源加载时间越慢，对应的浏览器提前加载静态资源带来的效果也越明显。另外，不管在什么网络情况下，cdn 流式渲染方式的白屏时间要短很多。



## 总结



CSR、SSR、NSR、ESR，前端渲染方案如此纷繁复杂，其实每一种渲染方案都有其优势和劣势，一定要结合自身的业务需求，平衡好收益和成本选择适合自己的方案。

