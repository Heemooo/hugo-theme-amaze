+++
title = "Python快速搭建web应用"
description = ""
tags = [
    "Bottle"
]
date = "2018-11-04"
categories = [
	"Python"
]
+++
# Python快速搭建web应用
> 人生苦短，我用python

最近闲着没事了解了一下python，发现python也支持web应用，以前我一直以为它是做爬虫的，了解了才知道我有多无知，大名鼎鼎的知乎，就是用python实现的。这里介绍的是python的Bottle框架，Bottle 是一个非常小巧但高效的微型 Python Web 框架，它被设计为仅仅只有一个文件的Python模块，并且除Python标准库外，它不依赖于任何第三方模块。

- 路由（Routing）：将请求映射到函数，可以创建十分优雅的 URL
- 模板（Templates）：Pythonic 并且快速的 Python 内置模板引擎，同时还支持 mako, jinja2, cheetah 等第三方模板引擎
- 工具集（Utilites）：快速的读取 form 数据，上传文件，访问 cookies，- - headers 或者其它 HTTP 相关的 metadata
- 服务器（Server）：内置HTTP开发服务器，并且支持 paste, fapws3, bjoern, Google App Engine, Cherrypy 或者其它任何 WSGI HTTP 服务器

## 创建项目
在PyCharm中创建一个python项目，很普通的那种，安装依赖
```
pip install bottle
```
当前的目录结构如下
```
  bottle_project
  --views
  --app.py
```
app.py代码如下
```python
import bottle
from bottle import run, view, Bottle
@app.route("/")
def main():
    return 'Hello World'
run(app, host='127.0.0.1')
```
运行代码，访问127.0.0.1:8080就实现一个简单的Hello World的例子啦，，Bottle支持普通路由和动态路由，动态路由可定制程度较高，有兴趣的可以自行了解一哈，这是单个路由，当路由多了想分成多个文件怎么办呢,新建login_route.py
```python
from bottle import template, Bottle, request
login = Bottle()
@login.route("/")
def index():
    return 'login'
```
然后在app.py中去注册新增的路由
```python
import login_route
app.mount('/login/', login_route.login)
```
访问http://127.0.0.1:8080/login/就可以查看到效果了
## 模板
Bottle 内置了一个快速且强大的模板引擎，叫作：*SimpleTemplate Engine* ，你可以使用 template() 函数 或者 view()装饰器来编译一个模板，你所要作的仅仅只是提供该模板，以及要传送给模板的数据，下面是一个模板的简单示例：
```python
@login.route("/")
def index():
    return template("login",name='123')
```
```html
<a>{{name}}</a>
```

## 模板路径
Bottle中默认设置的模板路径为```'./', './views/'``` ,但是在windows上使用的时候有可能会出现找不到模板的问题（我就遇到了），因此最好设置一下模板路径
```python
import os     
abs_app_dir_path = os.path.dirname(os.path.realpath(__file__))
abs_views_path = os.path.join(abs_app_dir_path, 'views')
bottle.TEMPLATE_PATH.insert(0, abs_views_path )
```