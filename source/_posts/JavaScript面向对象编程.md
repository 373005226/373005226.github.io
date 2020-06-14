---
title: JavaScript面向对象编程
author: LY
date: 2020-5-18-9:00
summary: 具体描述了JavaScript的变量和名字
categories:
    - 设计模式
tags:
    - JavaScript设计模式
    - JavaScript
---

> 本篇以及JavaScript设计模式系列的代码和大概理论都是出自由张容铭所著的《JavaScript设计模式》，本博客只再阅读后加上自己的理解

## JavaScript的几种函数式编写方式

### 函数亦是变量

在JavaScript中，函数也是一种变量

比如如下的定义函数的代码

```javascript
function checkName(){
	//验证名字
}
function checkEmail(){
	//验证邮箱
}
function checkPassword(){
	//验证密码
}
......
```

如上代码其实等价于

```javascript
var checkName = function(){
	//验证名字
}
var checkEmail = function(){
	//验证邮箱
}
var checkPassword = function(){
	//验证密码
}
```

但是如上做法肯定是污染全局变量的，如果别人也定义了这个方法名字就会进行覆盖

### 函数收编变量

这个时候可以用对象来解决

```javascript
var CheckObject = {
	checkName : function(){
		//验证名字
	},
	checkEmail : function(){
		//验证邮箱
	}
	checkPassword : function(){
		//验证密码
	}
}
```

但是书中1.3章写的不明确，我认为这样的方法不易于拓展，每一个方法都要往复的写到这个对象里边会麻烦

如下写法也是一样的

```javascript
var CheckObject = function (){};
CheckObject.checkName = function(){
	//验证名字
},
CheckObject.checkEmail = function(){
	//验证邮箱
}
CheckObject.checkPassword = function(){
	//验证密码
}
```

但是这样的话别人想用你的方法就比较难了，因为对象不可以复制一份或者说这个对象在用new关键字创建的时候，新创建对象不可以继承于这些方法

就比如买了这本书，书就这么一本，但如果买的是一台打印机，那么无论有多少个人要看这本书都可以打印出来

这里我的个人理解为，这个可以理解为这样的写法两个人看的都是同一本书，如果是一个人要翻页势必会影响另一个人的阅读，在程序说也就是一个人操作这个对象，势必会影响另一个人的对象代码

### 真假对象

对于如上的问题，如果只是想把对象复制一份的话，可以把对象放在一个函数对象中

```javascript
var CheckObject = function (){
	return{
		checkName : function(){
			//验证名字
		},
		checkEmail : function(){
			//验证邮箱
		}
		checkPassword : function(){
			//验证密码
		}
	}
}
```

如上代码也就是意味着每次调用这个函数的时候，都会把之前的那个对象返回回来，当别人每次调用这个函数的时候都会返回一个新对象，这样执行的明面上是CheckObject对象，可实际上返回的是新对象，这样每个人使用的时候都是互不影响了，比如想要检测邮箱使用的时候

```javascript
var a = CheckObject();
a.checkEmail();
```

### 类方法也可以

上面的代码虽然可以完成我们的需求，但是不是一个真正意义上类的创建方式，并且创建的a和对象CheckObject没有任何的关系（即返回出来的对象本身与CheckObject对象无关），所以要对其稍加改造一下

```javascript
var = CheckObject = function(){
	this.checkName = function(){
		//验证名字
	}
	this.checkEmail = function(){
		//验证邮箱
	}
	this.checkPassword = function(){
		//验证密码
	}
}
```

当然使用了如上的方法的话再去使用的话就变成了如下的形式

```
var a = new CheckObject();
a.checkEmail();
```

这样就可以用CheckObject类创建出来的对象了

当然如上的写法，是把所有的方法放在函数内部了，通过this定义的，所以每一次通过new关键字创建新对象的时候，新创建的对象都会对类的this上的属性进行复制。所以这些新创建的对象都会有自己的一套方法，但是这样的消耗是很奢侈的，需要进行处理一下

```javascript
var CheckObject = function(){};
CheckObject.prototype = {
	checkName : function(){
		//验证名字
	},
	checkEmail : function(){
		//验证邮箱
	}
	checkPassword : function(){
		//验证密码
	}
}
```

