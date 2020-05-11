---
title: servlet疑惑总结（一）
date: 2019-04-14 16:13:29
categories: Java Web
tags: 
- Servlet
---

## 　一、${pageContext.request.contextPath}

在学到使用Request获取Form表单提交的数据的时候，在Form的action属性中看到了“${pageContext.request.contextPath}”的表达式，查了一会才知道这属于EL表达式的一种写法：

从get方法中获取数据：
pageContext.getRequest()得到HttpServletRequest对象，这是第一步，第二步是利用拿到的HttpServletRequest调用getContextPath方法。
作用：取出部署的应用程序名，这样不管如何部署，所用路径都是正确的。

也就是说：
**${pageContext.request.contextPath} = <%=request.getContextPath()%> =“ /项目名称”**

## 二、href="javascript:void(0);"
href=”javascript:void(0);”这个的含义是，让超链接去执行一个js函数，而不是去跳转到一个地址，
而void(0)表示一个空的方法，也就是不执行js函数。

javascript:是伪协议，表示url的内容通过javascript执行。void(0)表示不作任何操作，这样会防止链接跳转到其他页面。这么做往往是为了保留链接的样式，但不让链接执行实际操作，

与之作用类似的是href="#"但是这种方式会导致点击链接之后网页回到底部，href="javascript:void(0);"则不会产生这种副作用

```
<a href="javascript:void(0)" onclick="changeImage()">看不清，换一张</a> 
```
上面的代码就是这种方法的应用

## 三、request.getRemoteAddr()取得的是IPv6的地址格式

request.getRemoteAddr()方法是用来取得客户端的IP地址的，目前来说我们都是使用IP V4的地址格式，但是我在将本机作为服务器的时候，取得的IP底值居然是0:0:0:0:0:0:0:1。

于是我在网上查到了两种解决方法

##### 1、修改hosts文件。位置：C:\Windows\System32\drivers\etc

删除或者注释掉 " ::1 localhost"这一句，但是我的机器本来就是注释掉的，而且我取消掉了"127.0.0.1 localhost"也还是无济于事。

##### 2、使用127.0.0.1代替localhost进行访问

也就是在地址栏里将localhost手动换成127.0.0.1，这种方法虽然有效但是太麻烦了

##### 3、让tomcat只支持IPv4

**Run->Run Configurations...->Apache Tomcat->Tomcat v7.0 Server at localhost-> Arguments->VM arguments**
在运行参数中添加：``` -Djava.net.preferIPv4Stack=true```
重启Tomcat在使用request.getRemoteAddr()方法就能得到IPv4的地址了
具体如图

![](https://ws3.sinaimg.cn/large/005BYqpgly1g229o80bxwj30u50se0v1.jpg)

## 四、doGet()方法与doPost()方法以及get、post

##### 1.form运行方式

当form框里面的method为get时，执行doGet方法
当form框里面的method为post时，执行doPost方法

##### 2.生成方式

get方式有四种：

1）直接在URL地址栏中输入URL。

2）网页中的超链接。

3）form中method为get。

4）form中method为空时，默认是get提交。

post只知道有一种：

form中method属性为post。

##### 3，数据传送方式

get方式：表单数据存放在URL地址后面。所有get方式提交时HTTP中没有消息体。

post方式：表单数据存放在HTTP协议的消息体中以实体的方式传送到服务器。

##### 4、服务器获取数据方式

GET方式：服务器采用request.QueryString来获取变量的值。

POST方式：服务器采用request.Form来获取数据。

##### 5、传送的数据量

GET方式：数据量长度有限制，一般不超过2kb。因为是参数传递，且在地址栏中，故数据量有限制。

POST方式：适合大规模的数据传送。因为是以实体的方式传送的。

##### 6、安全性

GET方式：安全性差。因为是直接将数据显示在地址栏中，浏览器有缓冲，可记录用户信息。所以安全性低。

POST方式：安全性高。因为post方式提交数据时是采用的HTTP post机制，是将表单中的字段与值放置在HTTP HEADER内一起传送到ACTION所指的URL中，用户是看不见的。

##### 7、在用户刷新时

GET方式：不会有任何提示、

POST方式：会弹出提示框，问用户是否重新提交。



在做数据查询时，建议用Get方式；而在做数据添加、修改或删除时，建议用Post方式。

**通常doPost()方法更强大一些，所以如果是Post请求，就直接重写doPost()方法就好了，** 
**而如果是Get请求，也可以把方法体写在doPost()中，在doGet()中通过doPost(request,response);调用一下即可。**