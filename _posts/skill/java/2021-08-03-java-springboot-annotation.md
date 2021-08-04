---
layout: post
title: Spring Boot学习笔记二：底层注解简单说明
categories: SpringBoot
description: 学习 Spring Boot 随堂笔记，好记性不如烂笔头。
keywords: Java, Spring, Spring MVC, Spring Boot
---

> @Configuration、@Bean、@Import 等 SpringBoot 常用注解说明！
>

> 虽然已经工作好几年了，但是应该就是那种一年工作经验用好几年的人吧，一直忙于工作，兢兢业业，但是没有过总结，真的明白的时候发现已经荒废了好几年，把握现在，成就更好的明天。
>
> 本文参考雷神教程 + 官网手册，记录下自己的学习经历和成长！
>
> 首先雷神教程在这里：[雷神](https://www.bilibili.com/video/BV19K4y1L7MT) ，官网手册在这里：[Spring Boot 手册](https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/index.html)

## 1. SpringBoot 底层注解

### 1.1 组件添加

#### 1、@Configuration、@Bean

```java
// ===========================================================================================
package top.hkyzf.boot.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import top.hkyzf.boot.bean.Pet;
import top.hkyzf.boot.bean.User;

/**
 * 1、配置类里面使用 @Bean 标注在方法上给容器注册组件，默认单实例。
 * 2、配置类本身也是组件。
 * 3、proxyBeanMethods 代理 bean 的方法。
 *      Full 模式（proxyBeanMethods = true）[每个 @Bean 方法不管调用多少次都是单实例]
 *      Lite 模式（proxyBeanMethods = false）[每个 @Bean 方法调用多少次都是新创建的]
 *      组件依赖必须使用 Full 模式，其他情况可使用 Lite 模式
 * @author 朱峰
 * @date 2021-8-4 16:07
 */
// 告诉 SpringBoot 这是一个配置类 == 配置文件
@Configuration(proxyBeanMethods = true)
public class MyConfig {

    // 给容器中添加组件。
    // 方法名作为组件 id，返回类型就是组件类型，返回的值就是组件在容器中的实例。
    // 外部获取 MyConfig 类的对象对此方法调用多少次获取的都是容器中的单实例对象。
    @Bean
    public User user01() {
        User zhangsan = new User("zhangsan", 18);
        // user 组件依赖了 pet 组件
        zhangsan.setPet(tomcatPet());
        return zhangsan;
    }

    @Bean("tom")
    public Pet tomcatPet() {
        return new Pet("tomcat");
    }
}

// ===========================================================================================
package top.hkyzf.boot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import top.hkyzf.boot.bean.Pet;
import top.hkyzf.boot.bean.User;
import top.hkyzf.boot.config.MyConfig;

/**
 * Spring Boot 主程序类，主配置类
 * @author 朱峰
 * @date 2021-8-2 10:48
 */
@SpringBootApplication
public class MainApplication {

    public static void main(String[] args) {
        // 1、返回 IOC 容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

        // 2、查看容器中的组件
        String[] names = run.getBeanDefinitionNames();
        for (String name : names) {
            System.out.println(name);
        }
        System.out.println("=======================================");

        // 3、从容器中获取组件
        Pet pet1 = run.getBean("tom", Pet.class);
        Pet pet2 = run.getBean("tom", Pet.class);
        System.out.println("容器中的组件是否一样：" + (pet1 == pet2));

        // 4、top.hkyzf.boot.config.MyConfig$$EnhancerBySpringCGLIB$$673cd30c@7c211fd0
        MyConfig myConfig = run.getBean(MyConfig.class);
        System.out.println(myConfig);
        User user1 = myConfig.user01();
        User user2 = myConfig.user01();
        System.out.println(user1 == user2);

        User user01 = run.getBean("user01", User.class);
        Pet tom = run.getBean("tom", Pet.class);
        System.out.println("用户的宠物：" + (user01.getPet() == tom));

    }
}
```

#### 2、@Component、@Controller、@Service、@Repository

- @Component 代表所标注的类是一个组件

- @Controller 代表所标注的类是一个控制器

- @Service 代表所标注的类是一个业务逻辑组件

- @Repository 代表所标注的类是一个数据库层组件

  **其实并没有什么区别！**

#### 3、@ComponentScan、@Import

- @ComponentScan 组件扫描，指定了要扫描的包
- @Import 导入一个组件

```java
// ===========================================================================================
package top.hkyzf.boot.config;

import ch.qos.logback.core.db.DBHelper;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import top.hkyzf.boot.bean.Pet;
import top.hkyzf.boot.bean.User;

/**
 * 1、配置类里面使用 @Bean 标注在方法上给容器注册组件，默认单实例。
 * 2、配置类本身也是组件。
 * 3、proxyBeanMethods 代理 bean 的方法。
 *      Full 模式（proxyBeanMethods = true）[每个 @Bean 方法不管调用多少次都是单实例]
 *      Lite 模式（proxyBeanMethods = false）[每个 @Bean 方法调用多少次都是新创建的]
 *      组件依赖必须使用 Full 模式，其他情况可使用 Lite 模式
 * 4、@Import({User.class, DBHelper.class})
 *      给容器中自动创建出这两个类型的组件，默认组件名字是全类名。
 *
 * @author 朱峰
 * @date 2021-8-4 16:07
 */
// 告诉 SpringBoot 这是一个配置类 == 配置文件
@Import({User.class, DBHelper.class})
@Configuration(proxyBeanMethods = false)
public class MyConfig {

    // 给容器中添加组件。
    // 方法名作为组件 id，返回类型就是组件类型，返回的值就是组件在容器中的实例。
    // 外部获取 MyConfig 类的对象对此方法调用多少次获取的都是容器中的单实例对象。
    @Bean
    public User user01() {
        User zhangsan = new User("zhangsan", 18);
        // user 组件依赖了 pet 组件
        zhangsan.setPet(tomcatPet());
        return zhangsan;
    }

    @Bean("tom")
    public Pet tomcatPet() {
        return new Pet("tomcat");
    }
}

// ===========================================================================================
package top.hkyzf.boot;

import ch.qos.logback.core.db.DBHelper;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;
import top.hkyzf.boot.bean.User;

/**
 * Spring Boot 主程序类，主配置类
 * @author 朱峰
 * @date 2021-8-2 10:48
 */
@SpringBootApplication
public class MainApplication {

    public static void main(String[] args) {
        // 1、返回 IOC 容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);
        
        // 5、获取组件
        String[] namesForType = run.getBeanNamesForType(User.class);
        // top.hkyzf.boot.bean.User
		// user01
        for (String name : namesForType) {
            System.out.println(name);
        }
        DBHelper dbHelper = run.getBean(DBHelper.class);
        // ch.qos.logback.core.db.DBHelper@1b9c1b51
        System.out.println(dbHelper);

    }
}
```



#### 4、@Conditional

条件装配：满足 @Conditional 指定的条件，则进行组件注入。

![](/images/posts/java/springboot/20210804172916.png)

```java
// ===========================================================================================
package top.hkyzf.boot.config;

import ch.qos.logback.core.db.DBHelper;
import org.springframework.boot.autoconfigure.condition.ConditionalOnBean;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;
import top.hkyzf.boot.bean.Pet;
import top.hkyzf.boot.bean.User;

/**
 * 1、配置类里面使用 @Bean 标注在方法上给容器注册组件，默认单实例。
 * 2、配置类本身也是组件。
 * 3、proxyBeanMethods 代理 bean 的方法。
 *      Full 模式（proxyBeanMethods = true）[每个 @Bean 方法不管调用多少次都是单实例]
 *      Lite 模式（proxyBeanMethods = false）[每个 @Bean 方法调用多少次都是新创建的]
 *      组件依赖必须使用 Full 模式，其他情况可使用 Lite 模式
 * 4、@Import({User.class, DBHelper.class})
 *      给容器中自动创建出这两个类型的组件，默认组件名字是全类名。
 *
 * @author 朱峰
 * @date 2021-8-4 16:07
 */
// 告诉 SpringBoot 这是一个配置类 == 配置文件
@Import({User.class, DBHelper.class})
@Configuration(proxyBeanMethods = false)
//@ConditionalOnBean(name = "tom")
@ConditionalOnMissingBean(name = "tom")
public class MyConfig {

    // 给容器中添加组件。
    // 方法名作为组件 id，返回类型就是组件类型，返回的值就是组件在容器中的实例。
    // 外部获取 MyConfig 类的对象对此方法调用多少次获取的都是容器中的单实例对象。
    @Bean
    public User user01() {
        User zhangsan = new User("zhangsan", 18);
        // user 组件依赖了 pet 组件
        zhangsan.setPet(tomcatPet());
        return zhangsan;
    }

    @Bean("tom22")
    public Pet tomcatPet() {
        return new Pet("tomcat");
    }
}

// ===========================================================================================
package top.hkyzf.boot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

/**
 * Spring Boot 主程序类，主配置类
 * @author 朱峰
 * @date 2021-8-2 10:48
 */
@SpringBootApplication
public class MainApplication {

    public static void main(String[] args) {
        // 1、返回 IOC 容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

        boolean tom = run.containsBean("tom");
        System.out.println("容器中的 tom 组件：" + tom);

        boolean user01 = run.containsBean("user01");
        System.out.println("容器中的 user01 组件：" + user01);

        boolean tom22 = run.containsBean("tom22");
        System.out.println("容器中的 tom22 组件：" + tom22);
    }
}
```

### 1.2 原生配置文件引入

#### 1、@ImportResource

1. 新建 beans.xml 文件，放入 src\main\resources\beans.xml 目录。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="userXml" class="top.hkyzf.boot.bean.User">
        <property name="name" value="zhangsan"></property>
        <property name="age" value="18"></property>
    </bean>

    <bean id="catXml" class="top.hkyzf.boot.bean.Pet">
        <property name="name" value="tomcat"></property>
    </bean>
</beans>
```

2. 可以使用 @ImportResource 注解直接导入配置文件。

```java
// ===========================================================================================
package top.hkyzf.boot.config;

import org.springframework.context.annotation.ImportResource;

/**
 * 1、配置类里面使用 @Bean 标注在方法上给容器注册组件，默认单实例。
 * 2、配置类本身也是组件。
 * 3、proxyBeanMethods 代理 bean 的方法。
 *      Full 模式（proxyBeanMethods = true）[每个 @Bean 方法不管调用多少次都是单实例]
 *      Lite 模式（proxyBeanMethods = false）[每个 @Bean 方法调用多少次都是新创建的]
 *      组件依赖必须使用 Full 模式，其他情况可使用 Lite 模式
 * 4、@Import({User.class, DBHelper.class})
 *      给容器中自动创建出这两个类型的组件，默认组件名字是全类名。
 * 5、@ImportResource("classpath:beans.xml")
 *      导入 spring 的配置文件，让其生效。
 *
 * @author 朱峰
 * @date 2021-8-4 16:07
 */
@ImportResource("classpath:beans.xml")
public class MyConfig {
}

// ===========================================================================================
package top.hkyzf.boot;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.ConfigurableApplicationContext;

/**
 * Spring Boot 主程序类，主配置类
 * @author 朱峰
 * @date 2021-8-2 10:48
 */
@SpringBootApplication
public class MainApplication {

    public static void main(String[] args) {
        // 1、返回 IOC 容器
        ConfigurableApplicationContext run = SpringApplication.run(MainApplication.class, args);

        boolean userXml = run.containsBean("userXml");
        boolean catXml = run.containsBean("catXml");
        System.out.println("userXml:" + userXml);
        System.out.println("catXml:" + catXml);
    }
}
```

### 1.3 属性值绑定

#### 1、@ConfigurationProperties

1. 两种方式都可以进行属性值绑定，首先先写好配置文件。

```yaml
server:
  port: 8081

mycar:
  brand: YD
  price: 100000
```

- 方式一：@EnableConfigurationProperties + @ConfigurationProperties

- 方式二：@Component + @ConfigurationProperties

```java
// ===========================================================================================
package top.hkyzf.boot.bean;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

/**
 * @author 朱峰
 * @date 2021-8-4 18:15
 */
//@Component
@ConfigurationProperties(prefix = "mycar")
public class Car {
    private String brand;
    private Integer price;

    public String getBrand() {
        return brand;
    }

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public Integer getPrice() {
        return price;
    }

    public void setPrice(Integer price) {
        this.price = price;
    }

    @Override
    public String toString() {
        return "Car{" +
                "brand='" + brand + '\'' +
                ", price=" + price +
                '}';
    }
}

// ===========================================================================================
package top.hkyzf.boot.config;

import org.springframework.boot.context.properties.EnableConfigurationProperties;
import top.hkyzf.boot.bean.Car;

/**
 * 1、配置类里面使用 @Bean 标注在方法上给容器注册组件，默认单实例。
 * 2、配置类本身也是组件。
 * 3、proxyBeanMethods 代理 bean 的方法。
 *      Full 模式（proxyBeanMethods = true）[每个 @Bean 方法不管调用多少次都是单实例]
 *      Lite 模式（proxyBeanMethods = false）[每个 @Bean 方法调用多少次都是新创建的]
 *      组件依赖必须使用 Full 模式，其他情况可使用 Lite 模式
 * 4、@Import({User.class, DBHelper.class})
 *      给容器中自动创建出这两个类型的组件，默认组件名字是全类名。
 * 5、@ImportResource("classpath:beans.xml")
 *      导入 spring 的配置文件，让其生效。
 * 6、@EnableConfigurationProperties(Car.class)
 *      1、开启 Car 的配置绑定功能
 *      2、把 Car 这个组件自动注入到容器中
 *
 * @author 朱峰
 * @date 2021-8-4 16:07
 */
// 1、开启 Car 的配置绑定功能
// 2、把 Car 这个组件自动注入到容器中
@EnableConfigurationProperties(Car.class)
public class MyConfig {
}
```

