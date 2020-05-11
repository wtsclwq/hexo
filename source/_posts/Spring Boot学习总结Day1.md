---
title: Spring Boot学习总结Day1
date: 2020-02-05 00:39:05
categories: Java Web
tags:
- Spring Boot
---

博客已经闲置了很久了，这段时间我的学业基本上没有什么进步，学到的知识也很少。趁着寒假这段最后的空闲时光，学习一下Spring Boot，争取开学之前把博客系统做出来。

# 注解配置Spring IoC

## 1.使用@Bean装配

先创建一个Bean类——User

```java
package com.springboot.chapter2.beans;

public class User {
    private Long id;
    private String userName;
    private String note;

}
```

再创建一个配置文件——AppCofig

```java
@Configuration
public class AppConfig{
    @Bean(name = "user")//"user"作为bean的名称
    public User initUser(){
        User user = new User();
        user.setId(1L);
        user.setUserName("lwq");
        user.setNote("NiuZi");
        return user;
    }
}
```

最后就可以在主类中使用了。

```java
public class IoCTest {
    private static Logger log = Logger.getLogger(IoCTest.class);

    public static void main(String[] args) {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
        User user = (User) ctx.getBean(User.class);
        log.info(user.getNote());
    }
}
```

## 2.使用扫描装配Bean

使用@Component和@ComponentScan来装配Bean，能够以包为单位进行Bean的装配

首先创建Bean类——User

```java
package com.springboot.chapter2.beans;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component("user")
public class User {
    @Value("1")
    private Long id;
    @Value("user_name_1")
    private String userName;
    @Value("note_1")
    private String note;

}
```

然后创建配置文件——AppConfig

```java
@Configuration
@ComponentScan
public class AppConfig{}
```

将User类和AppConfig放在同一个包下，AppConfig就会自动装配其所在包中添加了@Component注解的Bean类

## 3.自定义扫描规则

将配置文件和Bean类放在他用一个包下显然不够合理，使用@ComponentScan注解的配置项，能够帮助我们自定义扫描的规则，即使是不同的包也能扫描，还能进行一些过滤。

在AppConfig的包的同一层，创建一个services包，其中添加一个UserService类，并为其添加一个@Service注释，这样在正常的扫描规则下，@Service类是会被扫描并装载的。

UserService类

```java
package com.springboot.chapter2.services;

import com.springboot.chapter2.beans.User;
import org.springframework.stereotype.Service;

@Service
public class UserService {
    public void printUser(User user) {
        System.out.println(user.getId());
        System.out.println(user.getUserName());
        System.out.println(user.getNote());
    }
}
```

这时我们需要修改AppConfig中的扫描规则

```java
@Configuration
@ComponentScan(basePackages = "com.springboot.chapter2.*",
        excludeFilters = {@Filter(classes = {Service.class})})
public class AppConfig{}
```

这样就会在扫描时，过滤掉chapter包中所有的Service类

## 4.问题总结

### （1）注解结尾不需要加分号

由于是太久没写代码，居然忘了注解后是不需要加分号的

错误示例：@Component("user")；

### （2）log4j版本问题

今天看书上的示例代码，使用log4j作为日志模块，但是在Maven加载依赖的时候，一直找不到对应的包，经过一番搜索，才找到比较常见的版本

````xml
       <groupId>log4j</groupId>  
       <artifactId>log4j</artifactId>  
       <version>1.2.15</version>  
       <exclusions>  
         <exclusion>  
           <groupId>javax.mail</groupId>  
           <artifactId>mail</artifactId>  
         </exclusion>  
         <exclusion>  
           <groupId>javax.jms</groupId>  
           <artifactId>jms</artifactId>  
         </exclusion>  
         <exclusion>  
           <groupId>com.sun.jdmk</groupId>  
           <artifactId>jmxtools</artifactId>  
         </exclusion>  
         <exclusion>  
           <groupId>com.sun.jmx</groupId>  
           <artifactId>jmxri</artifactId>  
         </exclusion>  
       </exclusions>  
       <scope>compile</scope>  
     </dependency> 
````

### （3）log42的配置问题

````
log4j:WARN Please initialize the log4j system properly.
log4j:WARN No appenders could be found for logger (com.netease.qa.testng.TestngRetry).
````

今天遇到了一个类似的异常，原来是没有为log4j添加log4j.properties。

如果是普通项目，那么就放在src目录下；

如果是maven项目，那么就放在resources目录中。

正确的文件内容：

````

log4j.rootLogger=debug, stdout, R
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout

log4j.appender.stdout.layout.ConversionPattern=%5p - %m%n

log4j.appender.R=org.apache.log4j.RollingFileAppender
log4j.appender.R.File=firestorm.log

log4j.appender.R.MaxFileSize=100KB
log4j.appender.R.MaxBackupIndex=1

log4j.appender.R.layout=org.apache.log4j.PatternLayout
log4j.appender.R.layout.ConversionPattern=%p %t %c - %m%n

log4j.logger.com.codefutures=DEBUG
````

