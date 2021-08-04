---
layout: post
title: Spring Boot学习笔记一：一切的开端 Hello World
categories: SpringBoot
description: 学习 Spring Boot 随堂笔记，好记性不如烂笔头。
keywords: Java, Spring, Spring MVC, Spring Boot
---

> SpringBoot 入门学习记录，学习过了总要留下点痕迹，对吧？

> 虽然已经工作好几年了，但是应该就是那种一年工作经验用好几年的人吧，一直忙于工作，兢兢业业，但是没有过总结，真的明白的时候发现已经荒废了好几年，把握现在，成就更好的明天。
>
> 本文参考雷神教程 + 官网手册，记录下自己的学习经历和成长！
>
> 首先雷神教程在这里：[雷神](https://www.bilibili.com/video/BV19K4y1L7MT) ，官网手册在这里：[Spring Boot 手册](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/index.html)

## 1. 系统要求

1. Spring Boot 2.4.3 系统要求如下：

|                  | 版本              |
| :--------------- | :---------------- |
| Spring Boot      | 2.4.3             |
| Spring Framework | 5.3.4             |
| JDK              | Java8 兼容 Java15 |
| Maven            | 3.3+              |
| Idea(本人版本)   | 2019.2            |

2. 官方说明：

Spring Boot 2.4.3 requires [Java 8](https://www.java.com/) and is compatible up to Java 15 (included). [Spring Framework 5.3.4](https://docs.spring.io/spring/docs/5.3.4/reference/html/) or above is also required.

Explicit build support is provided for the following build tools:

| Build Tool | Version                                                      |
| :--------- | :----------------------------------------------------------- |
| Maven      | 3.3+                                                         |
| Gradle     | 6 (6.3 or later). 5.6.x is also supported but in a deprecated form |

3. 对Maven进行简单设置：

```xml
  <!-- 本地资源库位置 -->
  <localRepository>F:\Workspaces\maven\repository</localRepository>
  <!-- 镜像仓库设置为阿里云，加快下载依赖速度 -->
  <mirrors>
	<mirror>
		<id>alimaven</id>
		<name>aliyun maven</name>
		<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
		<mirrorOf>central</mirrorOf>        
    </mirror>
  </mirrors>
  <!-- 指定编译使用的版本为Jdk8 -->
  <profiles>
	<profile>
		<id>jdk-1.8</id>
		<activation>
			<activeByDefault>true</activeByDefault>
			<jdk>1.8</jdk>
		</activation>
		<properties>
			<maven.compiler.source>1.8</maven.compiler.source>
			<maven.compiler.target>1.8</maven.compiler.target>
			<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
		</properties>
    </profile>
  </profiles>
```



## 2. 新建项目

### 1) 编写 POM 文件

1. 使用 Idea 新建项目 boot-01-helloworld。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>top.hkyzf</groupId>
    <artifactId>boot-01-helloworld</artifactId>
    <version>1.0-SNAPSHOT</version>

    <!-- 引入 Spring Boot 父依赖 -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.3</version>
    </parent>

    <!-- 引入 Web 开发场景 -->
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

</project>
```

### 2) 编写代码。

1. 首先先编写主程序。

```java
package top.hkyzf.boot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * Spring Boot 主程序
 * @author 朱峰
 * @date 2021-8-2 10:48
 */
@SpringBootApplication
public class MainApplication {

    public static void main(String[] args) {
        SpringApplication.run(MainApplication.class, args);
    }
}
```

2. 编写一个Controller进行测试。

```java
package top.hkyzf.boot.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author 朱峰
 * @date 2021-8-2 10:51
 */
// 代表这是一个控制器，SpringBoot会将这个类放到容器中
//@Controller
// 此注解可以放在方法上代表此方法可返回特定格式的字符串，放到类上面则表示整个类的所有方法都生效。
//@ResponseBody
// @RestController == @Controller + @ResponseBody
@RestController
public class HelloController {

    //@ResponseBody
    @RequestMapping("/hello")
    public String hello01() {
        return "Hello SpringBoot 2";
    }
}
```

3. 简化配置，下面以修改端口号为例，其他配置可参考[官网配置项](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/appendix-application-properties.html)。

```yaml
# 1. 项目 resources 目录下新建 application.yml 文件。
# 2.输入如下内容即可修改端口号。
server:
  port: 8081
```

4. 简化部署，打 jar 包，直接运行。
   1. 修改 POM 文件，做如下配置。

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
   
       <groupId>top.hkyzf</groupId>
       <artifactId>boot-01-helloworld</artifactId>
       <version>1.0-SNAPSHOT</version>
       <!-- 1. 不写默认为 jar 包 -->
       <packaging>jar</packaging>
   
       <!-- 引入 Spring Boot 父依赖 -->
       <parent>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-parent</artifactId>
           <version>2.4.3</version>
       </parent>
   
       <!-- 引入 Web 开发场景 -->
       <dependencies>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
       </dependencies>
   
       <!-- 2. 将项目打成 jar 包后，会将所有依赖打入包中，可直接启动部署 -->
       <build>
           <plugins>
               <plugin>
                   <groupId>org.springframework.boot</groupId>
                   <artifactId>spring-boot-maven-plugin</artifactId>
               </plugin>
           </plugins>
       </build>
   
   </project>
   ```

   2. 进如项目目录 boot-01-helloworld/target 目录下，执行命令启动服务。

   ```powershell
   java -jar boot-01-helloworld-1.0-SNAPSHOT.jar
   ```


## 3. 相关特性初探

### 1) 依赖管理

1. 父项目做依赖管理。

```xml
<!-- 父项目 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.4.3</version>
</parent>

<!-- 父项目的父项目 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.4.3</version>
</parent>

<!-- 几乎声明了所有开发中常用的依赖的版本号，自动版本仲裁机制 -->
<properties>
    <activemq.version>5.16.1</activemq.version>
    <antlr2.version>2.7.7</antlr2.version>
    <appengine-sdk.version>1.9.86</appengine-sdk.version>
    <artemis.version>2.15.0</artemis.version>
    <aspectj.version>1.9.6</aspectj.version>
    <assertj.version>3.18.1</assertj.version>
	...
</properties>
```

2. 开发导入 starter 场景启动器。

```xml
1、能见到很多类似依赖 spring-boot-starter-* ，* 就是某种依赖。
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

2、只要引入 starter ，这个场景的所有常规依赖都会自动引入。

3、官网可查所有SpringBoot支持的场景。
https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/using-spring-boot.html#using-boot-starter

4、能见到很多类似依赖 *-spring-boot-starter ，第三方的简化开发的场景启动器。

5、所有的场景启动器底层都依赖这个依赖。
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <version>2.4.3</version>
    <scope>compile</scope>
</dependency>
```

3. 无需关注版本号，自动版本仲裁。

```xml
1、引入的抵赖默认都可以不写版本号。
2、引入非版本仲裁的 jar ，需要写版本号。
```

4. 可以修改版本号。

```xml
1、先查看需要修改的依赖在 spring-boot-dependencies 里面规定的当前依赖版本使用的 key 是什么。
2、在当前项目重写配置即可，拿 mysql 举例：
<properties>
	<mysql.version>8.0.23</mysql.version>
</properties>
```

### 2) 自动配置

1. 自动配置好 Tomcat 。

   1. 引入 Tomcat 依赖。
   2. 配置 Tomcat。

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-tomcat</artifactId>
       <version>2.4.3</version>
   </dependency>
   ```

   

2. 自动配置好SpringMVC。

   1. 引入 SpringMVC 的全套组件。
   2. 自动配置好 SpringMVC 的常用组件（功能）。

3. 自动配置好 Web 常见功能，比如字符编码。

   1. SpringBoot 帮我们配置好了所有 Web 开发的常见场景。

4. 默认的包结构。

   1. 主程序所在包及其下面所有子包里面的组件都会被默认扫描出来。
   2. 无需配置包扫描。
   3. 想要改变包扫描路径，如下。
      1. @SpringBootApplication(scanBasePackages = "top.hkyzf")
      2. @ComponentScan 指定扫描路径，此处由于是不可重复注解，后续讨论如何使用。

   ```java
   @SpringBootApplication
   // 等价于
   @SpringBootConfiguration
   @EnableAutoConfiguration
   @ComponentScan
   ```

5. 各种配置拥有默认值。

   1. 配置文件的值最终都会绑定到某个类上，这个类会在容器中创建对象。

6. 按需加载所有自动配置项。

   1. 引入了哪个场景，哪个场景的自动配置才会生效。
   2. 所有的自动配置功能都在 spring-boot-autoconfigure 这个包里面。

   ...

