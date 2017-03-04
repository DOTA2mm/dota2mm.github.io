---
title: CSS垂直水平居中与z-index详解
date: 2016/5/21
categories:
- 笔记整理
tags:
- css
- 垂直居中
---


## 优雅实现垂直水平居中的3种方法 ##
1. #### 方法一 ####
使用绝对定位，并设置`left`,`right`,`top`,`bottom`为0，`margin:auto`
```css
	html,body {
		width: 100%;
		height: 100%;
		margin: 0;
	}
	body {
		position: relative;
	}
	.div {
		background-color: green;
		width: 400px;
		height: 400px;
		position: absolute;
		left: 0;
		right: 0;
		top: 0;
		bottom: 0;
		margin: auto;
	}
```
<!--more-->

2. #### 方法二 ####
父元素使用`flex`布局，`align-items: center;`：设置垂直居中，`justify-content: center;`：设置水平居中
```css
	html,body {
		width: 100%;
		height: 100%;
		margin: 0;
	}
	body {
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.div {
		background-color: green;
		width: 400px;
		height: 400px;
	}
```
3. #### 方法三 ####
使用相对定位，设置`top: 50%;`，此时的top值50%是相对父元素的，再用`translateY(-50%);`来转换，此时的-50%是相对自身高度的
```css
	html,body {
		width: 100%;
		height: 100%;
		margin: 0;
	}
	.div {
		width: 400px;
		height: 400px;
		background-color: green;
		position: relative;
		top: 50%;
		transform: translateY(-50%);/*margin-top:-200px*/
		margin:0 auto;
	}
```

## 层叠顺序详解(z-index) ##

>参考链接：[cnblog](http://www.cnblogs.com/bfgis/p/5440956.html)

z-index可以设置成三个值：
- `auto`，默认值。当设置成auto的时候，当前元素的层叠级数是0，同时这个盒子不会创建新的层级上下文；
- `<integer>`，指示层叠级数，可以是负值，同时无论是什么值，都会创建一个新的层叠上下文；
- `inherit`，父元素继承