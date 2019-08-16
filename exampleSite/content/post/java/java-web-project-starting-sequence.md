+++
title = "JavaWeb项目启动加载顺序"
description = ""
tags = [
    "JavaWeb"
]
date = "2018-10-14"
categories = [
	"Java"
]
+++
# JavaWeb项目启动加载顺序
原文地址：[JAVA Web.xml 加载顺序  ](http://blog.163.com/qulei_lei/blog/static/186144231201141945740356/)

此处以javaweb servlet为例
1. 启动web项目时,web容器（如：tomcat）会先去读取web.xml配置文件中的两个节点：
```
<listener></listener>
<context-param></context-param>
```
2. 紧接着，容器创建一个ServletContext（上下文），这个web项目的所以部分将共享这个上下文
3. 容器将<context-param></context-param>转化为键值对，并交给ServletContext
4. 容器创建<listener></listener>中的类实例，即创建监听
5. 在监听中会有contextInitialized(ServletContextEvent args)初始化方法，在这个方法中获得
```
ServletContext servletContext = servletContextEvent.getServletContext();
context-param的值 = ServletContext.getInitParameter("context-param的键");  
```
6. 得到这个context-param的值之后，你就可以做一些操作。注意，这个时候你的WEB项目还没有完全启动完成。这个动作会比所有的Servlet都要早，换句话说，这个时候，你对<context-param>中的键值做的操作，将在你的WEB项目完全启动之前被执行。
例如： 你希望在项目启动之前就打开数据库，那么可以在<context-param>中设置数据库的连接方式，在监听类中初始化数据库的连接。
7. 这个监听是自己写的一个类，了初始化方法,它还有销毁方法。用于关闭应用前释放资源，比如说数据库连接的关闭
8. web.xml节点加载顺序：

可以肯定的是，节点的加载顺序与它们在 web.xml 文件中的先后顺序无关。即不会因为 filter 写在 listener 的前面而会先加载 filter。最终得出的结论是：listener -> filter -> servlet

同时还存在着这样一种配置节点：context-param，它用于向 ServletContext 提供键值对，即应用程序上下文信息。我们的 listener, filter 等在初始化时会用到这些上下文中的信息，那么 context-param 配置节是不是应该写在 listener 配置节前呢？实际上 context-param 配置节可写在任意位置，因此真正的加载顺序为：
```
context-param -> listener -> filter -> servlet
```
对于某类配置节而言，与它们出现的顺序是有关的。以 filter 为例，web.xml 中当然可以定义多个 filter，与 filter 相关的一个配置节是 filter-mapping，这里一定要注意，对于拥有相同 filter-name 的 filter 和 filter-mapping 配置节而言，filter-mapping 必须出现在 filter 之后，否则当解析到 filter-mapping 时，它所对应的 filter-name 还未定义。web 容器启动时初始化每个 filter 时，是按照 filter 配置节出现的顺序来初始化的，当请求资源匹配多个 filter-mapping 时，filter 拦截资源是按照 filter-mapping 配置节出现的顺序来依次调用 doFilter() 方法的。
```
  servlet 同 filter 类似，此处不再赘述。
```
##【加载spring】

 比如filter 需要用到 bean ，但加载顺序是： 先加载filter 后加载spring，则filter中初始化操作中的bean为null；
所以，如果过滤器中要使用到 bean，可以将spring 的加载 改成 Listener的方式 :

```
<listener>  
        <listener-class>  
             org.springframework.web.context.ContextLoaderListener   
        </listener-class>  
</listener>
```
    最终结论：
```
 web.xml 的加载顺序是：[context-param -> listener -> filter -> servlet -> spring] ，而同类型节点之间的实际程序调用的时候的顺序是根据对应的 mapping 的顺序进行调用的。
 ```