---
title: promise详解
date: 2020-07-07 11:26:50
summary: 详promise的多种方使用法
categories:
    - 前端
tags:
    - 转载
    - Vue
    - ES6语法
---

> 本文转载于：https://www.jianshu.com/p/dbda3053da20

## 1.Promise.all 并行执行promise

> getA和getB并行执行，然后输出结果。如果有一个错误，就抛出错误

```js
/**
 * 每一个promise都必须返回resolve结果才正确
 * 每一个promise都不处理错误
 */

const getA = new Promise((resolve, reject) => {
   //模拟异步任务
   setTimeout(function(){
     resolve(2);
   }, 1000) 
})
.then(result => result)


const getB = new Promise((resolve, reject) => {
   setTimeout(function(){
     // resolve(3);
     reject('Error in getB');
   }, 1000) 
})
.then(result => result)


Promise.all([getA, getB]).then(data=>{
    console.log(data)
})
.catch(e => console.log(e));
```

> getA和getB并行执行，然后输出结果。总是返回resolve结果

```js
/**
 * 每一个promise自己处理错误
 */

const getA = new Promise((resolve, reject) => {
   //模拟异步任务
   setTimeout(function(){
     resolve(2);
   }, 1000) 
})
.then(result => result)
.catch(e=>{

})


const getB = new Promise((resolve, reject) => {
   setTimeout(function(){
     // resolve(3);
     reject('Error in getB');
   }, 1000) 
})
.then(result => result)
.catch(e=>e)


Promise.all([getA, getB]).then(data=>{
    console.log(data)
})
.catch(e => console.log(e));
```

## 2.顺序执行promise

> 先getA然后getB执行，最后addAB

### 2.1 方法一——连续使用then链式操作

```js
function getA(){
      return  new Promise(function(resolve, reject){ 
      setTimeout(function(){     
            resolve(2);
        }, 1000);
    });
}
 
function getB(){
    return  new Promise(function(resolve, reject){       
        setTimeout(function(){
            resolve(3);
        }, 1000);
    });
}
 
function addAB(a,b){
    return a+b
}

function getResult(){
    var  obj={};
    Promise.resolve().then(function(){
        return  getA() 
    })
    .then(function(a){
         obj.a=a;
    })
    .then(function(){
        return getB() 
    })
    .then(function(b){
         obj.b=b;
         return obj;
    })
    .then(function(obj){
       return  addAB(obj['a'],obj['b'])
    })
    .then(data=>{
        console.log(data)
    })
    .catch(e => console.log(e));

}
getResult();
```

### 2.2 方法二——使用promise构建队列

```js
function getResult(){
    var res=[];
    // 构建队列
    function queue(arr) {
      var sequence = Promise.resolve();
      arr.forEach(function (item) {
        sequence = sequence.then(item).then(data=>{
            res.push(data);
            return res
        })
      })
      return sequence
    }

    // 执行队列
    queue([getA,getB]).then(data=>{
        return addAB(data[0],data[1])
    })
    .then(data => {
        console.log(data)
    })
    .catch(e => console.log(e));

}

getResult();
```

### 2.3方法三——使用async、await实现类似同步编程

```js
function getResult(){
 async function queue(arr) {
  let res = []
  for (let fn of arr) {
    var data= await fn();
    res.push(data);
  }
  return await res
}

queue([getA,getB])
  .then(data => {
    return addAB(data[0],data[1])
  }).then(data=>console.log(data))

}
```

## 总结

实现异步队列函数的三种方式

> 方法一——连续使用then链式操作
> 方法二——使用promise构建队列
> 方法三——使用async、await实现类似同步编程，async函数内部实现同步