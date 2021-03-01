---
title: Maven镜像仓库
date: 2021-02-28 09:19:22
tags:
 - Maven
---

## 前言

​	如果不配置镜像仓库使用 Maven，Maven 的下载速度实在是太慢了。本文保存一些 Maven 的镜像仓库。

<!-- more -->

## 镜像仓库

​	刚开始用 Maven 的时候，下载速度实在是太慢了，然后就去网上找镜像仓库，最后找到了应该算比较常用的一些镜像仓库，如下

```xml
<mirror>  
    <id>alimaven</id>  
    <name>aliyun maven</name>  
    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>  
    <mirrorOf>central</mirrorOf>          
</mirror>
<mirror>
    <id>ui</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>http://uk.maven.org/maven2/</url>
</mirror>
<mirror>
    <id>jboss-public-repository-group</id>
    <mirrorOf>central</mirrorOf>
    <name>JBoss Public Repository Group</name>
    <url>http://repository.jboss.org/nexus/content/groups/public</url>
</mirror>
<mirror>
    <id>repo2</id>
    <mirrorOf>central</mirrorOf>
    <name>Human Readable Name for this Mirror.</name>
    <url>http://repo2.maven.org/maven2/</url>
</mirror>
<mirror>
    <id>OSChina</id>
    <name>OSChina Central</name>
    <url>http://maven.oschina.net/content/groups/public/</url>
    <mirrorOf>central</mirrorOf>
</mirror>
<mirror>
    <id>nexus-osc-thirdparty</id>
    <mirrorOf>thirdparty</mirrorOf>
    <name>Nexus osc thirdparty</name>
    <url>http://maven.oschina.net/content/repositories/thirdparty/</url>
</mirror>
```

## 使用方法

​	在你的 Maven 目录下的 conf 目录下，存在一个 settings.xml 文件，打开该文件，将上面的内容复制到文件里面的 < mirrors >标签里面即可，好像没配置过镜像仓库的，这个标签默认是被注释掉的或者不存在，自己添加一个即可，完成的情况应该如下

```xml
<mirrors>
    <mirror>  
        <id>alimaven</id>  
        <name>aliyun maven</name>  
        <url>http://maven.aliyun.com/nexus/content/groups/public/</url>  
        <mirrorOf>central</mirrorOf>          
    </mirror>
    <mirror>
        <id>ui</id>
        <mirrorOf>central</mirrorOf>
        <name>Human Readable Name for this Mirror.</name>
        <url>http://uk.maven.org/maven2/</url>
    </mirror>
    <mirror>
        <id>jboss-public-repository-group</id>
        <mirrorOf>central</mirrorOf>
        <name>JBoss Public Repository Group</name>
        <url>http://repository.jboss.org/nexus/content/groups/public</url>
    </mirror>
    <mirror>
        <id>repo2</id>
        <mirrorOf>central</mirrorOf>
        <name>Human Readable Name for this Mirror.</name>
        <url>http://repo2.maven.org/maven2/</url>
    </mirror>
    <mirror>
        <id>OSChina</id>
        <name>OSChina Central</name>
        <url>http://maven.oschina.net/content/groups/public/</url>
        <mirrorOf>central</mirrorOf>
    </mirror>
    <mirror>
        <id>nexus-osc-thirdparty</id>
        <mirrorOf>thirdparty</mirrorOf>
        <name>Nexus osc thirdparty</name>
        <url>http://maven.oschina.net/content/repositories/thirdparty/</url>
    </mirror>
</mirrors>
```



