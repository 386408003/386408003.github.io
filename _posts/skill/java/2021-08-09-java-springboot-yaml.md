---
layout: post
title: Spring Boot学习笔记四：YAML 一种新的配置文件类型
categories: SpringBoot
description: 学习 Spring Boot 随堂笔记，好记性不如烂笔头。
keywords: Java, Spring, Spring MVC, Spring Boot, YAML, yml
---

> SpringBoot 支持的一种新的配置文件类型。YAML -- yaml -- yml

> 虽然已经工作好几年了，但是应该就是那种一年工作经验用好几年的人吧，一直忙于工作，兢兢业业，但是没有过总结，真的明白的时候发现已经荒废了好几年，把握现在，成就更好的明天。
>
> 本文参考雷神教程 + 官网手册，记录下自己的学习经历和成长！
>
> 首先雷神教程在这里：[雷神](https://www.bilibili.com/video/BV19K4y1L7MT) ，官网手册在这里：[Spring Boot 手册](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/index.html)

## SpringBoot 配置文件

### 1. properties

- 同以前的 properties 用法。
- k=v ，a.b.c=v

### 2. yaml

#### 2.1 简介

YAML 是 "YAML Ain't a Markup Language"（YAML 不是一种标记语言）的递归缩写。在开发的这种语言时，YAML 的意思其实是："Yet Another Markup Language"（仍是一种标记语言）。

YAML 的语法和其他高级语言类似，并且可以简单表达清单、散列表，标量等数据形态。它使用空白符号缩进和大量依赖外观的特色，特别适合用来表达或编辑数据结构、各种配置文件、倾印调试内容、文件大纲（例如：许多电子邮件标题格式和 YAML 非常接近）。

YAML 的配置文件后缀为 **.yml**，如：**application.yml** 。

#### 2.2 基本语法

- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用 tab ，只允许空格
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- '#' 表示注释

#### 2.3 数据类型

- 字面量：单个的，不可再分的值。 date, boolean, string, number, null

```yaml
key: value
```

- 对象：键值对的集合。 map, hash, set, object

```yaml
# 行内写法：
k: {k1: v1, k2: v2, k3: v3}
# 或者
k:
  k1: v1
  k2: v2
  k3: v3
```

- 数组：以组按次序排列的值。array, list, queue

```yaml
# 行内写法
k: [v1, v2, v3]
#或者
k:
  - v1
  - v2
  - v3
```



#### 2.4 实例

```java
package top.hkyzf.boot.bean;

import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.Date;
import java.util.List;
import java.util.Map;
import java.util.Set;

/**
 * @author 朱峰
 * @date 2021-8-9 18:24
 */
@Data
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
        private String userName;
        private Boolean boss;
        private Date birth;
        private Integer age;
        private Pet pet;
        private String[] interests;
        private List<String> animal;
        private Map<String, Object> score;
        private Set<Double> salarys;
        private Map<String, List<Pet>> allPets;
}

// ========================================================================================
package top.hkyzf.boot.bean;

import lombok.Data;

/**
 * @author 朱峰
 * @date 2021-8-9 18:23
 */
@Data
public class Pet {
    private String name;
    private Double weight;
}
```

```yaml
person:
  # 细节1：
  # 经测试不带引号 等价于 带上单引号 后台会原模原样输出。如：'zhang\nsan' ，后台控制台输出为 zhang\nsan
  # 使用双引号转义字符会继续进行转义。如："zhang\nsan" ，后台控制台输出为 zhang
  # san
  # 细节2：
  # 后台字段名为：userName
  # yml 文件中名字可以和后台一致 userName ，也可以是 user-name ，其中 -n 等价于 N
  user-name: "zhang\nsan"
  boss: true
  birth: 2019/12/9
  age: 18
#  interests: [篮球, 足球]
  interests:
    - 篮球
    - 足球
    - 乒乓球
  animal: [阿猫, 阿狗]
#  score: {english: 80, math: 90}
  score:
    english: 80
    math: 90
  salarys:
    - 9999.98
    - 9999.99
#  pet: {name: 阿狗, weight: 12.34}
  pet:
    name: 阿狗
    weight: 99.99
  allPets:
    sick:
      - {name: 阿狗, weight: 99.99}
      - name: 阿猫
        weight: 88.88
      - name: 阿虫
        weight: 77.77
    health:
      - {name: 阿花, weight: 199.99}
      - {name: 阿明, weight: 199.99}
```

#### 2.5 如何让 Idea 进行提示

- 可参考官网步骤进行[配置注释处理器](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/appendix-configuration-metadata.html#configuration-metadata-annotation-processor-setup)

- 引入相关依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

- 重新使用 Maven 的 compile 编译一下或 package 打包即可。
- 会在 target/classes/META-INF/ 目录下生成 spring-configuration-metadata.json 文件用于提示。

- 注意：如果在项目中使用了 AspectJ 需要保证注释处理器只运行一次，具体可参考官网配置。

