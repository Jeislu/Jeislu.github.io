---
title: Mybatis流程
top: 1
date: 2021-02-03 18:10:18
tags: 
 - Mybatis
---

## 前言

从零开始的Mybatis流程（大致）

<!-- more -->

## 流程

### 准备工作

​	新建一个Maven项目，在Maven项目中配置`MyBatis`，`mysql` 和 `Junit`三个模块

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- 父目录 -->
    <groupId>org.jeislu</groupId>
    <artifactId>MyBatis</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.46</version>
        </dependency>

        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>

        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>

</project>
```

​	将项目作为父项目，在它下面新建Maven模块来作为MyBatis的学习模块，子模块默认拥有父模块配置的东西，省去了以后多次创建多次配置

### 工具类

​	构建Mybatis使用的工具，主要是使用其工厂类SqlSessionFactory来生产SqlSession

​	使用XML的形式构建工厂类的生成器

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8&amp;serverTimezone=GMT%2B8"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
<!--    <mappers>-->
<!--        <mapper resource="org/mybatis/example/BlogMapper.xml"/>-->
<!--    </mappers>-->
</configuration>
```

​	编写方法，返回一个SqlSession对象

```java
package com.jeislu.DAO;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;

public class MybatisUtils {
    private static SqlSessionFactory sqlSessionFactory;
    // 构建工厂类
    static {
        // 配置文件的路径
        String resource = "org/mybatis/example/mybatis-config.xml";
        InputStream inputStream = null;
        try {
            inputStream = Resources.getResourceAsStream(resource);
        } catch (IOException e) {
            e.printStackTrace();
        }
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    }


    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }
}
```

### 实体类

​	编写对应表的实体类

```java
package com.jeislu.POJO;

public class User {
    private int id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }
}
```

### 数据库操作接口

​	根据需求编写数据库操作接口，然后配置对应 xml 资源文件（接口和配置最好同名），使用< select >< update > 等标签来实现方法，标签的 id 对应方法名。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace绑定一个Mapper接口 -->
<mapper namespace="com.jeislu.dao.UserMapper">
    <!-- 记得 resultType 写全名称 -->
    <!-- 查询全部用户 -->
    <select id="getUserList" resultType="user">
        select * from mybatis.user
    </select>

    <!-- 查询特定用户 -->
    <select id="getUser" parameterType="int" resultType="user">
        select * from user where id = #{id}
    </select>

    <!-- 增加特定用户 -->
    <insert id="addUser" parameterType="user">
        insert into user(id,name,pwd) values (#{id},#{name},#{pwd})
    </insert>

    <!-- 删除特定用户 -->
    <delete id="deleteUser" parameterType="int">
        delete from user where id = #{id}
    </delete>

    <!-- 修改特定用户 -->
    <update id="updateUser" parameterType="user">
        update user set name=#{name}, pwd=#{pwd} where id = #{id}
    </update>

    <!-- 使用map修改数据 -->
    <update id="updateUser2" parameterType="map">
        update user set pwd = #{password} where id = #{myid}
    </update>

</mapper>
```

​	在 mybatis 的配置XML中注册该Mapper

```xml
<mappers>
        <mapper resource="com/jeislu/dao/UserMapper.xml"/>
</mappers>
```

### 测试类

​	在测试类中测试相应功能是否可以使用