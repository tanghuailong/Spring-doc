### 11.开发你的第一个Spring boot应用
---
让我们开发一个简单的 "Hello World" Java web 应用，来展示Spring Boot 某些重要特性，我们将使用Mavan来构建这个项目，因为大多数IDEs 都支持它。


> Spring.io 站点中有很多Spring Boot 的 "Get Started" 指南， 如果你正在寻找某个问题的解决方案，请先参考它. 或者前往 start.spring.io 通过依赖搜索功能去选择一个web starter的依赖。这些依赖将会自动初始化一个项目结构，因此你可以在那里开始你的代码之旅。如果要了解更多细节请查看文档。


在开始之前，打开一个控制台去检查Java的版本和Maven安装情况。


```shell
$ java -version
java version "1.7.0_51"
Java(TM) SE Runtime Environment (build 1.7.0_51-b13)
Java HotSpot(TM) 64-Bit Server VM (build 24.51-b03, mixed mode

```


```
$ mvn -v
Apache Maven 3.2.3 (33f8c3e1027c3ddde99d3cdebad2656a31e8fdf4; 2014-08-11T13:58:10-07:00)
Maven home: /Users/user/tools/apache-maven-3.1.1
Java version: 1.7.0_51, vendor: Oracle Corporation

```

### 11.1 创建POM
我们需要创建一个Maven的pom.xml文件，这个文件是将是构建我们项目的秘诀。打开你最喜欢的文本编辑器，添加如下代码。



```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myproject</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.6.RELEASE</version>
    </parent>

    <!-- Additional lines to be added here... -->

</project>

```

这些应该能让你进行项目构建，你可以通过运行 `mvn package` 来检测。(目前你可以忽略掉 "Jar will be empty - no content was makred for inclusion" 这类警告)

> 此刻你可以把你的项目导入到IDE(大多数Java的IDE 都内建了对Maven的支持)。简单起见,这个例子我们将继续使用文本编辑器来进行操作

### 11.2 添加类路径依赖

为了更轻松的添加Jars包到你的类路径直线，Spring 提供了很多个“starters”。 我们现在这个例子已经在POM的`parent`部分使用了 `spring-boot-starter-parents`。`spring-boot-starter-parent` 是一个特别的Starter,它设置了许多有用的Maven默认配置，另外它还提供了`dependency-management` 部分，通过这个我们可以忽略[blessed](http://power1128.tumblr.com/post/94609602465/spring-boot%E4%B8%AD%E4%B8%BA%E4%BB%80%E4%B9%88%E5%BE%88%E5%A4%9Adependency%E4%B8%8D%E7%94%A8%E5%86%99%E7%89%88%E6%9C%AC%E5%8F%B7)依赖的版本号。

其他的一些"Starter"仅是提供依赖当你在开发某种类型的应用的时候。因为我们开发的是Web应用，我们会添加`spring-boot-starter-web` 依赖。但是在进行这个操作之前，让我们先来看一下这个项目的依赖数。


```
$ mvn dependency:tree

[INFO] com.example:myproject:jar:0.0.1-SNAPSHOT

```

`mvn dependency:tree` 命令将会把你的项目的依赖打印成一个依赖树,你可以看到 `spring-boot-starter-parent` 自身并没有提供依赖，让我们编辑`pom.xml` 文件并在`parent`节点下面添加`spring-boot-starter-web`的依赖。



```
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>

```

如果现在再运行 `mvn depenency:tree`，我们将会看见许多额外的依赖，包括Tomcat 和 Spring Boot它自己。

### 编写代码

为了完成我们这个应用，我们需要去创建一个 .java 文件， Maven 默认将会编译 `/src/main/java` ，所以我们需要去创建一个类似的目录结构并且添加一个名字叫 `src/main/java/Example.java` 文件:



```java
import org.springframework.boot.*;
import org.springframework.boot.autoconfigure.*;
import org.springframework.stereotype.*;
import org.springframework.web.bind.annotation.*;

@RestController
@EnableAutoConfiguration
public class Example {

    @RequestMapping("/")
    String home() {
        return "Hello World!";
    }

    public static void main(String[] args) throws Exception {
        SpringApplication.run(Example.class, args);
    }

}

```

虽然这里并没有太多代码，其实底下发生了很多事情，让我们逐步走进这一个重要的部分。

### 11.3.1 @RestController 和 @ RequestMapping 注解

首先在我们Example 类中第一个注解`@RestCOntr`是 一个 stereotype 注解，它给余毒代码的用户一个提示，这个类在spring 扮演着这特定的角色, 比如我们这个类就是一个web controller ，当请求过来的时候，spring认为它能够处理它。

`@RequestMapping` 注解提供了一个路由信息，它告诉任何以"/"开头的Http请求都会被对应到`home`这个方法上，`@RestController` 这个注解则告诉Spring给调用者渲染请求返回的字符串。


























