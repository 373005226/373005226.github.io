---
title: JavaScript的字符串方法大全
date: 2020-07-10 17:57:12
summary: 这些都是在前端开发中很常用的数组方法
categories:
    - 前端
tags:
    - JavaScript
    - 转载
---

> 本文转载于：https://blog.csdn.net/shenxianhui1995/article/details/85271352

| 方法                                                         | 描述                                                         | 备注         |
| :----------------------------------------------------------- | :----------------------------------------------------------- | ------------ |
| [charAt()](https://www.runoob.com/jsref/jsref-charat.html)   | 返回在指定位置的字符。                                       |              |
| [charCodeAt()](https://www.runoob.com/jsref/jsref-charcodeat.html) | 返回在指定的位置的字符的 Unicode 编码。                      |              |
| [concat()](https://www.runoob.com/jsref/jsref-concat-string.html) | 连接两个或更多字符串，并返回新的字符串。                     |              |
| [fromCharCode()](https://www.runoob.com/jsref/jsref-fromcharcode.html) | 将 Unicode 编码转为字符。                                    | 静态方法     |
| [indexOf()](https://www.runoob.com/jsref/jsref-indexof.html) | 返回某个指定的字符串值在字符串中首次出现的位置。             |              |
| [includes()](https://www.runoob.com/jsref/jsref-string-includes.html) | 查找字符串中是否包含指定的子字符串。                         |              |
| [lastIndexOf()](https://www.runoob.com/jsref/jsref-lastindexof.html) | 从后向前搜索字符串，并从起始位置（0）开始计算返回字符串最后出现的位置。 |              |
| [match()](https://www.runoob.com/jsref/jsref-match.html)     | 查找找到一个或多个正则表达式的匹配。                         |              |
| [repeat()](https://www.runoob.com/jsref/jsref-repeat.html)   | 复制字符串指定次数，并将它们连接在一起返回。                 |              |
| [replace()](https://www.runoob.com/jsref/jsref-replace.html) | 在字符串中查找匹配的子串， 并替换与正则表达式匹配的子串。    |              |
| [search()](https://www.runoob.com/jsref/jsref-search.html)   | 查找与正则表达式相匹配的值。                                 |              |
| [slice()](https://www.runoob.com/jsref/jsref-slice-string.html) | 提取字符串的片断，并在新的字符串中返回被提取的部分。         |              |
| [split()](https://www.runoob.com/jsref/jsref-split.html)     | 把字符串分割为字符串数组。                                   | 字符串转数组 |
| [startsWith()](https://www.runoob.com/jsref/jsref-startswith.html) | 查看字符串是否以指定的子字符串开头。                         |              |
| [substr()](https://www.runoob.com/jsref/jsref-substr.html)   | 从起始索引号提取字符串中指定数目的字符。                     |              |
| [substring()](https://www.runoob.com/jsref/jsref-substring.html) | 提取字符串中两个指定的索引号之间的字符。                     |              |
| [toLowerCase()](https://www.runoob.com/jsref/jsref-tolowercase.html) | 把字符串转换为小写。                                         |              |
| [toUpperCase()](https://www.runoob.com/jsref/jsref-touppercase.html) | 把字符串转换为大写。                                         |              |
| [trim()](https://www.runoob.com/jsref/jsref-trim.html)       | 去除字符串两边的空白                                         |              |
| [toLocaleLowerCase()](https://www.runoob.com/jsref/jsref-tolocalelowercase.html) | 根据本地主机的语言环境把字符串转换为小写。                   |              |
| [toLocaleUpperCase()](https://www.runoob.com/jsref/jsref-tolocaleuppercase.html) | 根据本地主机的语言环境把字符串转换为大写。                   |              |
| [valueOf()](https://www.runoob.com/jsref/jsref-valueof-string.html) | 返回某个字符串对象的原始值。                                 |              |
| [toString()](https://www.runoob.com/jsref/jsref-tostring.html) | 返回一个字符串。                                             |              |

## 一. 字符方法

### charAt()

- 定义和用法
  charAt() 方法可返回指定位置的字符
  第一个字符位置为 0, 第二个字符位置为 1,以此类推

- 语法
  string.charAt(index);
  index 必需。表示字符串中某个位置的数字，即字符在字符串中的位置

### charCodeAt()

- 定义和用法
  **charCodeAt()** 方法可返回指定位置的字符的 Unicode 编码
  字符串中第一个字符的位置为 0， 第二个字符位置为 1，以此类推

- 语法
  **string.charCodeAt(index);**
  index 必需。表示字符串中某个位置的数字，即字符在字符串中的下标

## 二. 字符串操作方法

### concat()

- 定义和用法
  用于将一或多个字符串拼接起来, 返回拼接得到的新字符串
- 语法
  string.concat(string1, string2, ..., stringX);
  string1, string2, ..., stringX 必需。将被连接为一个字符串的一个或多个字符串对象
- 提示
  虽然 concat() 是专门用来拼接字符串的方法, 但实践中使用更多的还是加号操作符(+). 而且, 使用加号操作符在大多数情况下都比使用 concat() 方法要简便易行(特别是在拼接多个字符串的情况下)

### slice()

- 定义和用法
  slice(start, end) 方法可提取字符串的某个部分，并以新的字符串返回被提取的部分
  使用 start（包含） 和 end（不包含） 参数来指定字符串提取的部分
  字符串中第一个字符位置为 0, 第二个字符位置为 1, 以此类推

- 语法
  string.slice(start, end);
  start 必需。 要抽取的片断的起始下标。第一个字符位置为 0
  end 可选。 紧接着要截取的片段结尾的下标。若未指定此参数，则要提取的子串包括 start 到原字符串结尾的字符串。如果该参数是负数，那么它规定的是从字符串的尾部开始算起的位置
- 提示
  如果是负数，则该参数规定的是从字符串的尾部开始算起的位置。也就是说，-1 指字符串的最后一个字符，-2 指倒数第二个字符，以此类推

### substr()

- 定义和用法
  substr() 方法可在字符串中抽取从开始下标开始的指定数目的字符
- 语法
  string.substr(start, length);
  start 必需。要抽取的子串的起始下标。必须是数值。如果是负数，那么该参数声明从字符串的尾部开始算起的位置。也就是说，-1 指字符串中最后一个字符，-2 指倒数第二个字符，以此类推
  length 可选。子串中的字符数。必须是数值。如果省略了该参数，那么返回从 stringObject 的开始位置到结尾的字串

### substring()

- 定义和用法
  substring() 方法用于提取字符串中介于两个指定下标之间的字符
  substring() 方法返回的子串包括开始处的字符，但不包括结束处的字符
- 语法
  string.substring(from, to);
  from 必需。一个非负的整数，规定要提取的子串的第一个字符在 string Object 中的位置
  to 可选。一个非负的整数，比要提取的子串的最后一个字符在 string Object 中的位置多 1。如果省略该参数，那么返回的子串会一直到字符串的结尾

## 三. 字符串位置方法

### indexOf()

- 定义和用法
  indexOf() 方法可返回某个指定的字符串值在字符串中首次出现的位置
  如果没有找到匹配的字符串则返回 -1
- 语法
  string.indexOf(searchvalue, start);
  searchvalue 必需。规定需检索的字符串值
  start 可选的整数参数。规定在字符串中开始检索的位置。它的合法取值是 0 到 string Object.length - 1。如省略该参数，则将从字符串的首字符开始检索

### lastIndexOf()

- 定义和用法
  lastIndexOf() 方法可返回一个指定的字符串值最后出现的位置，如果指定第二个参数 start，则在一个字符串中的指定位置从后向前搜索
- 语法
  string.lastIndexOf(searchvalue, start);
  searchvalue 必需。规定需检索的字符串值
  start 可选的整数参数。规定在字符串中开始检索的位置。它的合法取值是 0 到 stringObject.length - 1。如省略该参数，则将从字符串的最后一个字符处开始检索
- 注意
  该方法将从后向前检索字符串，但返回是从起始位置 (0) 开始计算子字符串最后出现的位置。 看它是否含有字符串
  开始检索的位置在字符串的 start 处或字符串的结尾（没有指定 start 时）
  如果没有找到匹配字符串则返回 -1

## 四. 字符串大小写转换方法

### toLowerCase()

- 定义和用法
  toLowerCase() 方法用于把字符串转换为小写
- 语法
  string.toLowerCase()

### toUpperCase()

- 定义和用法
  toUpperCase() 方法用于把字符串转换为大写
- 语法
  string.toUpperCase()

## 五. 字符串的模式匹配方法

### match()

- 定义和用法
  match() 方法可在字符串内检索指定的值，或找到一个或多个正则表达式的匹配
- 语法
  string.match(regexp)
  regexp 必需。规定要匹配的模式的 RegExp 对象。如果该参数不是 RegExp 对象，则需要首先把它传递给 RegExp 构造函数，将其转换为 RegExp 对象。

- 注意
  match() 方法将检索字符串 String Object，以找到一个或多个与 regexp 匹配的文本
  这个方法的行为在很大程度上有赖于 regexp 是否具有标志 g
  如果 regexp 没有标志 g，那么 match() 方法就只能在 stringObject 中执行一次匹配
  如果没有找到任何匹配的文本， match() 将返回 null
  否则，它将返回一个数组，其中存放了与它找到的匹配文本有关的信息

### search()

- 定义和用法
  search() 方法返回字符串中第一个匹配项的索引; 如果没有找到匹配项, 则返回 -1
  search() 方法始终是从字符串开头向后查找模式
- 语法
  string.search(searchvalue)
  searchvalue 必需。查找的字符串或者正则表达式

### replace()

- 定义和用法
  replace() 方法用于在字符串中用一些字符替换另一些字符，或替换一个与正则表达式匹配的子串
  该方法不会改变原始字符串
- 语法
  string.replace(searchvalue,newvalue)
  searchvalue 必需。规定子字符串或要替换的模式的 RegExp 对象。
  请注意，如果该值是一个字符串，则将它作为要检索的直接量文本模式，而不是首先被转换为 RegExp 对象
  newvalue 必需。一个字符串值。规定了替换文本或生成替换文本的函数

### split()

- 定义和用法
  split() 方法用于把一个字符串分割成字符串数组
- 语法
  string.split(separator,limit)
  separator 可选。字符串或正则表达式，从该参数指定的地方分割 stringObject
  limit 可选。该参数可指定返回的数组的最大长度。如果设置了该参数，返回的子串不会多于这个参数指定的数组。如果没有设置该参数，整个字符串都会被分割，不考虑它的长度
- 提示
  如果把空字符串 ("") 用作 separator，那么 stringObject 中的每个字符之间都会被分割

- 注意
  split() 方法不改变原始字符串

## 六. 其他

### trim()

- 定义和用法
  trim() 方法会创建一个字符串的副本, 删除前置及后缀的所有空格, 然后返回结果
- 语法
  string.trim();

- 注意

- 
  实例


```js
let stringValue = "    hello world    ";

stringValue.trim(); // "hello world"
```

### localeCompare()

- 定义和用法
  从前到后依次比较两个字符串并返回下列值中的一个:
- 如果字符串在字母表中应该排在字符串参数之前, 则返回一个负数 (大多数情况下是 -1)
  如果字符串等于字符串参数, 则返回 0
  如果字符串在字母表中应该排在字符串参数之后, 则返回一个正数 (大多数情况下是 1)
  语法
  stringObject.localeCompare(target)
  target 要以本地特定的顺序与 stringObject 进行比较的字符串
- 注意
  localeCompare() 区分大小写, 同字母小写在前, 大写在后

- 实例


```js
let stringValue = "bcd";

stringValue.localeCompare("abc"); // 1
stringValue.localeCompare("bc"); // 1
stringValue.localeCompare("bcd"); // 0
stringValue.localeCompare("bcde"); // -1
stringValue.localeCompare("cd"); // -1
stringValue.localeCompare("Bcd"); // -1
```

### formCharCode()

- 定义和用法
  fromCharCode() 可接受一个指定的 Unicode 值，然后返回一个字符串
  从本质来看, 这个方法与 charCodeAt() 执行的是相反的操作
- 语法
  String.fromCharCode(numX, numX, ..., numX)
  numX 必需。一个或多个 Unicode 值，即要创建的字符串中的字符的 Unicode 编码

- 注意
  该方法是 String 的静态方法，字符串中的每个字符都由单独的数字 Unicode 编码指定
  它不能作为您已创建的 String 对象的方法来使用
  因此它的语法应该是 String.fromCharCode()，而不是 myStringObject.fromCharCode()

- 实例


```js
String.fromCharCode(104, 101, 108, 108, 111); // "hello"
```

### valueOf()

- 定义和用法
  valueOf() 方法可返回 String 对象的原始值
- 语法
  string.valueOf()

- 注意
  valueOf() 方法通常由 JavaScript 在后台自动进行调用，而不是显式地处于代码中
