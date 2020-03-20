---
title: redis初步认识
author: LY
date: 2019-10-13
img: https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/redis.jpg
summary: Redis的初步认识，了解其特性、应用场景、常用命令等
categories:
    - 数据库
tags:
    - Redis
---



## Redis的五大数据结构

- String
- hash
- list
- set
- zset

## Redis的特性

1. 速度快：因为redis是保存在内存当中的
2. 持久化：Redis的数据保存在内存中，一般的保存在内存中突然断电内存就会丢失，所以redis在保存数据的时候会异步的保存在硬盘中
3. 支持多种数据结构：Redis支持(字符串、列表、哈希、集合、有序集合)这五种数据结构，在新版本中也支持BitMaps(位图，使用很小的内存来实现高效的存储)、HyperLoglog(超小内存唯一计数，即使用12K)、GEO(地理信息定位 )这三种衍生的数据结构
4. 支持多种语言：基本上现在的主流语言都是支持redis的
5. 功能丰富：支持发布订阅、Lua脚本、事物，pipeline(提高客服端的并发效率)
6. 简单性：Redis不依赖任何外部库和使用的是单线程模型
7. 主从复制：主服务器的数据可以同步到从服务器上，给高可用和分布式提供了一个很好的基础
8. 高可用、分布式：Redis单单靠主从复制是不能进行高可用分布式的，所以Redis2.8版本提供了Redis-Sentinel来支持高可用，Redis3.0版本提供了Redis-Cluster来支持分布式

## Redis的应用场景

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Redis应用场景十分的广泛，缓存(比如在网站中可以使用Redis缓存来代替session和cookie)、计数器(比如微博的转发、评论数)、消息队列(比如Celery和redis结合来使用异步任务)、排行榜、实时系统(垃圾邮件过滤功能)、社交系统(比如youtube那些社交网站的共同关注数、粉丝数)等，大多数企业都使用了Redis

## Redis的常用命令

- `keys`：这个一般不在生产环境中使用，其一是因为key是一个很重的命令，时间复杂度为O(n)如果有上万个键值对一下子遍历出来会很慢，这个可以在不在生产环境中使用的从节点使用比较重的命令，不会对生产环境产生危害即可
  - `keys *`：查看所有的键
  - `keys [pattern]`：keys支持正则表达式，比如`keys he*`,就可以选择出所有he开头的key
- `dbsize`：计算keys的总数，这个是可以在生产环境中使用的，因为这个不是遍历所有的表，而是内置的一个计数器，所以时间复杂度是O(1)
- `exists keys`：判断一个key是否存在，存在返回1，不存在返回0
- `del key`：删除制定的key-value，成功删除返回1，不成功返回0
- `expire key seconds`：(设置过期时间)，一般用于网站记录用户缓存，seconds就是秒数，seconds为30就是30秒后过期
  - `ttl key`：查看key的过期时间，返回值为-2代表key不存在了，-1表示没有过期时间
  - `persist key`：去除key过期时间
- `type key`：查看key的数据类型
- `select number`：选择使用第number个数据库
- `info`：查看客户端的所有信息



| 命令               | 时间复杂度 |
| ------------------ | ---------- |
| keys               | O(n)       |
| dbsize             | O(1)       |
| exists keys        | O(1)       |
| del key            | O(1)       |
| expire key seconds | O(1)       |
| type key           | O(1)       |



## Redis的数据结构和内部编码

![](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/1.png)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;每一个数据结构都有自己的内部编码，在开发过程中，如果一些元素比较小的时候可以通过空间换时间的操作，这些得看开发中的具体要求和操作来定制

## Redis 的单线程操作

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Redis的单线程就像一个队列，每一个瞬间只能执行一个命令，所以在开发中，一定要注意不要轻易使用很长时间的命令，不然后面的命令都会阻塞在那

Redis单线程还这么快的原因跟node.js部分很像，快的原因如下

- Redis采用的是纯内存的存储，内存的存储是非常的快的(这个是主要的原因)
- 非阻塞IO
- 避免了线程的切换和竞态的消耗