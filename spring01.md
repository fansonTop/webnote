Title: Spring学习（篇章二）
Date: 2021-04-11 01:20
Category: Java
tags: java, spring
authors: Fanson
summary: 对spring学习的总结篇幅二


### 8.Spring  Ioc

#### 8.1 什么是Ioc

Spring Ioc表示**控制反转**， 即在传统模式下，由程序员编写创建并管理对象，例如`User user = new User();`，当使用Spring框架之后，则将对象的创建、管理的权利交给了框架，则表示控制权给了框架，所以，称之为控制反转！

Spring框架是通过DI实现了IoC， DI（Dependency Injection）表示**依赖注入**。

#### 8.2 通过SET方法注入属性的值

在对象的运行过程中，需要使用另一个对象，在编写代码时，在一个类中需要生命另一个类的对象，则表现出了依赖关系！例如在'UserLoginServlet'中声明了`UserDao`类型的属性，则称之为**UserLoginServlet依赖于UserDao**。

使用Spring框架，不仅可以管理某个类的对象,还可以创建对象的同时，为该类中声明属性进行复制，这种复制操作就称为**注入**。

假设存在：

```java
public class User {
  public String name;
}
```

希望通过Spring获取该`User`类的对象时，对象的`name`属性已经有值！首先，需要为`name`属性添加规范的SET方法:

```java
public void setName(String name) {
  this.name = name;
}
```

然后在Spring的配置文件中，为配置`User`类的`Bean`节点添加子级的`property`节点：

```
<!-- name属性：需要被注入值的属性名 -->
<!-- value属性：需要注入的值 -->
<property name="name" value="David"></property>
```

>注意：在配置`property`节点时，`name`属性的值，其实，并不是类中的属性名，而是类中的方法名调整后的属性名，即将方法名称左侧的`set`去掉，即哪个字母改为小写后的名称！或者说，在Spring框架工作时，会根据配置的`property`中的`name`值，将首字母大写，并在左侧拼接上`set`，形成方法名，并调用该方法！

另外，可能还有一些类中的属性，并不是可以直接写出来的直接值，例如存在`UserLoginServlet`,其中有`UserDao`属性，如果需要为这个属性注入值，就需要使用`ref`属性引用另一个Bean:

```
<bean id="userDao" class="cn.tedu.spring.UserDao"></bean>
<bean id="userLoginServlet" class="cn.tedu.spring.UserLoginServlet">
<!--ref属性：引用另一个bean，该属性的值是另一个bean的id-->
<property name="userDao" ref="userDao"></property>
</bean>
```

java代码：

```java
public class UserLoginServlet {
  public UserDao userDao;

  public voud setUserDao(UserDao userDao) {
    this.userDao = userDao;
  }
}
```

>在注入属性值是，`value`和`ref`都表示值，其中`value`用于可以直接写出来的值，例如字符串类型的值，数值、布尔值，而`ref`用于引用另一个Bean，当使用`ref`属性时，取值为另一个Bean节点的`id`。

#### 8.3 通过构造方法注入属性的值【不常用】

假设存在：

```java
public class Person {
      public String from;
}
```

如果需要为以上`from`属性注入值，并且不实用SET方式来注入，则可以先添加带参数的构造方法，在构造方法中为`from` 赋值

```java
public class Person {
  public String from;

  public Person(String from) {
    super();
    this.from = from;
  }
}
```

以上使用的`<constructor-arg>`节点就是用于配置构造方法的参数的，如果构造方法中有多个参数，则需要多个该节点，每个节点1个参数！在该节点的`index`表示配置第几个参数，取值从0开始顺序编号！至于参数的值的配置，依然需要根据值的类型选择使用`value`或`ref`来配置！


### 9.注入集合类型的值

#### 9.1 注入List类型的值（不常用）

假设存在`SampleBean`的类，在类中有`List<String> names`属性，用于存储多个用户的名称，需要为该集合类型的属性注入值！
首先，需要为该属性添加SET方法:

```java
public class SampleBean {
  //Lucy, Jack, Kate, Tom
  public List<String> names;
  public void setName(List<String> names) {
    this.names = names;
  }
}
```

在配置时，由于使用的是SET方式注入，所以，依赖在`<bean>`节点下添加子级`<property>`节点，只不过这次在`<property>`中只配置`name`属性，并不配置`value`或`ref`属性，而是在子级添加`<list>`节点，并在`<list>`的子级添加若干个`<value>`节点以配置若干个值：

```
<bean id="sampleBean" class="cn.tedu.spring.SampleBean">
  <property name="names">
    <list>
      <value>Lucy</value>
      <value>Jack</value>
    </list>
  </property>
</bean>
```

#### 9.2注入SET集合类型的属性值（不常用）

假设在以上类型中存在`Set`集合类型的属性值，并需要注入值：

```java
public Set<String> cities;
public void setCities(Set<String> cities) {
  this.cities = cities;
}
```

然后，在spring配置文件中添加配置：

```
<property name="cities">
    <set>
        <value>Beijing</value>
        <value>Shanghai</value>
    </set>
</property>
```

>在Spring框架中处理`Set`类型的数据时，使用的是`LinkedHashSet`，在这种`Set`是使用链表的结构存储数据的，所以查询出来的数据的顺序与添加时是保持一直的！

#### 9.3注入数组类型的属性值（不常用）

假设在以上类中存在数组类型的属性，并需要注入值：

```java
public String[] skills;
public void setSkills(String[] skills) {
    this.skills = skills;
}
```

