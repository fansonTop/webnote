Title: Spring学习(篇章一)
Date: 2021-04-03 10:20
Category: Java
tags: java, spring
authors: Fanson
summary: 对spring学习的总结，篇幅一

### 1.目录
Spring(2)
SpringMVC(3)
MyBatis(2)
AJAX(1)
SpringBoot(1)


### 2.Spring框架
框架（Framework):一系列的jar包，同时约定了实现某个功能的编程方式，可能于原有的编程方式并不相同！每个框架都解决了某些特定的问题！

Spring框架解决的问题：解决了创建和管理对象的问题，实现了解耦！

简单的创建对象的语法大致是
      User user = new User();
使用以上方式创建对象不便于实现解藕！
解藕：接触对象之间的耦合！即降低对象之间的依赖关系！
依赖：在一个对象的工作过程中需要使用到另一个对象，则称之为依赖另一个对象

例如:
```java
public class UserJdbcDao {
  public void login() {
    // 连接数据库
    // 执行SQL语句
  }
}

public class UserLoginServlet {
  public UserJdbcDao userJdbcDao = new UserJdbcDao();
  public void doPost() {
    userDao.login();
  }
}
```
在以上代码中`UserLoginServlet`就是依赖于`UserJdbcDao`的！

在以上代码的基础智商，如果`UserJdbcDao`使用的技术需要更换，或者出于其他的原因，这个类已经不再满足项目的需求，需要换成另一个类，或者使用另一种技术来实现，则可能创建新的`UserMyBatisDao`来替换原来的`UserJdbcDao`！

如果需要替换，则以上代码中`public UserJdbcDao userJdbcDao = new UserJdbcDao();`就需要被替换为：
    public UserMyBatisDao userMyBatisDao = new UserMyBatisDao();

如果这个项目中，有多个`Servlet`组建都使用了`UserJdbcDao`，则需要替换多次！

所以，如果要把`UserJdbcDao`替换为`UserMyBatisDao`，整个项目中需要调整的代码可能较多！通常，就称之为“耦合度太高”！

首先，可以定义一个接口

```java
public interface UserDao() {
  void login();
}
```

然后使得`UserJdbcDao`和`UserMyBatisDao`都实现以上接口！则后续需要使用DAO对象，可以声明为：

```java
// 使用接口声明，创建某个实现类的对象
public UserDao userDao = new UserMyBatisDao();
```

则后续需要替换时，该对象的声明语句是不用调整！

接下来，还可以使用设计模式中的工厂模式来解决创建对象的问题：

```java
public class UserDaoFactory {
  public static UserDaot newInstatnce() {
    reutrn new UserJdbcDao();
  }
}
```

则此前的代码可以进一步调整为：

```java
public UserDao userDao = UserDaoFactory.newInstatnce();
```

以上代码中根本就没有出现哪个实现类的名称，如果需要替换实现类，以上代码就根本不需要调整！需要调整的只有工厂类中的方法的返回值而已！

也可以看到，在`Servlet`类中，根本就不需要出现某个DAO类的名称，则可以称之为**UserLoginServlet不依赖UserJdbcDao或UserMyBatisDao**!也就接触了`UserLoginServlet`和`UserJdbcDao`或`UserMyBatisDao`之间的耦合度！

以上做法中，就使用到了接口、工厂类，它们是解藕的核心！但是在实际开发某个项目时，不可能为大量的类都去创建对应的接口与工厂类！所以，就出现了Spring框架！可以简单的将Spring框架理解为一个大型工厂，专门用于创建和管理项目中所需要的类的对象，当需要使用某个对象时，从Spring框架中获取即可，而不再是自行创建对象、自行编写工厂等！


### 3.通过无参数构造方法，使用Spring创建对象，并从Spring中获取对象

首先创建 **Maven Project**， 创建过程中，勾选**Create a simple Poroject**， **Group Id**填写`cn.tedu`, **Artifact Id**填写`Spring01`, **Packaging**选择`war`.

> 如果仅仅只是使用Spring，以上**Packaging**并不需要选择`war`,选择`jar`也是可以的

创建的项目默认没有**web.xml**文件，需要先生成该文件，否则，项目会报错！

创建完成后，需要在**pom.xml**中添加`org.springframework`依赖：

```
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.5</version>
</dependency>
```

> 此次所添加的依赖只要是4.2或以上版本均可！

> 如果只是使用Spring框架的内容，添加`spring-context`依赖即可，并不需要`spring-webmvc`由于后续继续学习SpringMVC框架，在`spring-webmvc`中包含了`Spring-context`，所以也可以直接使用`spring-webmvc`

然后下载**Spring配置文件**，解压后得到applicationcontext.xml，将该文件复制到项目的**src/main/resuorce**下。
[applicationContext.xml]({attach}/downloads/applicationContext.xml)

假设需要通过Spring创建`Date`类的对象，则在**applicationContext.xml**中添加配置：

```
<!-- 需要获取Date类的对象 -->
<!-- id属性：名称，后续将根据这个名称获取对象 -->
<!-- class属性：创建哪个类的对象 -->
<bean id="date" class="java.util.Date"></bean>
```

