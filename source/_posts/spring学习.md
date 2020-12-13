---
title: spring学习
date: 2020-12-05 23:05:21
tags:
 - Spring
 - 笔记
---

## 前言

目前是未整理的版本，我会将我遇到的需要注意的地方写入这个文档，待全面学习完一个部分以后再整理

<!-- more -->

## Spring工程的创建

### IDEA创建

1. 新建项目，选择`Spring Initializr`，更换源为`https://start.aliyun.com/`，next
2. 根据自己需求修改配置，不知道有什么用就不要改，next
3. 添加四个，分别是`Spring web`，`Lombok`，`Thymeleaf`，`Spring Boot DevTools`
4. 完成

### vscode创建

1. 安装插件`Java Extension Pack`和`Spring Boot Extension Pack`
2. 在设置中搜索Maven，打开Maven的settings.json，根据自己更改（以下为我的配置）

```json
{
    "workbench.iconTheme": "material-icon-theme",
    "workbench.startupEditor": "newUntitledFile",
    "java.errors.incompleteClasspath.severity": "ignore",
    "workbench.colorTheme": "Atom One Dark",
    "java.home":"C:/Program Files/Java/jdk-15",
    "java.configuration.maven.userSettings": "D:\\Study\\Maven\\conf\\settings.xml",
    "maven.executable.path": "D:\\Study\\Maven\\bin\\mvn.cmd",
    "maven.terminal.useJavaHome": true,
    "maven.terminal.customEnv": [
        {
            "environmentVariable": "JAVA_HOME",
            "value": "C:/Program Files/Java/jdk-15"
        }
    ],
    "java.configuration.checkProjectSettingsExclusions": false,
    "editor.suggestSelection": "first",
    "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",
    "spring.initializr.serviceUrl": "https://start.aliyun.com/",
    "maven.excludedFolders": [
    
        "**/.*",
        "**/node_modules",
        "**/target",
        "**/bin"
    ],
}
```

3. 按`Ctrl + shift + p`搜索`Spring`，选择出现的`Spring Initializr: Create a Maven Project...`，接下来选择版本（选最新的就可以了），语言选择Java，然后是包名，项目名，最后添加四个包，和IDEA一样。
4. 完成

### 问题：

1. 官方源`https://start.spring.io`如果出现无法访问的情况，替换源为`https://start.aliyun.com/`，vscode创建Spring项目报错`Fail to create a project. Error: Failed to fetch metadata.`，一样是这个问题，更新Spring 初始化插件里面的源即可。