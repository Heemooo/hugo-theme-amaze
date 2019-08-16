+++
title = "JavaScript中匿名函数和普通函数的区别"
description = ""
tags = [
    "function"
]
date = "2018-10-15"
categories = [
	"JavaScript"
]
+++
# JavaScript中匿名函数和普通函数的区别
> 在使用JavaScript定义方法时，常常使用两种方法，方法一：var test=function(){},方法二：function test(){}，但是这两种方法有什么区别呢？最关键的区别在于JavaScript 函数和变量声明的“提前”（hoist）行为.

## 普通方法
```JavaScript
function test(){
  return "1";
}
console.log(test());//结果：2
function test(){
  return "2";
}
```
结果竟然是2，完全没有按照顺序来，看起来貌似非常不合理。 
因为编译后函数声明和他的赋值都会被提前。函数声明过程在整个程序执行之前的预处理就完成了，所以只要处于同一个作用域，就可以访问到，即使在定义之前调用它也可以。
## 匿名方法
```
var test=function(){
  return "1";
}
console.log(test());//结果：1
test=function(){
  return "2";
}
```
这种方式编译后变量声明会被“提前”，但是它的赋值，也就是方法体未被提前，在调用时初始化。这也是the Google style guide建议的使用方法。
参考连接[link](https://www.zhihu.com/question/19878052)