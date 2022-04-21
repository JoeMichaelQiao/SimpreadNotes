> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/radic_feng/article/details/6798378)

关键字：页面[重定向](https://so.csdn.net/so/search?q=%E9%87%8D%E5%AE%9A%E5%90%91&spm=1001.2101.3001.7020)（跳转，page redirection）、实现方式

写完标题，我突然想到了孔乙己，鲁迅先生嘲笑他以知道茴香豆的茴字有四种写法而感到自豪。不过我想我和孔乙己还是不同的，因为茴香豆的茴字会写一种即可，但本文总结的四种页面重定向的方式应用的场景不尽相同，产生的页面效果也不同。是有必要加以理解并选择合适的方式处理不同的场景的。

四种方式：

*   在 Server 端 sendRedirect
*   在 Server 端使用 jsp 或 RequestDispatcher 进行 forward
*   在 Browser 端使用 Javascript 进行重定向
*   在 Browser 端使用 html 标签进行重定向

以下是对四种方式进行的详解。

#### 在 Server 端 sendRedirect

****示例代码****

```
//HttpServletResponse response
response.sendRedirect("some url");
```

**特点**

1. 执行该语句后 server 会发送一个没有 body 的 http response。状态码（Status Code）为 302，在 response header 中有 Location 属性，值为：some url。浏览器接到该 response 后会自动读取 Location 的信息，并向指向的 URL 发出请求，所以你可以在地址栏看到 some url 了。

值得一提的是，如果第一个请求为 Post 请求时，现在很多浏览器会在第二个请求中转换为 Get 请求，这种处理方式并没有严格遵守 HTTP 协议。HTTP 协议对重定向的状态码作了细分，增加了 303（第二个请求转换为 Get 请求）和 307 码（第二个请求和第一个请求方法相同）。Eric Law 在[他的博文](http://blogs.msdn.com/b/ieinternals/archive/2011/08/19/understanding-the-impact-of-redirect-response-status-codes-on-http-methods-like-head-get-post-and-delete.aspx)中有更详细的解释。

2. 由于浏览器发出的第二个请求，因此 Location 中的 some url 可以跨域（Cross domain），为任意 URL。

3. 由于是两次独立的请求，所以两次请求不能共享 request 里面的数据。

4. 为访问目的资源，事实上浏览器发出了两个请求，效率较低。  

**页面效果**

1.  浏览器地址栏发生变化；
2.  由于第一次请求返回的 response body 为空，所以页面不会发生闪烁。  
    

**使用场景**

1.   期望让用户知道最终的 URL，以便加入书签（Bookmark）；
2.  在 web.xml 的 welcome file 配置中只能指定一个页面的 Location，可以使用一个简单的页面，比如 jsp，重定向到期望的 servlet；
3.  在 dispatcher 中根据用户身份重定向到各自的系统。  
    

#### 在 Server 端使用 jsp 或 RequestDispatcher 进行 forward

****示例代码****

JSP 中  

```
<jsp:forward page={"relativeurl" | "<%= expression %>"} >
	<jsp:param {parametervalue | <%= expression %>}" /><%--如果没有参数可以移除该语句 --%>
</jsp:forward> 

```

Java Code

```
request.getSession().getServletContext().getRequestDispatcher("Some url").forward(request, response);

```

**特点**

1. 在 Server 内部把请求转发给另一个资源，处理后把结果返回给浏览器，该操作对浏览器透明，因此地址栏的 URL 不会发生变化；

2. 两个资源共享 request 里的数据；

3. 转发到的 Some url 是本系统内部的资源，不能跨域。  

**页面效果**

1.  浏览器地址栏的 URL 不会发生变化；
2.  不会出现页面闪烁。  
    

**使用场景**

1.  服务器不期望用户了解服务器内部的资源结构信息；
2.  不跨域，以期更高的效率。  
    

#### 在 Browser 端使用 Javascript 进行重定向

**示例代码**

```
<script type="text/javascript">
window.location="Some URL";
</script>
```

**特点**

1. 浏览器已经成功得到所请求的页面了，浏览器在执行 JavaScript 时将地址栏的 URL 改变，以致向新的 URL 发出请求。  

**页面效果**

1.  浏览器地址栏的 URL 发生变化；
2.  如果第一次请求得到的页面不是空白，可以看到页面的闪烁。  
    

**使用场景**

1.  有的框架，比如 [Apache Tiles](http://tiles.apache.org/)，不支持 response.sendRedirect，只好用其他的办法了。。。

#### 在 Browser 端使用 html 标签进行重定向

****示例代码****

```
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
  <head>
      <title>Welcome to Radic's Blog</title>
      <meta http-equiv="refresh" content="0;url=<c:url value="/authenticate.html"/>" />
  </head>
  <body>Redirecting...</body>
</html>
```

0 表示页面被加载后发生跳转的时间，以毫秒为单位，0 毫秒表示立即执行跳转。后面的 url 指定跳转的目标 URL。参考[该](http://www.w3schools.com/tags/att_meta_http_equiv.asp)[文档](http://www.w3schools.com/tags/att_meta_http_equiv.asp)了解更多 meta http-equiv 的用途。

**特点**

1. 浏览器已经成功得到所请求的页面了，浏览器发现 meta http-equiv="refresh" 时将地址栏的 URL 改变，以致向新的 URL 发出请求。

**页面效果**

1.  浏览器地址栏的 URL 发生变化；
2.  如果第一次请求得到的页面不是空白，可以看到页面的闪烁。

**使用场景**

1.  有人会问，浏览器禁用了 JavaScript，我又不幸使用了 Tile 框架，怎么办呢？那就用这种方式把！