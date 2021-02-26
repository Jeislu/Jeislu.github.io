---
title: Spring学习
date: 2021-02-26 15:07:29
tags:
 - Spring
---

## 前言

​	Spring 的学习总记录

## 1 Spring

​	Spring框架是针对 **bean 的生命周期**进行管理的轻量级容器，是一个轻量级的**控制反转（IOC）**和 **面向切面编程（AOP）**的框架。

## 2 控制反转（IOC）

​	控制反转，其实主要理解什么被反转就可以了。

​	正常的设计中，各个对象之间相互协调，相互合作来实现业务逻辑，如下图所示

![各对象协调工作](Spring学习/1.png)

​	而在IOC中，引入了一个容器，各对象之间不再直接关联，而是通过容器来协调，如下图所示

![IOC容器](Spring学习/2.png)

​	引入 IOC 容器之前，如果 A 想使用 B，需要自己去创建一个 B，这时候 A 来直接控制 B 的创建。

​	而引入 IOC 容器以后，如果 A 想使用 B，去和 IOC 容器拿就可以了，这时候控制权反转了，B 的创建被 IOC 容器控制，由 IOC 容器创建 B 然后注入到 A 需要用到 B 的地方。

​	控制反转主要的功能就是，减少程序间的耦合度，它来控制对象的创建，然后在将控制的对象注入到所需的地方。也就是由 Spring 来创建、管理、装配对象。

## 3 依赖注入（DI）

​	依赖注入是实现控制反转的一种主要方式。

​	在 Spring 中，依赖注入有三种实现方式，分别是 构造器注入、Set注入、扩展方式注入。

### 3.1 构造器注入

​	所谓构造器注入，就是通过类的有参构造方法来将值注入到属性中，也就是这种方法必须要求对应类有对应的有参构造方法。

​	这里有几种配置的方式，第一种是通过下标来配置，假如我们的类如下

```java
public class Student {
    private String name;
    private int age;
    private String sex;

    public Student(String name,int age){
        this.name = name;
        this.age = age;
    }

    public void study(){
        System.out.println(age + "岁的" + name + "正在学习");
    }
}
```

​	则在 Spring 中的配置如下：

```xml
<bean id="student" class="com.jeislu.pojo.Student">
    <constructor-arg index="0" value="老钱"/>
    <constructor-arg index="1" value="20"/>
</bean>
```

​	这里面的下标就是形参的位置，很好理解吧。

​	那如果是有两个有参构造方法，都是两个参数，那么怎么办呢？例如下面这种情况

```java
public class Student {
    private String name;
    private int age;
    private String sex;

    public Student(int age,String sex){
        this.age = age;
        this.sex = sex;
    }

    public Student(String name,int age){
        this.name = name;
        this.age = age;
    }

    public void study(){
        System.out.println(age + "岁的" + name + "正在学习");
    }
}
```

​	这种情况，虽然都是两个参数，但是由于 age 是 int 的，所以会智能的选择第二个。

```properties
Student(name=老钱, age=20, sex=null)
```

​	那假如是下面这种情况呢

```java
public class Student {
    private String name;
    private int age;
    private String sex;
    private String love;

    public Student(String name,String sex){
        this.name = name;
        this.sex = sex;
    }

    public Student(String name,int age){
        this.age = age;
        this.name = name;
    }

    public void study(){
        System.out.println(age + "岁的" + name + "正在学习");
    }
}
```

​	这种情况，由于两个都适配，则Spring会默认选择第一个

```properties
Student(name=老钱, age=0, sex=20)
```

​	如果有这种情况，那么还是选择使用其他方式注入吧

​	第二种则是通过变量名注入，我们使用变量名来解决上面那个问题吧

​	配置如下

```xml
<bean id="student" class="com.jeislu.pojo.Student">
    <constructor-arg name="age" value="18"/>
    <constructor-arg name="name" value="务实"/>
</bean>
```

​	需要说的是，这里的 name 对应的是构造方法里面的形参变量名，必须一致，否则会报错。

​	配置完的运行结果为

```properties
Student(name=务实, age=18, sex=null)
```

​	第三种方法，则是通过类型注入，配置如下

```xml
<bean id="student" class="com.jeislu.pojo.Student">
    <!--type记得写全-->
    <constructor-arg type="java.lang.String" value="老王"/>
    <constructor-arg type="java.lang.String" value="男"/>
</bean>
```

