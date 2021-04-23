Title: SpringMVC学习(篇章一)
Date: 2021-04-17 10:00
Category: Java
tags: java, springMVC
authors: Fanson
summary: 对springMVC学习的总结，篇幅一


### 1.SpringMVC框架的作用

MVC = Model（数据模型) + View(视图) + Controller(控制器)

SpringMVC框架主要解决了v与c的交互问题，也就是控制器如何接受客户断提交的请求，最终如何给予客户端响应

在JavaEE中，传统的控制器就是`Servlet`组件！假设某个项目中有注册功能、登陆功能、修改资料功能、修改密码功能...等等50个功能，应该需要50个`Servlet`组件来处理对应的请求！则项目运行时，就有50个`Servlet`对象常驻内存！在**web.xml**中还需要对每个`Servlet`组件进行注册，而每个`Servlet`组件至少需要8行代码来注册，则50个`Servlet`组件至少需要400行代码来注册...所以，这种传统的使用方式存在消耗内存略大，管理难度大等问题，甚至，很多API使用也并不方便！

### 2.SpringMVC

-`DispatcherServlet`：前端控制器，用于接收所有请求，并组织分发

-`HandlerMapping`：记录请求路径与控制器的对应关系;

-`Controller`：自定义的处理请求的控制器组件;

-`ModelAndView`：控制器组件处理完请求子后得到的结果，包含数据与试图名称;

-`ViewResolver`：试图解析器，可以根据试图名称，确定具体的试图组件;

![SpringMVC]({static}/images/SpringMVC.png){:width="500px" height="300px"}

### 3.SpringMVC HelloWorld

#### 3.1案例目标

在浏览器中输入`http://localhost:8080/项目名称/hello.do`能现实某个自定义的页面

#### 3.2创建项目

创建**Maven Project**, 勾选**Create a simple project**, **Group Id**为`cn.tedu`, **Artufact Id**为`SpringMVC01`, **Packaging**选择`war`。

注意：此处**Packagin**必须选择`war`。

项目创建出来后，生成**web.xml**，从前面项目中复制所需的以来，此次项目中必须添加的依赖只有`spring-webmvc`，并且，从前序项目中复制**spring.xml**文件到当前项目中，并删除原有的配置。

#### 3.3配置DispatcherServlet

由于先项目的第一步是由`DispathcerServlet`接收所有请求，它是一个框架中自带的`Servlet`组件，为了使得他能够接收所有的请求，首先，需要在**web.xml**中进行配置：

```
<servlet>
	<servlet-name>SpringMVC</servlet-name>
	<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>

<servlet-mapping>
	<servlet-name>SpringMVC</servlet-name>
	<url-pattern>*.do</url-pattern>
</servlet-mapping>
```

SpringMVC框架是基于Spring框架的，所以，还需要夹在Spring环境，即：读取Spring的配置文件，在`DispatherServlet`的父类`FrameworkServlet`中，就定义名为`contextConfigLocation`的属性，该属性的值就应该是Spring配置文件的位置，只要为该属性配置了值，创建`DispatcherServlet`对象的同时，就会加载Spring的配置文件！

所以，还需要在`<servlet>`节点下补充配置：

```
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring.xml</param-value>
    </init-param>
</servlet>

<servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <url-pattern>*.do</url-pattern>
</servlet-mapping>
```

最后，在`<servlet>`节点下补充配置初始化启动，即：当启动Tomcat时，就会创建`DispatcherServlet`的对象，进而加载Spring配置文件！

```
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>

<servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <url-pattern>*.do</url-pattern>
</servlet-mapping>
```

为了检验以上配置效果，可以现在**spring.xml**中添加配置：

```
<!--组件扫描-->
<context:component-scan base-package="cn.tedu.spring" />
```

然后，在组件扫描的包下创建类，在类之前添加`@Component`，并在构造方法中输出一句话：

```java
@Component
	public class User {
		
		public User() {
			System.out.println("创建User类的对象！");
		}
	
	}
```

最后，启动Tomcat，在控制台可以看到以上输出的内容！

该过程图示：
![]({static}/images/SpringMVC00.png) {:width="500px" height="300px"}


#### 3.4使用控制器接收客户端提交的请求

在组件扫描的`cn.tedu.spring`包创建`HelloController`控制类，并且在类之前添加`@Controller`注解：

```java
@Controller
public class HelloController {

}
```

注意：此处必须使用`@Controller`注解，不可以使用其他注解！

然后，在该控制器类中添加处理请求的方法，关于方法的声明原则：

1. 应该使用`public`权限;

2. 可以使用`String`类型作为返回值类型;

3. 方法的名称可以自定义;

4. 方法的参数列表可以为空。

所以添加的方法可以是：

```java
public String showHello() {

}
```

然后，在方法之前添加`@RequestMapping`注解，用于配置请求路径与该方法的对应关系，则后续`DispatcherServlet`收到这个路径的请求时，就会调用该方法：

```java
@RequestMapping("hello.do")
	public String showHello() {
		System.out.println("HelloController ShowHello");
		return null;
	}
```

完成后，重新部署该项目到Tomcat，打开浏览器，输入`http://localhost:8080/SpringMVC01/hello.do`，浏览器应该无法现实页面，但是，在Eclipse的控制台可以看到以上方法的输出内容！

如果在控制台提示`No Mapping Found`字样，并没有以上输出语句，则错误的原因可能有：

1. 控制器类根本不在组件扫描的包中;

2. 控制器类之前没有添加注解，或者，添加的不是`@Controller`注解;

3. 处理请求的方法之前没有正确的添加`@RequestMapping`注解;

4. 请求的路径与`@RequestMapping`注解中的路径不一致;

5. 尝试Clean项目，甚至Clean Tomcat，甚至更新Maven;

