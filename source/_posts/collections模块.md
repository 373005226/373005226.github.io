---
title: collections模块
summary: collections模块里面有很多种python常用的数据结构
date: 2019-10-18
author: LY
categories:
    - python
tags:
    - 数据结构与算法
---

## ChainMap

```python
from collections import ChainMap

user_dict1={"a":12,"b":15}
user_dict2={"c":16,"d":19}
new_dict=ChainMap(user_dict1,user_dict2)
print(new_dict)
for key ,values in new_dict.items():
    print(key,values)
```

就是合并两个字典

&emsp;

## Counter

Counter是一个统计数量的方法，方法比defaultdict来实现要简单的多

```python
from collections import Counter


Users=[11,22,11,33,22,33,66]
user=Counter(Users)
print(user)
```

甚至可以直接选择出前几个排名的方法

```python
from collections import Counter


Users=[11,22,11,33,22,33,66]
user=Counter(Users)
print(user.most_common(2))
```

这些方法要自己从头实现是非常的麻烦的，这个是使用了堆的数据结构，比自己用遍历的方法实现性能是高很多的 

&emsp;

## defaultdict

如果要对一个列表的数进行统计

```python
userdict={}
Users=['LY','LY','LY1','LY1','LY2','LY2']
for user in Users:
    if user not in userdict:
        userdict[user]=1
    else:
        userdict[user]+=1
print(userdict)
```

这样写可以，但是如果逻辑一多，代码可读性就差



```python
userdict={}
Users=['LY','LY','LY1','LY1','LY2','LY2']
for user in Users:
    userdict.setdefault(user,0)
    userdict[user]+=1
print(userdict)
```

这里的setdefault代码比上面的少，性能还比第一个高，因为少了一次判断



如果使用defaultdict的方法会更好 

```python
from collections import defaultdict

Users=['LY','LY','LY1','LY1','LY2','LY2']
default_dict = defaultdict(int)
for user in Users:
    default_dict[user]+=1
print(default_dict)
```

如果没有赋值就看defaultdict传入的可调用对象，如果是整数就为0，如果为字符串就为空，而不是抛出异常

当然这个方法的默认值是可以改变的，但不是直接传一个变量进去，而是定义一个函数，函数的返回值是调用默认对象组合的数组

```python
def test():
    return {
        "name":"",
        "nums":0
    }
default_dict=defaultdict(test)
default_dict["name"]
```

&emsp;

## dqeue双端队列

以前我们数据结构都可能append等方法，而双端队列就是两边都可以进行的操作

```
from collections import deque


userlist=deque(["LY","LY2"])
userlist.appendleft("1")
print(userlist)
```

&emsp;

## namedtuple

在类中，可以有如下形式

```python
class User:
    def __init__(self,name,age):
        self.name=name
        self.age=age

user = User(name='ly',age=21)
print(user.name)
print(user.age)
```

可以用`.属性名`的形式表示出来，字面意思清晰明了



在namedtuple中，也可以定义一个类的形式

```python
from collections import namedtuple

User=namedtuple("User",["name","age"])
user=User(name="ly",age=21)
print(user.name,user.age)
```



两者形式是差不多完全一样的，那么区别是什么呢

在类中，定义一个类需要耗费的内存相对于namedtuple是比较大的，class会内置很多对象，而namedtuple就单单只是一个存储数据的数据结构

其余的方法可以直接看源码来找方法

&emsp;

## OrderDict

在python3中，使用dict和OrderDict结果是一样的，顺序都为自己添加时候的顺序

而在python2中，使用dict添加的顺序是排序好的，而OrderDict才是自己添加的

```
from collections import OrderedDict
user_dict = OrderedDict()
user_dict["b"]="test1"
user_dict["a"]="test2"
user_dict["c"]="test3"
print(user_dict)
```

其余方法都在源码中有说明

&emsp;

## 补充：为什么tuple会比list好

**性能优化**

元素全为不可变对象的时候，编译出来的pyc，也就是python的字节码是很确定的，所以加载的速度要比一般的快

**线程安全**

可以作为dict的key，因为元组是可哈希对象

**拆包特性**