但是这两种方法不能混合使用，如果一旦混合使用，那么后面为对象的原型对象赋值新对象的时候，就会覆盖掉之前对prototype对象赋值的方法

那么如果是要使用上面定义的方法，就是如下代码所示

```javascript
var a = new CheckObject();
a.checkName();
a.checkEmail();
a.checkPassword();
```

### 另类方法的使用

如上的代码，调用了3个方法，但是a.这个代码用了三次，这个情况是可以避免的，那么就要声明的每一个方法末尾都把当前的对象返回，在JavaScript中this指向的就是当前对象，所以可以把它返回，就例如本篇使用的第一个对象

```javascript
var CheckObject = {
	checkName: function() {
		//验证名字
		return this;
	},
	checkEmail: function() {
		//验证邮箱
		return this;
	}
	checkPassword: function() {
		//验证密码
		return this;
	}
}
```

那么想要使用的他就可以这样

```javascript
CheckObject.checkName().checkEmail().checkPassword()；
```

当然同样的方式还可以放在类的原型对象当中

```javascript
var CheckObject = function(){};
CheckObject.prototype = {
	checkName: function() {
		//验证名字
		return this;
	},
	checkEmail: function() {
		//验证邮箱
		return this;
	}
	checkPassword: function() {
		//验证密码
		return this;
	}
}
```

但是使用的时候也需要创建一下

```javascript
var a = new CheckObject();
a.checkName().checkEmail().checkPassword();
```

### 函数的祖先

如果看过prototype.js的代码，会想到如下的书写方式

prototype是一个JavaScript框架，里面封装了很多的方法，最大的特点就是对原生的对象（JavaScript为我们提供的对象类，如Function、Array、Object等等）的拓展，比如每一个函数都添加一个检测邮箱的方法可以这么做

```javascript
Function.prototype.checkEmail = function(){
	//验证邮箱
}
```

那么创建的新对象都会包含这个方法

```javascript
var f = function(){};
f.checkEmail();
```

比如在vue中，如果要使用一些全局的js方法，那么是在main.js写上这行代码调用的，加入这个变量的名字是common

```
import common from "../common.js"
Vue.prototype.common = common
```

也可以这么写

```
var f = new Function();
f.checkEmail();
```

当然这么做是不允许的，因为污染了原生的对象Function，会造成不必要的开销，但是可以抽象出一个统一的方法的功能方法

```javascript
Function.prototype.addMethod = function(name,fn){
	this[name] = fn;
}
```

这样写的话，那么如果是添加邮箱验证和姓名验证的方法可以这么做

```javascript
var methods = function(){};
```

或者是

```javascript
var methods = new Function();
methods.addMethod('checkName',function(){
	//验证姓名
});
methods.addMethod('checkEmail',function(){
	//验证邮箱
});
methods.checkName();
methods.checkEmail();
```

### 链式添加

如果是想要链式添加，也就是把this返回

```javascript
Function.prototype.addMethod = function(name,fn){
	this[name] = fn;
	return this;
}
```

当然再想添加方法的话就要这么做了

```javascript
var methods = function(){};
methods.addMethod('checkName',function(){
	//验证姓名
}).addMethod('checkEmail',function(){
	//验证邮箱
});
```

以上的方法肯定是错的，如果是想要把this返回实现的话，那么添加的每个方法都将this返回

```javascript
var methods = function(){};
methods.addMethod('checkName',function(){
	//验证姓名
	return this;
}).addMethod('checkEmail',function(){
	//验证邮箱
	return this;
});
```

然后进行测试一下

```javascript
methods.checkName().checkEmail();
```

### 把链式添加换成类方法的使用

如上链式代码的函数调用方式是没有问题了，但是如果是习惯了使用类方法的人来说，可以简单的修改一下

```javascript
Function.prototype.addMethod = function(name,fn){
	this.prototype[name] = fn;
	return this;
}
```

此时还可以按照上一种方式来添加代码

```javascript
var methods = function(){};
methods.addMethod('checkName',function(){
	//验证姓名
}).addMethod('checkEmail',function(){
	//验证邮箱
});
```

但是使用的时候就不可以直接用了，要通过new关键字来创建新对象