​	注意，这里面是两个 String ，先配置的会注入到第一个参数里面。

​	这就是构造器注入的几种方式，一般来说，最常使用的是通过形参名注入，这种可以比较好的防范很多问题，依据具体情况使用即可。

### 3.2 Set注入

​	Set注入就是通过 Set 方法将值注入，其实和构造器区别不大，就是通过的方法不一样而已。还是使用上面那个类，由于我使用了 Lombok 所以没有看到我的 set 方法，这里发一遍不使用 Lombok。

```java
@NoArgsConstructor
public class Student {
    private String name;
    private int age;
    private String sex;

    public Student(String name,String sex){
        this.name = name;
        this.sex = sex;
    }

    public Student(String name,int age){
        this.age = age;
        this.name = name;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", sex='" + sex + '\'' +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getSex() {
        return sex;
    }

    public void setSex(String sex) {
        this.sex = sex;
    }
}
```

​	配置文件如下：

```xml
<bean id="student" class="com.jeislu.pojo.Student">
    <property name="age" value="19"/>
</bean>
```

​	输出：

```properties
Student{name='null', age=19, sex='null'}
```

​	不过有两个个注意点。

​	第一，在不使用构造器注入的时候，必须有无参构造器，因为 Spring 在创建 bean 的时候，默认就是调用类的无参构造方法（我上面看起来好像没有无参构造，其实上面那个@NoArgsConstructor帮我创建了一个无参了），否则会报错，错误如下：

> org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'student' defined in class path resource [beans.xml]: Instantiation of bean failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [com.jeislu.pojo.Student]: No default constructor found; nested exception is java.lang.NoSuchMethodException: com.jeislu.pojo.Student.< init >()

​	第二，set 方法名别乱改，一般我们的习惯是将属性名字大写然后在前面加个get，也就是 getAge()，如果你硬要改，会报错，错误如下(省略一些错误)：

> org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'student' defined in class path resource [beans.xml]: Error setting property values; nested exception is org.springframework.beans.NotWritablePropertyException: Invalid property 'age' of bean class [com.jeislu.pojo.Student]: Bean property 'age' is not writable or has an invalid setter method. Did you mean 'aage', or 'name'?

### 3.3 扩展方式注入

​	 扩展方式其实也是上面那两种，只不过用了更加简洁的语法而已，这里介绍一下 p 命名空间（property）注入 和 c 命名空间（constructor-args）注入

​	注意，无论是使用哪个，都需要编辑配置 XML 来提供支持

​	如果使用 p 命名空间，那么需要加入这一行在 XML 中 `xmlns:p="http://www.springframework.org/schema/p"`，如果是使用 c 命名空间，那么需要加入`xmlns:p="http://www.springframework.org/schema/c"`，下面给出加入 p 命名空间 以及 c 命名空间的配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```

​	p 命名空间的使用很简单，其实就是把`< property name="age" value="19"/ >`这个配置缩减罢了，原配置如下：

```xml
<bean id="student" class="com.jeislu.pojo.Student">
    <property name="age" value="19"/>
</bean>
```

​	使用 p 命名空间的配置如下：

```xml
<bean id="student" class="com.jeislu.pojo.Student" p:age="19"/>
```

​	c 命名空间同理，不过是用来替代构造器注入的，原配置如下

```xml
<bean id="student" class="com.jeislu.pojo.Student">
    <constructor-arg name="age" value="19"/>
    <constructor-arg name="name" value="老王"/>
</bean>
```

​	使用 c 命名空间的配置如下：

```xml
<bean id="student" class="com.jeislu.pojo.Student" c:age="19" c:name="老王"/>
```

​	不过 c 命名空间的东西稍微多了一个下标注入，就是原版本的缩减代码版本，理解就行

### 3.4 复杂注入

​	那问题来了，如果对象的属性是一个类、一个数组、一个集合或者一个 Map呢，那要怎么注入呢？

​	其实也不麻烦，一个一个说。

​	类的话，先给这个类创建一个Bean，然后将这个 Bean 注入到对应属性即可，相关类如下

```java
package com.jeislu.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.ToString;

