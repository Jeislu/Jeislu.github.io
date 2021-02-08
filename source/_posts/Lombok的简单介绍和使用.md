---
title: Lombok的简单介绍和使用
top: 1
date: 2021-02-07 23:53:38
tags:
 - Java
 - Lombok
---

## 前言

​	Lombok的简单介绍和使用

<!-- more -->

## 介绍

​	Lombok是一个Java库，可以自动插入到编译器和构建工具中，只需要配置标签，就可以自动为 Java 类生成 getter 和 setter 等方法(还有其他功能，但是主要用这个功能)

​	也就是不用再为每一个POJO类编写重复的 Getter 和 Setter 之类的方法

## 使用

​	在Maven仓库中搜索Lombok即可，将相应的配置代码复制到Maven项目的 pom.xml 文件即可，下面给出一个较多人使用版本的配置代码

```xml
<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.12</version>
</dependency>
```

​	然后在对应的 POJO 类加上注释即可，常用的注释有以下几个

- @Data：自动生成 get 、set 、equal、hashcode 和 toString 方法（因为没有构造方法，Java会自己生成一个构造方法，这是 Java基础的东西）
- @AllArgsConstructor：生成一个满参的构造方法（因为有带参的构造方法，所以无参构造方法不再自己生成）
- @NoArgsConstructor：生成一个无参的构造方法
- @Getter：生成 get 方法
- @Setter：生成 set 方法

​    还有几个类似Getter的tostring注释，类似的效果，但是其实一般都是用前三个