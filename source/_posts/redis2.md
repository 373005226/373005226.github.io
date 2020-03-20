---
title: Redis五大数据结构的介绍及基本命令使用
author: LY
date: 2019-10-13
img: https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/redis.jpg
summary: 介绍了Redis的五大数据结构：String、hash、List、set、sorted set的常用命令
categories:
    - 数据库
tags:
    - Redis
---



## String(字符串)

### String的限制

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;String类型可以存储任何形式的字符串，比如整型，数字，二进制数据，json对象等，但是在有一个内存大小限制，最大只能存储512M

### 字符串的API

- 赋值：
  - set ：`set key value`，加键值对，无论key是否存在都设置
  - setnx ：`setnx key value`，key不存在才添加键值对
  - set  xx：`set key value xx`，key存在才设置键值对
  - mset ：`mset key1 value1 key2 value2`，设置多个键值对，时间复杂度为O(n)
  - getset ：`getset key newvalue`，添加新的值并且返回老的值
  - append ：`append key value`，将value追加到旧的value
  - setrange ：`setrange key index value`，设置指定下标所有对应的值，比如hello的值为world，setrange hello 0 L就是得到Lorld
- 查询：
  - get ：`get key`，获取键值对
  - mget ：`mget key1 key2`，批量获取key，原子操作，时间复杂度为O(n)
  - strlen ：`strlen key`，返回字符串的长度(中文一个字占两个字符)
  - getrange ：`getrange key start end`，获取字符串指定下标的所有值，比如hello的值为world，getrange hello 0 2就是得到wor
- 删除：
  - del：`del key`，删除键值对
- 自增：
  - incr ：`incr key`，key自增1，如果key不存在，自增后get(key)=1
  - incrby ：`incrby key`，key自增k，如果key不存在，自增后get(key)=k
  - incrbyfloat ：`incrbyfloat key`，是浮点数的自增操作
- 自减：
  - decrby ：`decrby key`，key自减k，如果key不存在，自增后get(key)=-k
  - decr ：`decr key`，key自减1，如果key不存在，自增后get(key)=-1

### String的使用场景

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;String一般适用于缓存（网站记录用户登录状态、视频缓存等）、计数器（比如每次观看视频，次数就+1，而且因为redis是单线程的，计数器是完全不会出错的），分布式锁等等

## hash(哈希)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;hash其实还是一个key-value结构，但是跟String有不同，hash类似于mysql的一个表的结构数据，每一个key都有一个field和value，field不能相同。就相当于key是mysql的一个表，filed是一个字段，value是值

### 哈希的API

- 赋值：
  - hset：哈希赋值，比如hset user:1:info age 23
  - hmset ：`hmset key1 vaule 1 key2 value2`批量设置一批key和value
  - hsetnx ：`hsetnx key field value`设置hash key对应field的value（如果存在就设置失败）
- 查询：
  - hget：哈希取值，比如hget user:1:info age
  - hgetall：获取所有的属性和值，比如hgetall user:1:info，时间复杂度O(n)
  - hvals：`hvals key`返回hash key对应所有的field的value，比如hvals user:1:info，时间复杂度O(n)
  - hkeys ：`hkeys key`：返回hash key对应所有的field，时间复杂度O(n)
  - hexists ：`hexists key field`：判断 hash key是否存在field，比如hlen user:1:info name
  - hlen：`hlen key`：获取hash key field数量，比如hlen user:1:info
  - hmget ：`hmget key field1 field2`：批量获取key一批的field值
- 删除：
  - hdel：删除哈希的值，比如hdel user:1:info
- 自增：
  - hincrby ：`hincrby key field intCounter`hash key对应的field的value自增intCounter
  - hincrbyfloat ：`hincrbyfloat key field floatCounter`增加浮点数



## list(列表)

list是有序的列表，可以接受重复值

### 列表的API

- 覆盖值：
  - lset：`lset key index newValue`设置列表索引值为newValue
- 插入值：
  - lpush：从左端插入值
  - rpush：从右端插入值，如rpushlistkey c b a，就是从右边插入c b a
  - linsert：`linsert key before|after value newValue`，时间复杂度O(n)
- 弹出值：
  - rpop：从右端弹出值
  - lpop：从左端弹出值
