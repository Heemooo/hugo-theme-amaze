+++
title = "IE6,7中JSON未定义"
description = ""
tags = [
    "IE兼容"
]
date = "2018-09-06"
categories = [
	"JavaScript"
]
+++
# IE6,7中JSON未定义
>问题：在IE6,7,8中使用JSON全局变量解析json数据时会报JSON未定义的异常，导致JavaScript终止运行。

*原因：* JSON是包含在JScript5.8引擎中，当IE8使用兼容模式时使用的是JScript5.7,因此要在IE8中使用JSON，需要声明文档为标准模式，
而在IE6,7则是因为本身不支持。

*解决方案：* 
1. 通过引入json2.js，Chrome等现代浏览器中存在JSON全局变量，而IE等老旧浏览器中则没有这个全局变量，json2.js的作用就是判断是否有JSON全局变量，如果没有就创建它。
2. 声明”Internet Explorer 8 Standards”模式(不需要兼容到ie6/7)： 
方法如下： 
在文档头添加
```
<meta http-equiv="X-UA-Compatible" content="IE=8" > 
使用<!DOCTYPE>来声明文档
```
3. 使用Window.evel()或new function(){}解析JSON格式字符串
```
 window.onload = function(){
    var info = document.getElementById("info"),
      jsonStr = '{"name": "w3help", "url": "www.w3help.org", "tech": ["js", "HTML", "CSS", 5, 4.01, 2.1],'
          + '"online": true, "category": {"RCA": "Root Cause Artical", "KB":"Knowledge Base"},'
          + '"version": 1}', p, w3help;
    //使用两种简单的方式解析 JSON 格式字符串
    json1 = eval("(" + jsonStr + ")"),
    json2 = (new Function("return " + jsonStr))();
    for(p in json1)
      info.innerHTML += p + " : " + json1[p] + "<br/>";
    info.innerHTML += "-----------------------<br />";
    for(p in json2)
      info.innerHTML += p + " : " + json2[p] + "<br/>";
  }
```
这个方法支持所有浏览器，但是存在安全问题，evel会执行json中的表达式，因此当被插入恶意json时有很大的安全隐患

参考文档:
1. [解决IE浏览器兼容性问题：“JSON”未定义](https://blog.csdn.net/liu865033503/article/details/78191521)
2. [IE6 IE7 不支持JSON](https://www.cnblogs.com/duanhuajian/p/3392856.html)