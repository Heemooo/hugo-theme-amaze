+++
title = "CSS IE条件注释"
description = ""
tags = [
    "IE兼容"
]
date = "2018-09-06"
categories = [
	"CSS"
]
+++
# CSS IE条件注释

> 推荐使用条件注释的方式兼容IE,使用Hack方式不宜维护，且有些方面并不容易实现，比如IE里的奇行种IE8，要实现完全正对它的Hack还是有些难度，不如使用条件注释，简单快捷。

#### 调用条件注释的方法
```
<!--[if IE]> ... <![endif]-->
```
- gt(greate than):选择条件版本以上版本，不包含条件版本本身；
- lt(less than):这个刚好与gt相反，表示的是选择条件版本以下的版本，不包含条件版本自身；
- gte(greate than or equal):选择条件版本以上版本，并包含条件版本自身；
- lte(less than or equal):选择条件版本以下的版本，并包含条件版本自身；
- !:选择条件版本以外所有版本，无论高低
#### 条件注释使用方法
1. 支持所有IE浏览器
```
<!--[if IE]>
	<link rel="stylesheet" href="ie.css"  type="text/css"/>
<![endif]-->
```
2. 支持除IE外的所有浏览器
```
<!--[if !IE]>
	<link rel="stylesheet" href="not-ie.css"  type="text/css"/>
<![endif]-->
```
3. 仅仅支持IE7
```
<!--[if IE 7]>
	<link rel="stylesheet" type="text/css" href="ie7.css">
<![endif]-->
```
4. IE8以下(不包括IE8)
```
<!--[if lt IE 8]>
	<link rel="stylesheet" type="text/css" href="ie7-and-down.css">
<![endif]-->
或者
<!--[if lte IE 7]>
	<link rel="stylesheet" type="text/css" href="ie7-and-down.css">
<![endif]-->
```
5. IE7以上(不包括IE7)
```
<!--[if gt IE 7]>
	<link rel="stylesheet" type="text/css" href="ie8-and-up.css">
<![endif]-->
或者
<!--[if gte IE 8]>
	<link rel="stylesheet" type="text/css" href="ie8-and-up.css">
<![endif]-->
```
6. 亦可以用于注释js
```
<!--[if IE 7]>
	<script type="text/javascript" src="js/ie7.js"></script>
<![endif]-->
```
参考文章:[浏览器兼容之旅的第一站:如何创建条件样式](http://www.w3cplus.com/create-an-ie-only-stylesheet)