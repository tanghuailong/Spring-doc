#
## 11.开发你的第一个Spring boot应用
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

首先在我们Example 类中第一个注解`@RestController`是 一个 stereotype 注解，它给余毒代码的用户一个提示，这个类在spring 扮演着这特定的角色, 比如我们这个类就是一个web controller ，当请求过来的时候，spring认为它能够处理它。

`@RequestMapping` 注解提供了一个路由信息，它告诉任何以"/"开头的Http请求都会被对应到`home`这个方法上，`@RestController` 这个注解则告诉Spring给调用者渲染请求返回的字符串。

> `@RestController`和 `@RequestMapping` 这两个注解是Spring MVC 的注解（并不是Spring boot特有的注解），更多细节浏览Spring的参考文档


### 11.3.2 `@EnableAutoConfiguration` 注解

第二个类级别的就是@EnableAutoConfiguration. 这个注解告诉Spring Boot基于你添加的Jar包依赖去猜用户希望如何配置Spring. 因为 `spring-boot-starter-web` 添加了Tomcat和Spring MVC，于是自动配置假定你正在开发一个web应用并相应设置了Spring.

> Starters 和 Auto-Configuration
Auto-Configuration被设计来能够和"Starters"很好的结合.但是这两个概念并不直接相关，你可以自由选择除去Starters引入的Jar依赖的其他依赖，并且Spring Boot的Auto-Configuration仍旧能够很好的工作。

### 11.3.3 Main 方法
我们这个应用的最后一个部分就是main方法，它就是一个遵循Java语法约定的标准的程序入口方法。Main 函数通过调用`run`代理了Spring Boot的`Spring Application`.`Spring Application`是我们程序的启动器，启动Spring 并开始自动配置Tomcat web Server.我们需要把`Example.class`作为一个参数传递给 `Run` 方法，去告诉 `SpringApplication` 哪一个是主要的Spring 组件。通过命令行暴露的·args·这个参数也会被传递到`SpringApplication`.


### 11.4 运行示例

这时候我们的程序应该能够开始工作。因为我们使用了`spring-boot-starter-parent` POM ,我们能够有一个有效的运行目标来启动这个应用。在项目的根目录输入 `mvn spring-boot:run` 来启动这个应用

```bash
$ mvn spring-boot:run

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v1.5.6.RELEASE)
....... . . .
....... . . . (log output here)
....... . . .
........ Started Example in 2.222 seconds (JVM running for 6.514)

```

如果你打开一个网页浏览器输入`localhost:8080`，你将会看到如下输出

```bash
Hello World!
```
### 11.5 创建一个可执行的Jar

让我们做一个完全自足的可执行的Jar文件运行在生产环境中。可执行Jars(有时候叫做 fat jars) 是一个归档的文件包含你编译的类以及你代码运行所需要的Jar依赖。
为了创建一个可执行的Jar,我们需要在pom.xml添加一个 `spring-boot-maven-plugin`插件，在`depenencies`节点中插入如下内容

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

> 因为 `spring-boot-starter-parent` POM 绑定了 `repackage` 指令在 `<executions>` 配置里面，如果你没有使用parent POM的话，你需要自己去声明这些配置，详细信息请参考(plugin documention)[https://docs.spring.io/spring-boot/docs/1.5.6.RELEASE/maven-plugin/usage.html]

保存你的pom.xml文件,控制台上运行`mvn package`

```bash
$ mvn package

[INFO] Scanning for projects...
[INFO]
[INFO] ------------------------------------------------------------------------
[INFO] Building myproject 0.0.1-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] .... ..
[INFO] --- maven-jar-plugin:2.4:jar (default-jar) @ myproject ---
[INFO] Building jar: /Users/developer/example/spring-boot-example/target/myproject-0.0.1-SNAPSHOT.jar
[INFO]
[INFO] --- spring-boot-maven-plugin:1.5.6.RELEASE:repackage (default) @ myproject ---
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
```

如果你在 `target` 目录下，你应该能看到 `myproject-0.0.1-SNAPSHOT.jar` 这个文件. 这个文件大概10 MB左右，你如果想了解这个文件内部，你可以运行 `jar tvf`:

```bash
$ jar tvf target/myproject-0.0.1-SNAPSHOT.jar
```

另外在 `target`目录小你应该可以看到小很多的文件 `myproject-0.0.1-SNAPSHOT.jar.original` ,这个是maven创建的，在Spring boot repackage 之前的，原始的jar文件。

可以使用 `java -jar` 来运行应用
```bash
$ java -jar target/myproject-0.0.1-SNAPSHOT.jar

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::  (v1.5.6.RELEASE)
....... . . .
....... . . . (log output here)
....... . . .
........ Started Example in 2.536 seconds (JVM running for 2.864)
```

就像之前一样可以通过 `ctrl c ` 来实现优雅的退出
