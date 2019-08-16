+++
title = "前段基础知识记录"
description = ""
tags = [
    "CSS"
]
date = "2018-12-26"
categories = [
	"基础"
]
+++
# 前段基础知识记录
> 简单记录一些CSS的基础知识，都是Web项目开发中常遇到的，比如左右布局、相对布局留白、浮动弹窗等，这里做一个简单的记录，方便日后忘记了查阅

## 经典左右布局
在开发过程中很常见的前端需求是实现一个左右布局，左边宽度固定，右边根据左边的宽度自适应屏幕填充满剩余的屏幕
### 法一
左侧 DIV 设置 float 属性为 left，右侧 DIV 设置 margin-left 属性等于或大于左侧 DIV 宽度
```html
<div style="float: left;width: 50%;text-align: center;"></div>
<div style="position: relative;margin-left: 50%;text-align: center;"></div>
```
效果如下（为了有效果加了高度和背景颜色）：

<div style="float: left;width: 50%;text-align: center;height:50px;background:lightgreen;"></div>
<div style="position: relative;margin-left: 50%;text-align: center;height:50px;background:lightgray;"></div>

```html
<div style="float: left;width: 50%;text-align: center;height:50px;background:lightgreen;"></div>
<div style="position: relative;margin-left: 50%;text-align: center;height:50px;background:lightgray;"></div>
```
## 清除相对布局的留空区域
*相对定位和绝对定位区别：*
   1. 都可以设置位置top，left..等属性，但是相对定位是不脱离文档流的，所以占有原来的位置，无论怎么定位，原来占有的地方依然不能被利用，相反绝对定位是脱离的文档流的。
   2. 相对定位一般只用于给后代元素提供一个“框架”参考，基本和绝对元素没有太多差别
所以最佳解决方法是，最初就用绝对定位来代替相对定位。其后代元素也是可以用这个元素作为参考的。

## ie兼容性问题
Q:window.innerWidth在ie7、8下无效
A:使用Jquery的$(window).width()，这个问题是在开发过程中一个页面在ie7、8下没有完整运行，经过打断点排查，检查到其中 window.innerWidth的值为NaN，但是在ie8以上和其它浏览器是支持的，这里就需要使用一个兼容性较好的替代品Jquery的方法$(window).width()

*$(window).width()与window.innerWidth有什么区别：*

$(window).width()也是获得浏览器宽度的方法，但它跟window.innerWidth是不同的。
1. $(window).width()需要引用jquery.js文件，它是jquery方法。
2. 它们获取的数值是不同的，window.innerWidth获取当前窗口的宽度(包含滚动条)，$(window).width()获取当前窗口的宽度(不包含滚动条)。

## 元素使用浮动导致父元素塌陷，高度为0
此时可以使用overflow属性，overflow 属性规定当内容溢出元素框时发生的事情。只需要给父元素设置overflow属性

可能的值| 描述
------- | -------
visible |	默认值。内容不会被修剪，会呈现在元素框之外。
hidden |内容会被修剪，并且其余内容是不可见的。
scroll |	内容会被修剪，但是浏览器会显示滚动条以便查看其余的内容。
auto |	如果内容被修剪，则浏览器会显示滚动条以便查看其余的内容。
inherit |	规定应该从父元素继承 overflow 属性的值。
具体设置如下
```css
overflow: hidden;
```
## 实现绝对定位元素水平垂直居中的两种方法
平时,用的方法即第一种方法是设置left,top值均为50%,同时margin-left设置为绝对定位元素width的一半取负,margin-top设为其height的一半取负。
例如，绝对定位元素的width:100px;height:100px;
代码如下:
```css
position:absolute;
left:50%;
top:50%;
margin-left:-50px;
margin-top:-50px;
```
这是比较常用的一个方法，今天介绍一个巧妙的方法，是利用了绝对定位元素的自动伸缩的特性实现的。

代码如下：
```css
position:absolute;
left: 0;
right: 0;
top: 0;
bottom: 0;
margin:auto;

```
上面就是第二种方法：设置margin:auto;设置left和right的值相等,top和bottom的值相等,
注意：left和right的值不能超过其相对元素width减去它自身width的一半,否则绝对定位元素会优先取left值进行定位(前提是文档流是从左向右),但是top和bottom的值却没有这个限制。