@NoArgsConstructor
@AllArgsConstructor
@Data
@ToString
public class Student {
    private String name;
    private int age;
    private String sex;
    private Book loveBook;
}
```

```java
package com.jeislu.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@AllArgsConstructor
@NoArgsConstructor
@Data
public class Book {
    private String bookName;
}
```

​	上面两个类使用 Lombok，以后将不贴上 Lombok 注释和导入包信息	

​	相应的配置如下：

```xml
<bean id="student" class="com.jeislu.pojo.Student">
    <constructor-arg name="age" value="19"/>
    <constructor-arg name="name" value="老王"/>
    <constructor-arg name="sex" value="男"/>
    <!--将bean注入-->
    <constructor-arg name="loveBook" ref="wangLoveBook"/>
</bean>

<!-- 给对应类创建一个bean-->
<bean id="wangLoveBook" class="com.jeislu.pojo.Book">
    <property name="bookName" value="活着"/>
</bean>
```

​	如果注入的是数组的话，则改变一下注入写法即可，基础类型或者映射类型可以直接写在 constructor-arg 标签中，如果是复杂类型的话，则需要写在标签里面（有点绕，看代码）

​	POJO类：

```java
public class Student {
    private String name;
    private int age;
    private String sex;
    private String[] loveBook;
}
```

​	注入配置

```xml
<bean id="student" class="com.jeislu.pojo.Student">
    <constructor-arg name="age" value="19"/>
    <constructor-arg name="name" value="老王"/>
    <constructor-arg name="sex" value="男"/>
    <constructor-arg name="loveBook">
        <array>
            <value>活着</value>
            <value>许三观卖血记</value>
        </array>
    </constructor-arg>
</bean>
```

​	在 constructor-arg 里面加上一个 array 标签，然后使用 value 标签写上需要的注入的值即可

​	集合（List，set）类似，将上面书的类型从数组修改成集合，两者的配置分别如下

```xml
<bean id="student" class="com.jeislu.pojo.Student">
    <constructor-arg name="age" value="19"/>
    <constructor-arg name="name" value="老王"/>
    <constructor-arg name="sex" value="男"/>
    <constructor-arg name="loveBook">
        <list>
            <value>活着</value>
            <value>许三观卖血记</value>
        </list>
    </constructor-arg>
</bean>
```

```xml
    <bean id="student" class="com.jeislu.pojo.Student">
        <constructor-arg name="age" value="19"/>
        <constructor-arg name="name" value="老王"/>
        <constructor-arg name="sex" value="男"/>
        <constructor-arg name="loveBook">
            <set>
                <value>活着</value>
                <value>许三观卖血记</value>
            </set>
        </constructor-arg>
    </bean>
```

​	是的，就是将 array 标签换成 list 或者 set 即可，因为这两者就是特殊的数组

​	如果注入的是 Map 则稍微麻烦一点，如果需要注入的类如下所示

```java
public class Student {
    private String name;
    private int age;
    private String sex;
    private Map<String,String> love;
}
```

​	则注入配置如下：

```xml
<bean id="student" class="com.jeislu.pojo.Student">
    <constructor-arg name="age" value="19"/>
    <constructor-arg name="name" value="老王"/>
    <constructor-arg name="sex" value="男"/>
    <constructor-arg name="love">
        <map>
            <entry key="书籍" value="活着"/>
            <entry key="水果" value="芒果"/>
            <entry key="动漫" value="齐木楠雄"/>
        </map>
    </constructor-arg>
</bean>
```

​	还有一个 properties ，properties 的配置和 Map 和相似，但是有不一样的地方，配置如下所示

```xml
<bean id="student" class="com.jeislu.pojo.Student">
    <constructor-arg name="age" value="19"/>
    <constructor-arg name="name" value="老王"/>
    <constructor-arg name="sex" value="男"/>
    <constructor-arg name="love">
        <props>
            <prop key="书籍">活着</prop>
            <prop key="水果">芒果</prop>
            <prop key="动漫">齐木楠雄</prop>
        </props>
    </constructor-arg>
</bean>
```

​	Map 的键值对是在写标签中的，而 properties 是被标签包裹的，这是一个注意点

​	还有一个比较特殊的，null，用于赋空值，继续用上面那个例子，配置如下

```xml
<bean id="student" class="com.jeislu.pojo.Student">
    <constructor-arg name="age" value="19"/>
    <constructor-arg name="name" value="老王"/>
    <constructor-arg name="sex" value="男"/>
    <constructor-arg name="love">
        <null/>
    </constructor-arg>
</bean>
```

## 4 bean的作用域

​	在创建 bean 的时候，可以使用 scope 来设置 bean 的作用域，一共能设置五个值，不过有三个值只能在 web 开发中使用

​	

## Spring学习中的注意事项以及问题