最后创建可运行的类，添加代码

```java
ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("applicationContext.xml");
Date date = (Date) context.getBean("date");
System.out.println(date);
context.close();
```

运行，即可查看到输出的日期。

以上做法需要该类存在无参构造方法！

### 4.通过静态工厂方法创建并获取对象【不常用】

某些类也许没有无参构造方法，但是，可能存在某个被`static`修饰的方法，并且该方法就返回当前类型的对象，例如`Calendar` 类中就存在：

```java
public static Calendar getInstance() {...}
```

如果需要使用Spring管理`Calendar`对象，则需要在**applicationCOntext.xml**中配置:

```
<!-- 2.通过静态工厂方法创建对象-->
<!-- factory-method属性：工厂方法的名称 -->
<bean id="calendar" class="java.util.Calendar" factory-method="getInstance"></bean>
```

以上`getInstance()`方法就是`Calendar`对象

### 5.通过实例工厂创建并管理对象

假设某个类并不满足以上2中方式创建对象，并且，存在另一个类，类中有某个方法可以创建对象，例如：

```java
public class UserDao {
  public UserDao(String s){

  }
}

public class UserDaoFactory {
  public UserDao newInstance() {
    return new UserDao(null);
  }
}
```

则配置为：

```
<!-- 3.通过实例工厂方法创建对象 -->
<!--factory-bean属性：工厂对象的bean-id -->
<!--factory-method属性：工厂方法的名称 -->
<bean id="userDaoFactory" class="cn.tedu.spring.UserDaoFactory"></bean>
<bean id="userDao" class="cn.tedu.spring.UserDao" factory-bean="userDaoFactory" factory-method="newInstance" ></bean>
```

### 6.由Spring管理对象的作用域 【仅了解】

由Spring管理的对象，默认都是单例

> 单例：在同一时间，某个类的对象一定只有一个！

如果需要配置对象是否单例，可以：


```
<!-- 属性scope: 配置类的对象的作用域，是否是单例，默认值是singleton， 当配置为prototype时表示非单例-->
	<bean id="user" class="cn.tedu.spring.User" scope="prototype"></bean>
```

如果是自行设计单例模式，可以是例如：
```java
public class User {
  private static User user = new User();

  private User() {

  }

  public static User getInstance() {
    return user;
  }
}
```
由于被获取的对象是使用了`static`修饰的，所以，单例的类的对象，都是静态的，也就是具有**常驻内存**的特点！则单例的对象的作用域就是整个程序加载这个列开始，知道程序结束！而非单例对象想的作用域就是普通的变量的作用域！

另外，单例模式还区分为**懒汉式单例**和**饿汉式单例**，如果是懒汉单例模式，只会在获取对象的前一刻才创建对象，如果是饿汉单例模式，则项目运行之初就已经创建了对象

默认情况下，由Spring管理的单例的对象都是**饿汉式的**！可以在Spring的配置文件中添加配置：

<!-- lazy-init属性：是否懒汉式加载，取值可以是布尔值 -->
<bean id="user" class="cn.tedu.spring.User" scope="singleton" lazy-init="true" />

**注意：必须是单例模式的基础之上，再讨论是否加载的问题！**

关于懒汉式单例的实现代码:
```java
public class User {

  private static User user;

  private static final Object lock = new Object();

  private User() {};

    public static User getInstance() {
      if (user == null ){
        sychronized(lock) {
          if (user == null) {
            user = new User();
          }
        }
      }
      return user;
    }
}
```
> 注：为什么要使用两个判空

### 7.由Spring管理的对象的生命周期【不常用】

对象的生命周期就是从创建到销毁的历程

以`Servlet`为例，从创建到销毁的过程中，还会执行`init()`、`service()`、`destroy()`方法，其中，`init()` 方法是创建对象之后就会立即执行的除数方法，并且只会执行1次，而`service()`方法是每次收到请求后都会执行的方法，可能执行若干次、`destroy()`方法是销毁对象的前一刻执行的方法，也会执行1次！

在java EE中的`Servlet`组建是由容器（例如Tomcat）创建并管理的！作为程序员，必须知道哪些方法在什么时候被调用，才可以正确的重写这些方法，从而决定“什么时候应该做什么事情”！

当使用了Spring框架后，对象的管理权也是交给了框架的！为了保证在初始化时能定制所需要执行的任务，在销毁之前能释放资源等，也可以在类中生命对应的生命周期方法：

```java
public class User {

  public User() {
    super();
    System.out.println("创建User对象！");
  }

  public void onCreate() {
    System.out.println("User.onCreate()");
  }

  public void onDestroy() {
    System.out.println("User.onDestroy()");
  }

}
```

在配置时，添加`init-method`和`destroy-method`属性的配置：

```
<!-- init-method：生命周期方法的初始化方法的名称 -->
<!-- destroy-method：生命周期方法的销毁方法的名称 -->
<bean id="user" class="cn.tedu.spring.User" init-method="onCreate" destroy-method="onDestroy" />
```