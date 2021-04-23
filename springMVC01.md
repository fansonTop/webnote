Title: SpringMVC学习(篇章二)
Date: 2021-04-18 10:00
Category: Java
tags: java, springMVC
authors: Fanson
summary: 对springMVC学习的总结，篇幅二


### 3.SpringMVC HelloWorld

#### 3.5使用Thymleaf显示页面

当项目中需要使用Thymleaf模板时，就需要添加`thymleaf`的以来，并且，由于是应用在SpringMVC项目中，还需要添加`thymeleaf-spring4`的依赖：

```
<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf</artifactId>
    <version>3.0.11.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.thymeleaf</groupId>
    <artifactId>thymeleaf-spring4</artifactId>
    <version>3.0.11.RELEASE</version>
</dependency>
```

>以上依赖的备选版本有3.0.2, 3.0.9。

由于选的是Thymleaf模板技术来显示页面，所以，还应该创建爱嗯对应的HTML页面，这个页面将是客户端发出请求后，最后能够现实在浏览器中的页面！
