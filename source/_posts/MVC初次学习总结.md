---
title: MVC初次学习总结
date: 2019-04-18 14:53:13
categories: Java Web
tags: 
- Servlet
- MVC
---

已经一个多月没更新博客了，这一个月过的非常荒废，真应该好好反省一下。即使是放假或者忙别的事也应该保持总结自己学习经验的习惯。

前两天利用周末把MVC开发模式学习了一下，按照网上的教程实现了一个简单的登陆系统的开发，具体的源码和思路在下一篇博客。
这篇博客主要总结一下学习过程中踩得两个坑。

一、IDEA没有导入tomcat自带的servlet,jsp包
这是我第一次使用IDEA，在心间Servlet的时候发现了异常，Servlet本该自动import的各种包并没有导入，出现了很多报错。到网上一查原来是没有再IDEA导入tomcat自带的包
步骤：
1、右键项目
![](https://cdn.sinaimg.cn.52ecy.cn/large/005BYqpgly1g3iip18lp8j30g40ozac0.jpg)

2、导入jar包
![](https://cdn.sinaimg.cn.52ecy.cn/large/005BYqpgly1g3iiq2sim9j30yw0jzgnv.jpg)

二、利用注解方式配置Servlet
由于我看的教程是使用的Servlet2.5版本，并没注解配置这种方法，而我又太过于不求甚解，甚至没有上网查一下Servlet注解配置的用法，直接用了创建Servlet时默认的配置语句。

```java
@WebServlet(name = "LogoutServlet"）
```
其实这样只是配置了Servlet的映射名称，并没有配置具体路径。
```
@WebServlet(name = "LogoutServlet",urlPatterns = "/LogoutServlet")
```
才是正确的，具体更多的语法以后我还会再总结

话说IDEA好像是不能像Eclipse一样直接打开某个页面（Servlet/jsp），部署之后只会默认打开主页，只能在地址栏中加上相应页面的映射地址才能直接访问那个页面吗，也可能是我还没发现吧。



