---
title: enumerate函数
summary: enumerate函数就是为列表添加索引
date: 2019-10-18
author: LY
categories:
    - 后端
tags:
    - python语法
---

## enumerate() 函数

**enumerate() 函数**可以使得列表添加索引

比如

```python
 L = ['Adam', 'Lisa', 'Bart', 'Paul']
 for index, name in enumerate(L):
     print index, '-', name
#0 - Adam
#1 - Lisa
#2 - Bart
#3 - Paul
```

这个函数实际上原理是把**['Adam', 'Lisa', 'Bart', 'Paul']**转换成了**[(0, 'Adam'), (1, 'Lisa'), (2, 'Bart'), (3, 'Paul')]**

每一次迭代都是一个元组

```python
for t in enumerate(L):
    index = t[0]
    name = t[1]
    print index, '-', name
```

所以就可以简写为

```python
for index, name in enumerate(L):
    print index, '-', name
```

