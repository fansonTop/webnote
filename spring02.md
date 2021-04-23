Title: Spring学习（篇章三）
Date: 2021-04-11 08:09
Category: Java
tags: java, spring
authors: Fanson
summary: 对spring学习的总结篇幅三


### 10.自动装配

自动装配： 不需要显示的为属性注入值，Spring框架会自动的将合适的值注入到属性中！

自动装配的做法：在Spring的配置文件中，为需要自动装配的`<bean>`添加`autowire`属性的配置，该配置的常用取有：`byName`和`byType`。

当取值为`byName`时，表示**根据名称实现自动装配**，即要求被注入的属性名称（严格的说，是该属性的规范SET方法名还原出来的属性名）与Bean-id是一致的！

当取值为`byType`时，表示**根据类型实现自动装配**，即要求被注入的属性类型与Spring管理的某个对象的类型是一致的！使用这种装配模式时，要求匹配类型的值只能有1个，如果有2个或更多，则无法装配，进而导致创建Spring容器失败！

自动装配的做法，本质上还是通过SET注入的方式来实现的！

这种自动装配的做法，其实是**尝试自动装配**，也就是说：如果某个属性可以被装配则装配，如果不满足装配条件（例如灭有SET方法，或没有可以装配的值），则放弃装配。

通过`autowire`属性实现自动装配存在明显的缺陷：某个类中的属性是否成功的装配了值，是不明确的！


### 11.通用注解

可以在Spring的配置文件中，配置组件扫描（Component-scan）：

```
<context:component-scan base-package="cn.tedu.spring"></context:component-scan>
```

注意：以上配置的属性`base-package`表示**根包**！，也就说，Spring框架除了扫描制定的这个包意外，还会扫描这个包的各层级子包！如果配置为`cn.tedu.spring.dao`，某个类在`cn.tedu.spring.dao`包之下，也会被扫描到！

后续，当加载Spring的配置时，就会扫描以上配置的包中的所有类，如果某些类是组件类，就会创建这些类的对象，反之，如果不是组件类，就不会创建这些类的对象！

如果要标记某个类是组件类，在类的声明之前添加`@Component`注解即可

```java
@Component
public class User {

}
```

这些由Spring创建的对象，默认bean-id就是类名首字母小写，例如`User`类的默认bean-id就是`user`，`Person`类的默认bean-id就是`person`！

也可以在注解中添加参数，自行指定bean-id，例如配置为：

```java
@Component
public class User {

}
```

以上代码就表示`User`类的bean-id就是`u`。

除此之外没，在类之前还可以使用`@Controller`、`@Service`、`@Repository`注解，在Spring的技术领域中，这些注解的作用和使用方式与`@Component`完全相同！

以上4个注解表达的语义并不相同！通常会在**控制器类**之前使用`@Controler`，在**业务类**之前使用`@Service`，在**处理持久化的类**之前使用`@Repository`，在没有其他组件之前使用`@Component`注解！


### 12.关于对象的作用域与声明周期的注解

可以在类之前添加`@Scope("protitype")`将其设置为**非单例**的：

```java
@Component
@Scope("prototype")
public class Person {
    public Person() {
        System.out.println("创建Perosn对象!");
    }
}
```

> 在类的声明之前可以添加多项注解，各注解不区分先后顺序！

在类之前添加`@Lazy`可以将其配置为**懒汉式单例**，例如：

```java
@Component
@Lazy
public class Person {
    public Person() {
        System.out.println("创建Person对象！");
    }
}
```

也可以在方法之前添加`@PostConstruct`和`@PreDestroy`注解，分别表示**初始化方法**和**销毁方法**:

```java
@PostConstruct
public void onCreate() {
    System.out.println("Person.onCreate()");
}

@PreDestroy
public void onDestroy() {
    System.out.println("Person.onDestroy()");
}
```

注意：以上2个注解是`javax.annotation`包中的注解，在使用之前，需要在项目属性的支行环境中勾选Tomcat。


### 13.自动装配的注解

在使用注解的开发模式下，如果类中的某个属性需要自动装配值，在属性的声明之前添加`@Autowired`注解即可：

```java
@Component
public class UserLoginServlet {

    @Autowired
    public UserDao userDaol

    public void doPost() {
        System.out.println("UserLoginServlet.doPost()");
        userDao.login();
    }
}
```

当添加该注解后，项目运行时，创建该类的对象时，检测到有自动装配的注解，就会从Spring容器中查找匹配的对象，并赋值给该属性！

**注意：自动装配的前提是当前类和需要使用到的值都是由Spring管理的！如果Spring根本不管理当前类，就更不可能自动装配了，并且，属性的值也是在Spring容器中的！**

另外，使用`@Resource`注解也可以实现自动装配！


### 14.Spring阶段小结

1. 理解Spring框架的作用：创建与管理对象，实现解藕;

2. 掌握使用`<bean>`节点配置某个类，使用Spring框架创建管理这个类的对象

3. 理解单例模式，了解单例模式的写法;

4. 了解有Spring管理的对象的作用域（是否单例，是否懒加载），并了解如何调整作用域;

5. 了解配置Spring管理的对象的类的生命周期方法;

6. 掌握通过SET方式为类中的属性注入值;

7. 了解通过构造方法为属性注入值;

8. 了解为数组、`List`、`Set`、`Map`类型的属性注入值;

9. 掌握通过Spring读取`.properties`文件;

10. 了解自动装配的`byName`和`byType`装配方式的特点;

11. 掌握配置组件扫描;

12. 掌握`@Component`、`@Controller`、`@Service`、`@Repository`注解的使用;

13. 了解`@Scope`、`@Lazy`、`@PostConstruct`、`@PreDestroy`注解的使用;

14. 掌握并理解`@Autowired`和`@Resource`注解的使用;

15. 理解**解藕**, **依赖**, **IoC**,**DI**名词;

16. Spring AOP。