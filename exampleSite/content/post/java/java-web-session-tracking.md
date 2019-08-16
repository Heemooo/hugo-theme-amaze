+++
title = "JavaWeb会话跟踪"
description = ""
tags = [
    "JavaWeb"
]
date = "2018-10-14"
categories = [
	"Java"
]
+++
# JavaWeb会话跟踪
>岂能尽如人意,但求无愧于心

## 会话跟踪的需求

????HTTP是“无状态”协议：客户程序每次读取 Web 页面，都打开到 Web 服务器的单独的连接，并且，服务器也不自动维护客户的上下文信息。即使那些支持持续性 HTTP 连接的服务器，尽管多个客户请求连续发生且间隔很短时它们会保持 socket 打开，但是，它们也没有提供维护上下文信息的内建支持。上下文的缺失引起许多困难。例如，在线商店的客户向他们的购物车中加入商品时，服务器如何知道购物车中己有何种物品呢？类似地，在客户决定结账时，服务器如何能确定之前创建的购物车中哪个属于此客户呢？这些问题虽然看起来十分简单，但是由于
HTTP 的不足，解答它们却异常复杂困难。

## 会话跟踪解决之道

### 一．Cookie
????Java中把Cookie封闭成了javax.servlet.http.Cookie类。每个Cookie都是该Cookie类的对象。服务器端通过操作Cookie类对象对客户端Cookie进行操作。通过request.getCookie()获取客户端提交的Cookie（以Cookie[]数组形式返回），通过Response.addCookie(Cookie cookie)向客户端设置Cookie。

????Cookie对象使用key-value属性对的形式保存用户状态。一个Cookie对象只保存一个属性对，一个request或者response同时使用多个Cookie。服务器端只能获取当前用户的Cookie。各客户端的Cookie彼此独立，互不可见。
#### 编码问题
????**使用Unicode编码：保存中文**????中文与英文字符不同，中文属于Unicode字符，在内存中占4个字符，而英文属于ASCII字符，内存中只占2个字节。Cookie中保存中文只能编码，一般使用UTF-8编码即可。
#### 图片存储问题
????**使用BASE64编码：保存二进制图片**
#### Cookie的修改删除
????Cookie并不提供修改、删除操作。如果要修改某个Cookie，需要新建一个同名的Cookie,并添加到response中覆盖原来的Cookie。如果要删除一个Cookie,只需要新建一个同名的Cookie，并将maxAge设置为0，并添加到response中覆盖原来的Cookie。修改、删除Cookie时，新建的Cookie除value、maxAge之外的所有属性，都要与原Cookiep完全一样。

### 二．Session
#### Session的定义
????Session是另一种记录客户状态的机制，不同的是Cookie保存在客户端浏览器中，而Session保存在服务器上。客户端浏览器访问服务器的时候，服务器把客户信息以某种记录在服务器上。这就是Session。客户端浏览器再次访问时只需要从该Session中查找该客户的状态就可以了。

????每个来访者对应一个Session对象，所有的该客户的状态信息都保存在这个Session对象中。Session对象是在客户端第一次请求服务器的时候创建的。Session也是一种key-value的属性对，通过getAttribute(String key)和setAttribute(String key, Object value)访求读写客户状态信息。Servlet里通过request.getSession()方法获取该客户的Session。request.getSession(Boolean create)来获取Session,如果该Session不存在，则创建。

????Session在用户第一次访问服务器的时候自动创建。需要注意只有访问JSP、Servlet等程序时才会创建，只访问HTML、IMAGE等静态资源时不会创建Session。

????HTTP协议是无状态的，Session不能依据HTTP连接来判断是否为同一用户，因此服务器向客户端发送一个名为JSESSIONID的Cookie,它的值为Session的id(也就是HttpSession.getId()的返回值)

### 三．URL重写
????URL地址重写是对客户端不支持Cookie的解决方案。URL重写的原理是将该用户Session的id信息写到URL地址中。服务器能够解释重写后的URL获取Session的id。这样即使客户端支持Cookie,也可以使用Session来记录用户的状态。HttpServletResponse类提供了encodeURL(String url)实现URL重写。该方法能够自动判断客户端是否支持Cookie。如果支持会将URL原封不动    输出，不支持则会把用户的Session的id重写到URL中。

????浏览器第一次访问服务器时，请求中不带Cookie。

### 四．隐藏表单域
????是一种最简单的方式，将字段隐藏在HTML表单中，但不在客户端显示。比如在第一张页面中输入用户名和密码登陆，服务器生成响应返回第二张页面。当第二张页面提交时可能仍然需要知道来自第一张页面中的用户名。

????那么就可以通过隐藏表单域来实现这一连续的过程。当第一张页面提交后，服务器端作出响应返回第二张页面，此页面中用隐藏域记录了来自登陆时的用户名。通俗说就是当服务器回发给客户端的响应中，就同时把用户名再次回发到客户端，用隐藏域隐藏起来，是不可见的。当第二张页面提交时，此隐藏域中的用户名一并随表单提交。这样服务器就仍然可以判断此用户是否与以前的用户相同。于是，再次处理完结果后继续将响应回发给客户端，且此响应中也仍然包含了用户名，在客户端中仍然用隐藏域将这一信息隐藏。