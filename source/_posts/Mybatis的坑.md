---
title: Mybatis的坑
date: 2021-02-04 16:07:52
tags:
 - Mybatis
---

## 前言

我会把我在学习Mybatis中遇到的所有坑都写到这里面

<!-- more -->

## 1. Maven的资源过滤

​	除了resource文件夹下的资源文件，Maven默认是不将其输出到target文件夹的，所以为了让 com.jeislu.dao 中mapper配置xml可以输入到target，需要设置Maven的pom.xml，具体设置和注意事项见代码

```xml
<!-- 在bulid中的resources中添加一个新的resouce，如果没有父标签就创建 -->
<build>
    <resources>
        <resource>
            <!-- 需要开启资源文件的目录 -->
            <directory>src/main/resources</directory>
            <!-- 匹配规则 -->
            <includes>
                <!-- 单一匹配规则 -->
                <!-- ** 表示任意目录 -->
                <!-- *.xml 表示任意以xml结尾的文件 -->
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```

## 2. 连接数据库问题

​	百度上说 mysql 8.0+版本需要配置时区，但是我这个是5啊，不知道是IDEA还是什么的原因，还是报错了，所以乖乖加上时区配置

​	在 mybatis的配置XML中连接数据库那一行URL中设置Timezone

```xml
<property name="url" value="jdbc:mysql://localhost:3306/mybatis?serverTimezone=GMT%2B8"/>
```

​	在粘贴的时候才发现我没有这个问题，不过IDEA连接 mysql 的时候需要这个，也先记起来了

## 3. SSL问题

​	如果在配置 mybatis 的时候，没有去设置SSL，那在使用的时候可能会出现

> Thu Feb 04 16:19:47 CST 2021 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.

​	这一行鲜艳的红色报错(其实不是错误，只是提示)，如果觉得碍眼，和上面时区一样，在配置 mybatis 的时候设置useSSL=false，如果设置成true会出现一个报错，暂时还不了解，如果某天懂了再回来改

```xml
<property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false"/>
```

## 4. Mapper没有配置问题

​	每一个 mapper.xml 都需要在 mybatis 的配置文件中注册，如果没有注册，就会出现

> org.apache.ibatis.binding.BindingException: Type interface com.jeislu.dao.UserMapper is not known to the MapperRegistry.

​	这个错误，在配置文件中注册就好了

## 5. Maven输出的中文乱码问题

​	这个是真的坑，浪费我好久，还以为是哪里出问题了，原来是不知道哪里的编码问题，导致 xml 文件中的字符集不是使用 UTF-8 ，然后在 target 下导致中文注释乱码

> Caused by: org.xml.sax.SAXParseException; lineNumber: 5; columnNumber: 15; 1

​	大致的错误如上，已经解决了，所以找不到之前完整的报错了。

​	解决方法就是，把IDEA中所有有关 UTF 和 GBK 的设置全部设置成为 UTF-8 ，由于改了很多个，具体是哪个的设置我也不清楚，不过里面有一个是 xml 不使用 utf-8 的设置，我把√去掉了，可能是这个（也可能不是）反正把和字符集相关的都设置成为 UTF- 8 就可以了

## 6. 增删改需要提交事务

​	如题，增删改需要提交事务，否则不生效

## 7.Mybatis配置顺序问题

​	properties标签需要放在配置XML的最上面，配置XML有顺序要求

## 8.Mapper 里面的 Class 和 Resource 的区别

- Class：会绑定配置的接口和 XML 文件
- Resource：只绑定配置的 XML 文件

因此，class可能会导致一个问题，方法已经加了注释，又在 XML 中编写了对应的 SQL 语句标签，出现这种情况，会报以下错误

> Error parsing SQL Mapper Configuration. Cause: java.lang.IllegalArgumentException: Mapped Statements collection already contains value for com.jeislu.dao.UserMapper.getUserList. please check com/jeislu/dao/UserMapper.xml and com/jeislu/dao/UserMapper.java (best guess)

