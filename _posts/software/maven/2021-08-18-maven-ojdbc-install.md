---
layout: post
title: 使用 Maven 导入 Oracle 驱动包
categories: Maven
description: 使用 Maven 导入 Oracle 驱动常见问题
keywords: Maven, Oracle, ojdbc6
---

> 以前都是用 <scope>system<scope> 这种方式，还需要使用 Maven 插件拷贝 jar 包！

## 1. 导入 Oracle 驱动包

1. 在 Maven 仓库查找，发现都提示这个包已经移至 com.oracle.database.jdbc 包下面，点击链接到[最新仓库目录](https://mvnrepository.com/artifact/com.oracle.database.jdbc)，找到自己的版本。

   ```xml
   <!-- https://mvnrepository.com/artifact/com.oracle.database.jdbc/ojdbc6 -->
   <dependency>
       <groupId>com.oracle.database.jdbc</groupId>
       <artifactId>ojdbc6</artifactId>
       <version>11.2.0.4</version>
   </dependency>
   ```

2. 发现报错了，一直下载不成功，其实是因为Oracle的授权问题。
## 2. 手动下载安装

1. 在 Maven 仓库先找到自己需要的驱动包，[点此下载](https://repo1.maven.org/maven2/com/oracle/database/jdbc/)。

2. 在项目目录下执行命令安装至本地仓库，windows 系统。

   ```xml
   # install : 将项目编译并打包到本地仓库
   # install-file : 安装文件
   # -Dfile=D:\ojdbc6.jar : 指定要打包到本地仓库的文件
   # -DgroupId=com.oracle.database.jdbc : 指定当前包的 groupId 为 com.oracle.database.jdbc ，可自定义
   # -DartifactId=ojdbc6 : 指定当前包的 artifactId 为 ojdbc6
   # -Dversion=11.2.0.4 : 指定当前包的版本为 11.2.0.4
   # -DgeneratePom=true : 是否生成pom文件
   
   mvn install:install-file -Dfile=D:\ojdbc6.jar -DgroupId=com.oracle.database.jdbc -DartifactId=ojdbc6 -Dversion=11.2.0.4 -Dpackaging=jar -DgeneratePom=true
   ```

3. 执行完成之后会在控制台看到 BUILD SUCCESS，即为成功。