- 删除：
  - lrem：`lrem key count value`根据count来删除所有value相等的项，时间复杂度为O(n)
    - count>0，从左到右，删除最多的count个value相等的项
    - count<0，从右到左，删除最多的Math.abs(count)个value相等的项
    - count=0，删除所有的value相等的项
  - ltrim：`ltrim key start end`，时间复杂度为O(n)，即只保留下标start到end的，时间复杂度为O(n)
- 查询：
  - lrange：`lrange key start end(包含end)`，这是一个查询的API，只查询start到end的，负号就是从右边开始，时间复杂度为O(n)
  - lindex：`lindex key index`获取列表指定索引的value，时间复杂度为O(n)
  - llen：`llen key`获取列表的长度
- 阻塞版：b就是阻塞，比如一个空队列的话使用lpop会立刻弹出，但是使用blpop就会等待有值再弹出
  - blpop：`blpop key timeout`，timeout是阻塞超时时间，=0代表永不阻塞
  - brpop：`brpop key timeout`，timeout是阻塞超时时间，=0代表永不阻塞



## set(集合)

### 集合特征

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;集合是一个**无序和无重复元素**的的，还可以使用**集合操作**，集合就相当于我们高中学的数学的那个集合，两个集合可以交、并、补操作，同时集合是无序和不能重复的

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;集合的value是由field和value组成

### 集合的API

- 添加：
  - sadd：`sadd key element1 [element2]`向集合添加element，如果element已存在就添加失败，返回结果为0
- 删除：
  - srem：`srem key element`将集合key中的element删掉
  - spop：`spop key [count]`随机弹出集合的随机count个元素，如果没填就默认是1，改变原有的集合
- 查询
  - scard：`scard key`返回集合元素的数量
  - sismember：`SISMEMBER KEY VALUE` 计算元素是否在集合当中，是就返回1，不存在返回0
  - srandmember：`srandmember count` 返回集合中随便count 个元素，如果count没填就默认为1，不改变原有的集合
  - smembers：`smembers key [count]` 取出集合中所有的元素
- 集合间的操作：
  - sdiff：也就是数学集合中的差集  `sdiff key1 key2` 
  - sinter：数学中的交集 `sinter key1 key2`
  - sunion：数学中的并集 `sunion key1 key2`
- 保存集合操作的结果：
  - sdiffstore：`sdiffstore newValue key1 key2`,保存key1和key2的差集结果在newValue中
  - sinterstore：`sinterstore newValue key1 key2`,保存key1和key2的交集结果在newValue中
  - sunionstore：`sunionstore newValue key1 key2`,保存key1和key2的并集结果在newValue中



## sorted set(有序集合)

### 有序集合特征

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;有序集合是无重复元素的、有序，有序集合的的value是由element和score共同组成，有序集合所有的API都是z开头

有序集合的score可以重复，element不可以重复

### zset跟list和set的区别

| 集合(set)            | 有序集合(zset)           |
| -------------------- | ------------------------ |
| 无重复元素           | 无重复元素               |
| 无序                 | 有序                     |
| value是由element组成 | value由score+element组成 |

| 列表(list)           | 有序集合(zset)           |
| -------------------- | ------------------------ |
| 可以有重复元素       | 无重复元素               |
| 有序                 | 有序                     |
| value是由element组成 | value由score+element组成 |

### 有序集合API

- zadd：`zadd key score element(可以是多个)`O(logN)
- zrem：`zrem key element` 删除元素(可以是多个)
- zscore：`zscore key element`查询分数
- zincrby：`zincrby key increScore element`，给key增加increScore 分数
- zcard：`zcard key`返回元素总个数
- zrank：
- zrange：`zrange key start end [withscore]`，返回指定范围内的排名数，时间复杂度为O(log(n)+m)
- zrangebyscore：`zrangebyscore key minScore maxScore [withscore]`，返回指定范围的分数段，时间复杂度为O(log(n)+m)
- zcount：`zcount key minScore maxScore`，返回指定分数范围内的个数
- zremrangebyrank：`zremrangebyrank key start end`，删除指定排名内的升序元素，时间复杂度为O(log(n)+m)
- zrevrank：从高到低的排名
- zrevrange：
- zrevrangebyscore
- zunionstore：
- zinterstore：