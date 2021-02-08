---
title: Mybatis的映射问题
date: 2021-02-08 12:15:22
tags:
 - Mybatis
---

## 前言

​	在使用 Mybatis 中 association 的时候，发现了映射时候的省略问题

<!-- more -->

## association的简单介绍

​	Mybatis 的 association 用于多对一的查询，即对象里面还有对象，那么这时候就需要用 association 去映射包含的对象了。

​	association 有两种用法，第一种是通过嵌套查询来映射对象，即再使用一个查询来查询映射的对象，如下

```xml
<select id="getStudentTeacher" resultMap="studentT">
    select sid,sname,sex,s.tid,tname from student s,teacher t where s.tid = t.id
</select>

<resultMap id="studentT" type="Student">
    <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
</resultMap>

<select id="getTeacher" resultType="Teacher">
    select * from teacher where id = #{id}
</select>
```

​	学生有对应的老师信息，通过数据库中的 tid（老师的id）来查询出每个学生对应的老师

​	还有一种用法就是，将被包含的对象的信息在 association 中映射出来，如下

```xml
<resultMap id="studentT" type="Student">
    <result property="sid" column="sid"/>
    <result property="sname" column="sname"/>
    <result property="sex" column="sex"/>
    <association property="teacher" javaType="Teacher">
        <result property="id" column="tid"/>
        <result property="tname" column="tname"/>
    </association>
</resultMap>
```

## 问题

​	在使用第二种方法的时候，遇到了一个问题，就是省略某个列的时候，该列会出现 null。Mybatis 是有自动映射功能的，如果映射的属性和数据库中的字段是同名的，那么 Mybatis会自动映射，不需要手动写。

​	但是在使用 association 的时候，不知道为什么，不会自动映射。

​	例如，在使用上述的第二种方法里面的实例代码进行查询的时候，查询的结果如下

```properties
Student(sid=1, sname=一号猪, sex=女, teacher=Teacher(id=1, tname=帅气杰))
Student(sid=2, sname=二号猪, sex=女, teacher=Teacher(id=1, tname=帅气杰))
Student(sid=3, sname=三号猪, sex=女, teacher=Teacher(id=1, tname=帅气杰))
Student(sid=4, sname=四号猪, sex=女, teacher=Teacher(id=2, tname=绅士杰))
```

​	假如 Mybatis 中自动映射是生效的，那么中间那些重名映射应该是可以去掉的，但是当你去掉以后，会发现对应的列变成了null，使用以下配置进行测试

```xml
<resultMap id="studentT" type="Student">
    <!--        <result property="sid" column="sid"/>-->
    <!--        <result property="sname" column="sname"/>-->
    <!--        <result property="sex" column="sex"/>-->
    <association property="teacher" javaType="Teacher">
        <result property="id" column="tid"/>
        <result property="tname" column="tname"/>
    </association>
</resultMap>
```

​	查询的结果如下：

```properties
Student(sid=0, sname=null, sex=null, teacher=Teacher(id=1, tname=帅气杰))
Student(sid=0, sname=null, sex=null, teacher=Teacher(id=1, tname=帅气杰))
Student(sid=0, sname=null, sex=null, teacher=Teacher(id=1, tname=帅气杰))
Student(sid=0, sname=null, sex=null, teacher=Teacher(id=2, tname=绅士杰))
```

​	而如果使用的是第一种，自动映射还是生效的，其实在第一种那里放的代码就已经使用了自动映射，我只自己手动配置了 tid -> teacher 的映射，其他的都是 mybatis 自动完成的

## 总结

​	目前我自己也不清楚原因，网上也没查到类似的问题以及解答，可能是我目前学的还不够，暂时搁置，记住这个问题就可以了