然后，在Spring配置文件中添加配置：

```
<property name="skills">
    <array>
        <value>JavaOOp</value>
        <value>JavaSE</value>
    </array>
</property>
```

> 在配置集合类型的属性时，配置`List`集合类型的属性，与配置数组类型的属性时，使用的节点结构可以随意，即：使用`<List>`节点，或使用`<array>`节点均可！

#### 9.4注入Map集合的属性值（不常用）

假设在以上类中存在`Map`类型的属性，并需要注入值：

```java
public Map<String, Object> session;
public void setSession(Map<String, Object>) {
    this.session = session;
}
```

然后，在Spring配置文件中添加配置：

```
<property name="session">
    <map>
        <entry key="username" value="Billy"/>
        <entry key="age" value="23"/>
    </map>
</property>
```

#### 9.5使用<util:xx>系列节点（不常用）

假设在以上类型中存在`List`集合类型的属性，并需要注入值：

```java
public List<String> tools;
public void setTools(List<String> tools) {
    this.tools = tools;
}
```

然后， 在spring配置文件中，先添加一个与`<bean>`节点相同级别的`<util:list>`节点：

```
<util:list id=tools>
    <value>Eclipse</value>
    <value>MySQL</value>
</util:list>
```

然后，需要注入属性值时，通过`ref`引用到以上配置的节点即可

```
<property name="tools" ref="tools" />
```

使用这种做法的配置可以将一部分代码从原本的`<bean>`的子级分离出来，虽然可能不太直观，但是，在代码篇幅较长的情况下，可以利于代码管理，另外，使用这种做法还可以将配置的集合的值进行复用！

另外，还有`<util:set>`和`<util:map>`节点可以配置`Set`和`Map`集合的值

#### 9.6注入Properties类型的属性值（常用）

假设在**src/main/resource**存在**jdbc.properties**文件，文件中有如下配置：

```
url=jdbc:mysql//localhost::3306/database?useUnicode=true&characterEncoding=utf-8
driver=com.mysql.jdbc.Driver
username=root
password=123456
```

需要将这些配置读取到程序中，则需要在类中声明`Properties`类型的属性：

```java
public Properties jdbcConfig;
public void setJdbcConfig(Properties jdbcConfig) {
    this.jdbcConfig = jdbcConfig;
}
```

在Spring的配置文件中，首先，使用`<util:properties>`节点读取以上**jdbc.properties**配置文件：

```
<!-- util:properties节点可以用于读取.properties类型的配置文件 -->
<!-- 该节点就是一个Properties类型的对象 -->
<!-- locations属性：读取哪个文件-->
<util:properties id="jdbcConfig" location="classpath:jdbc.properties"/>
```

然后，在类的`<bean>`节点子级，添加`<property>`节点注入属性的值：
```
<property name="jdbcConfig" ref="jdbcConfig"/>
```

当然，也可以不使用`.properties`文件， 而是直接将各个属性的配置注入到`<property>`级诶单的下级，例如：

```
<property name="jdbcConfig">
    <props>
        <prop key="username">administrator</prop>
        <prop key="password">12345678</prop>
    </props>
</property>
```

通常，还是推荐将这类配置写在`.properties`文件中，而不推荐以上写法！


### 10.Spring表达式

在配置Spring的配置文件时，可以通过Spring表达式实现**获取另一个bean的某个属性值**。

假设存在`ValueBean`类，其中`String name`属性的值需要来值`User`类中的`name`属性，如果使用SET方法为`ValueBean`的`name`属性注入值，首先，还是要添加对应的SET方法

```java
public class ValueBean {
    // 值：User类的对象的name属性值
    public String name;
    public void setName (String name) {
        this.name = name;
    }
}
```

然后，在Spring的配置文件中进行配置：

```
<bean id="valueBean" class="cn.tedu.spring.ValueBean">
    <property name="name" value="#{user.name"/}>
</bean>
```

Spring 表达式的基本格式使用`#{}`格式的占位符，需要使用`value`属性进行配置！
如果值来自另一个bean的属性，则Spring表达式的格式是：

```
{bean-id.属性名}
```

当然，值也可以是另一个bean的`List`集合中的某个元素，例如：

```java
// 值：SampleBean的names中的第三个值
public String username;
```

则注入值配置为：

```
<property name="username" value="#{sampleBean.names[2]}" />
```

所以，如果要获取另一个bean中的`List`集合中的某个元算，Spring表达式的格式是：

```
#{bean-id.属性名[索引]}
```

由于在Spring中注入值时，`Set`集合的实现类是`LinkedHashSet`，是以链表的形式存储数据的，所以，也可以取出这种集合中的第x个元素，取值方式与从`List`集合中取出某个值的做法完全相同！

另外，还可以获取某个`Map`类型的属性中的值，例如：

```
<property name="password" value="#{sampleBean.session.password"} />
```

所以，如果要获取`Map`中的值的Spring表达式的格式为：

```
#{bean-id.Map类型的属性名.Key}
```

另外还可以写成：

```
#{bean-id.Map类型的属性名['Key']}
```

通过以上方式，还可以获取`Properties`类型的属性的某个值

### ----------------------------------------------

##### 附1: 什么时候需要定义构造方法

1.创建对象的同时，快速的赋值；
2.限制对象的创建过程，例如在单例模式的设计中，将构造方法声明为私有权限
3.强制要求传入某些数据

##### 附2：关于集合的类型
