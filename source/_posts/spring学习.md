---
title: Spring学习
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

## Taco实践



### 问题

- 刚创建完design视图后，无法运行项目
  - 原因：DesignTacoController里面的`model.addAttribute("design",new Taco())`语句报错，没有Taco类，创建空类也无法打开视图
  - 解决方法：按书本后面的内容新建Taco类即可

## Spring相关注解

- `@SpringBootApplication`：组合注解，包括`@SpringBootConfiguration`，`@EnableAntoConfigration` 和`@ComponentScan`
- `@SpringBootConfiguration`：将该类声明为配置类，这个注解实际上是`@Configuration`注解的特殊形式
- `@EnableAntoConfigration`：启动Spring Boot的自动配置，告诉Spring Boot自动配置它认为我们会用到的组件
- `@ComponentScan`：启用组件扫描，Spring会发现并将它组成为Spring应用上下文中的组件
- `@RunWith(xxx.class)`：提供一个测试运行期(runner)来知道JUnit如何运行测试
- `@SpringBootTest`：告诉JUnit在启动测试的时候要添加上Spring Boot的功能
- `@Controller`：让组件扫描将这个这个类识别为一个组件，创建一个对应的实例作为Spring应用上下文中的bean
- `@WebMvcTest`：是SpringBoot提供的一个特殊注解，它会让这个测试在Spring MVC应用的上下文中执行
- `@Data`：lombok注解，告诉Lombok生成所有缺失的方法（Get，Set之类），同时还会生成所有已final属性作为参数的构造器
- `@SLF4J`：自动生成一个SLF4J(Simple Logging Facade for Java) Logger
- `@RequestMapping`：指定该处理器所处理的请求类型
- `@GetMapping`：结合类级别的@RequestMapping，指明当接收到类 + 自身路径级别的Get请求时，调用此方法
- `@PostMapping`：结合类级别的@RequestMapping，指明当接收到类 + 自身路径级别的Post请求时，调用此方法
- `@DeleteMapping`：结合类级别的@RequestMapping，指明当接收到类 + 自身路径级别的Delete请求时，调用此方法
- `@PutMapping`：结合类级别的@RequestMapping，指明当接收到类 + 自身路径级别的Put请求时，调用此方法
- `@PatchMapping`：结合类级别的@RequestMapping，指明当接收到类 + 自身路径级别的Patch请求时，调用此方法
- `@NotNull`：限定字段不能为空，message传递警告文字
- `@Size`：限定字段长度
- `@CreditCardNumber`：是否为合法的信用卡号
- `@Pattern`：是否满足正则表达式
- `@Digits`：限定数字位数
- `@`
- `@`
- `@`
- `@`
- `@`
- `@`
- `@`
- `@`

