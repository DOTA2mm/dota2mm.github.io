---
title: 《JavaScript高级程序设计》学习-5.2-Array
date: 2016/5/31 
categories:
- 笔记整理
tags:
- JavaScript
- 原生JS
---

>《JavaScript高级程序设计》俗称“红宝书”，一直被列为前端人员必读经典，奉为前端圣经。买红宝书到现在很久了，刚开始一直拿他当工具书用，买了不久我又入手了更为适合当工具书的“犀牛书”，所以到现在红宝书都还没有完整的过一遍，实感不安。最近计划利用闲暇时间过一遍，为加深印象留下笔记。此为第五章：引用类型中的第三节：Array类型的讲解，较为基础。

## Array类型 ##
ECMAScript中的数组是常用的引用类型之一，秉承js松散类型的特点，js数组的每一项都可以保存任何类型的数据，并且数组的大小是可以动态调整的。
数组的基本创建方法有两种：
1. 使用Array构造函数：`var arr = new Array(3)`；
如上写法创建了一个长度为3的数组，
2. 使用数组字面量：`var arr = [1,2,3]`；
此写法较为常用，需要注意的是`[1,2,]`这种最后一项为空的写法IE8-会认为数组长度为2，而现代浏览器会认为长度为3，故尽量避免出现该问题。

可以改变数组的`length`属性值来为数组扩容（缩容），扩容后的数组被跳过的项的指为`undefined`。

<!--more-->

## 检测数组 ##
检测一个对象是不是数组有2种方法：
1. 使用`instanceof`操作符：`value instanceof Array`;
解读为value是否为Array的一个实例。该方法的局限性在于：假定只有一个全局执行环境。
2. 使用ES5中新增的`Array.isArray()`方法；
该方法判断准确，但只能在现代浏览器中使用。

## 数组方法 ##
### 转换方法 ###
数组继承对象的`toLocaleString()`，`toString()`，`valueOf()`方法。
```javascript
var colors = ['red','green','blue'];
alert(colors.toString());  //red,green,blue
alert(colors.valueOf());   //red,green,blue
alert(colors);             //red,green,blue
```
直接alert数组时会隐式的调用数组的`toString()`方法。这三种方法都是返回数组中每个元素以`,`分隔的结果，可以调用数组的`join("分隔符")`来以自定义的分隔符分隔数组。
### 栈方法 ###
1. `push()`:添加任意项到数组末尾--推入，末尾入栈
2. `pop()`:

## 未完待续 ##