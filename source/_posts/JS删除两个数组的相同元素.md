---
title: JS删除两个数组的相同元素
date: 2020-07-23 18:29:40
summary: JS如何快速删除具有相同元素的部分
categories:
    - 前端
tags:
    - 日常总结
---

## 案例1:

  如何arr2的数组内容和arr1的id值进行去重

```js
var arr1 = [
                {
                    id: 1,
                    value: 'value1',
                },
                {
                    id: 2,
                    value: 'value2',
                },
                {
                    id: 5,
                    value: 'value',
                },
                {
                    id: 6,
                    value: 'value6',
                }
            ]
  var arr2 = [3,4]
```

解法：

```js
  arr1 = arr1.filter(function(item){
      return arr2.indexOf(item.id) == -1
  })
  console.log(arr1)
```