```javascript
var m = new Methods();
m.checkEmail()
```

## JavaScript的面向对象编程

在团队开发中，解决需求的时候都是按照一个对象一个对象的去编写，因为如果直接编写函数的话会多很多全局变量

团队的风格一般都是面向对象编程，也就是把需求抽象成一个对象，然后根据这个对象分析其特征（属性和方法），这个对象称之为类。面向对象的其中一个特征是封装，但是遗憾的是对于JavaScript而言这种解释性的弱类型语言没有强类型语言中的class关键字来编写类，只能通过一些特性来模仿，但是这样也给了JavaScript极高的灵活性

### 封装

#### 创建一个类

在JavaScript中，创建一个类很容易，如下所示：

```javascript
var Book = function(id,bookname,price){
	this.id = id 
	this.bookname = bookname
	this.price = price
}
```

也可以在类的原型（类也是一个对象，所以也有原型prototype）上添加属性和方法两种方式，一种是一一位原型对象属性赋值，另一种则是将一个对象赋值给类的原型对象，但是这两种方式不能混用，例如：

```javascript
Book.prototype.display = function(){
	//展示这本书
}
```

或者

```javascript
Book.prototype = {
	display : function(){}
}
```

这样就可以把所需要的方法和属性都封装在我们抽象的Book类里面了，当使用方法的时候不能直接使用这个Book类，需要用new关键字实例化创建新对象，如下所示：

```javascript
var book = new Book(10,'JavaScript设计模式',50);
console.log(book.bookname)	//JavaScript设计模式
```

**如上两份代码，一个用了this一个用了prototype，那么这两个方法有什么不同呢**

**通过this添加的属性、方法都是在当前对象上添加的，然而JavaScript是一种基于原型prototype的语言，每创建一个对象的时候（当然在JavaScript中函数也是一个对象），它都有一个原型prototype用于指向于其继承的属性、方法。这样通过prototype继承的方法并不是对象自身的，所以在使用这些方法的时候，需要通过prototype一级一级查询来得到。这样会发现通过this定义的属性或者方法都是自身拥有的，所以每次通过类创建一个对象的时候，this指向的属性和方法都会得到相应的创建，**

**而通过prototype继承的属性或者方法是对每个对象通过prototype访问到，所以每次通过类创建一个新对象的时候这些属性和方法不会再次创建**

![图2-1](https://txy-tc-ly-1256104767.cos.ap-guangzhou.myqcloud.com/20200518160833.png)

见图2-1 所示，constructor是一个属性，当创建一个函数或者是对象的时候都会为其创建一个原型对象的prototype，在prototype对象中又会像this一样创建一个constructor属性，那么constructor属性指向的就是拥有整个原型对象的函数或者是对象，例如在本例中的Book prototype中的constructor属性就是指向Book类对象

#### 属性的方法和封装

比如在java中，有私有属性、公有属性、公有方法等，JavaScript中虽然没有这些东西，但是可以用技巧去实现他，由于JavaScript的函数级的作用域，声明在内部的变量和方法在外界都是访问不到的，由此可创建类的私有变量和私有方法。

在函数内部，通过this创建的属性和方法，在类创建对象的时候，每个对象都有一份外部可以访问到的的属性，因此通过this创建的属性可看做是对象共有属性和对象的共有方法。而这些this创建的方法和属性，还可以访问到类的私有属性和私有方法。这个可以看做是一个特权方法。在对象创建的时候通过这些特权方法可以初始化实例对象的一些属性，因此这个可以看做是一个构造器

```javascript
var Book = function(id,name,price){
	var num = 1;
	function checkId(){
		
	};
	//特权方法
	this.getName = function(){};
	this.getPrice = function(){};
	this.setName = function(){};
	this.setPrice = function(){};
	//对象公有属性
	this.id = id
	//对象共有方法
	this.copy = function(){};
	//构造器
	this.setName(name);
	this.setPrice(price);
};
```

如上代码可知，可以通过JavaScript的块级作用域的特征来实现在函数内部创建外界访问不到的私有变量和私有化方法。通过new关键字实例化对象时，由于只执行一次，所以类的内部this上定义的属性和方法都复制到新创建的对象上，成为公有化的属性和方法