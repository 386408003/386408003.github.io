---
layout: post
title: Java 日期和时间的前世今生
categories: Java
description: 主要是 JDK8 之前和之后日期和时间类可用 API 梳理
keywords: java, date, time
---

> 主要是 JDK8 前后日期和时间可用的 API 学习。

## 1. JDK8 之前日期和时间的 API 

### 1.1 System 类

```java
@Test
public void test1 () {
    // 用来返回当前时间与1970年1月1日0时0分0秒之间以毫秒为单位的时间差
    long currentTimeMillis = System.currentTimeMillis();
    // 1630027454965
    System.out.println(currentTimeMillis);
}
```

### 1.2 java.util.Date 类

```java
@Test
public void test2 () {
    // 构造一个当前时间的 Date 对象
    Date date1 = new Date();
    // Fri Aug 27 09:36:06 CST 2021
    System.out.println(date1.toString());
    // 获取当前 Date 对象对应的时间戳(毫秒数)
    // 1630028166998
    System.out.println(date1.getTime());;

    // 构造器 2：获取指定毫秒数的 Date 对象
    Date date2 = new Date(1630028166998L);
    // Fri Aug 27 09:36:06 CST 2021
    System.out.println(date2.toString());
}
```

### 1.3 java.util.Date 类

```java
@Test
public void test3 () {
    // java.sqL.Date对应着数据库中的日期类型的变量
    java.sql.Date date = new java.sql.Date(1630028166998L);
    // 2021-08-27
    System.out.println(date.toString());

    // 如何将 java.util.Date 对象转换为 java.sql.Date 对象
    // 情况一：
    Date date1 = new java.sql.Date(1630028166998L);
    java.sql.Date date2 = (java.sql.Date) date1;
    // 情况二：
    Date date3 = new Date();
    java.sql.Date date4 = new java.sql.Date(date3.getTime());
}
```

### 1.4 SimpleDateFormat 类

```java
@Test
public void testSimpleDateFormat () throws ParseException {
    // SimpleDateFormat 的使用：SimpleDateFormat 对日期 Date 类的格式化和解析
    // 实例化，使用默认构造器
    SimpleDateFormat sdf = new SimpleDateFormat();
    // 格式化：日期 -> 字符串
    Date date1 = new Date();
    // Fri Aug 27 10:39:40 CST 2021
    System.out.println(date1);
    String format = sdf.format(date1);
    // 21-8-27 上午10:39
    System.out.println(format);
    // 解析：字符串 -> 日期
    String str = "21-8-27 上午10:39";
    Date date2 = sdf.parse(str);
    // Fri Aug 27 10:39:00 CST 2021
    System.out.println(date2);

    // 按照指定方式格式化和解析
    SimpleDateFormat sdf1 = new SimpleDateFormat("yyyy-MM-dd hh:mm:ss");
    // 格式化
    String format1 = sdf1.format(date1);
    // 2021-08-27 10:46:33
    System.out.println(format1);
    // 解析
    Date date3 = sdf1.parse("2021-08-27 10:46:33");
    // Fri Aug 27 10:46:33 CST 2021
    System.out.println(date3);
}
```

### 1.5 练习题

```java
/**
 * 练习题：三天打鱼两天晒网
 * 从 1970-01-01 开始，三天打鱼，两天晒网，给定一个日期，看这天是在打鱼还是在晒网
 * 思路：
 *      1. 计算给定日期到 1970-01-01间隔天数
 *      2.  结果 % 5 = 1,2,3 == 打鱼
 *          结果 % 5 = 4,0 == 晒网
 */
@Test
public void test4 () throws ParseException {
    SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
    Date date1 = sdf.parse("1970-01-01");
    Date date2 = sdf.parse("2021-08-27");
    long time = (date2.getTime() - date1.getTime());
    // 由于除完之后大概率有余数所以天数 +1 ，即使除尽了，也就是当天晚上 12 点整，也可以当作是下一天了
    long day = time / (1000 * 60 * 60 * 24) + 1;
    System.out.println(day);
    int mod = (int) (day % 5);
    switch (mod) {
        case 0:
            System.out.println("晒网");
            break;
        case 1:
            System.out.println("打鱼");
            break;
        case 2:
            System.out.println("打鱼");
            break;
        case 3:
            System.out.println("打鱼");
            break;
        case 4:
            System.out.println("晒网");
            break;
        default:
            System.out.println("出错了？");
    }
}
```

### 1.6 Calendar 类

```java
@Test
public void testCalendar () {
    // 1. 实例化
    // 1.1 创建其子类（GregorianCalendar）对象
    // 1.2 调用其静态方法 getInstance();
    Calendar calendar = Calendar.getInstance();

    // 2. 常用方法
    // get()
    int dayOfMonth = calendar.get(Calendar.DAY_OF_MONTH);
    // 27
    System.out.println(dayOfMonth);
    int dayOfYear = calendar.get(Calendar.DAY_OF_YEAR);
    // 239
    System.out.println(dayOfYear);

    // set()
    calendar.set(Calendar.DAY_OF_MONTH, 20);
    // 20
    System.out.println(calendar.get(Calendar.DAY_OF_MONTH));

    // add()
    calendar.add(Calendar.DAY_OF_MONTH, 3);
    // 23
    System.out.println(calendar.get(Calendar.DAY_OF_MONTH));

    // getTime()：日历类 -> Date
    Date date1 = calendar.getTime();
    // Mon Aug 23 11:29:15 CST 2021
    System.out.println(date1);

    // setTime()
    Date date2 = new Date();
    calendar.setTime(date2);
    // 27
    System.out.println(calendar.get(Calendar.DAY_OF_MONTH));

    // 今天星期五，打印结果：6
    // 周日：1、周一：2 ... 周六：7
    System.out.println(calendar.get(Calendar.DAY_OF_WEEK));
    // 今天是八月，打印结果：7
    // 一月：0、二月：1 ... 十二月：11
    System.out.println(calendar.get(Calendar.MONTH));
}
```



## 2. JDK8 之后日期和时间的API

### 2.1 为什么要引入新得API

- 可变性：像日期和时间这样的类应该是不可变的。

- 偏移性：Date中的年份是从1900开始的，而月份都从0开始。

  ```java
  @Test
  public void testDate() {
      Date date = new Date(2021 - 1900, 8 - 1, 8);
      // Sun Aug 08 00:00:00 CST 2021
      System.out.println(date);
  }
  ```

- 格式化：格式化只对 Date 有用，Calendar 则不行。

- 此外，它们也不是线程安全的;不能处理闰秒等。



### 2.2 LocalDate、Local Time、LocalDateTime 类

```java
@Test
public void test1 () {
    // now() ：获取当前日期时间
    LocalDate localDate = LocalDate.now();
    LocalTime localTime = LocalTime.now();
    LocalDateTime localDateTime = LocalDateTime.now();
    // 2021-08-27
    System.out.println(localDate);
    // 11:49:02.014
    System.out.println(localTime);
    // 2021-08-27T11:49:02.014
    System.out.println(localDateTime);

    // of() ：设置指定的年、月、日、时、分、秒，没有偏移量
    LocalDateTime localDateTime1 = LocalDateTime.of(2021, 8, 27, 11, 51, 11);
    // 2021-08-27T11:51:11
    System.out.println(localDateTime1);

    // get相关：获取相关属性
    // 239
    System.out.println(localDateTime.getDayOfYear());
    // FRIDAY
    System.out.println(localDateTime.getDayOfWeek());
    // AUGUST
    System.out.println(localDateTime.getMonth());
    // 8
    System.out.println(localDateTime.getMonthValue());
    // 54
    System.out.println(localDateTime.getMinute());

    // with相关：设置相关属性
    // 不可变性
    LocalDate localDate1 = localDate.withDayOfMonth(20);
    // 2021-08-27
    System.out.println(localDate);
    // 2021-08-20
    System.out.println(localDate1);

    // plus相关：加上
    LocalDateTime localDateTime2 = localDateTime.plusMonths(3);
    // 2021-08-27T12:00:35.573
    System.out.println(localDateTime);
    // 2021-11-27T12:00:35.573
    System.out.println(localDateTime2);
    // minus相关：减去
    LocalDateTime localDateTime3 = localDateTime.minusDays(6);
    // 2021-08-21T12:00:35.573
    System.out.println(localDateTime3);
}
```

### 2.3 Instant 类

```java
@Test
public void test2 () {
    // now() ：获取本初子午线对应的标准时间
    Instant instant = Instant.now();
    // 2021-08-27T04:04:20.011Z
    System.out.println(instant);

    // 添加偏移量
    OffsetDateTime offsetDateTime = instant.atOffset(ZoneOffset.ofHours(8));
    // 2021-08-27T12:07:18.272+08:00
    System.out.println(offsetDateTime);

    // 获取自 1970-01-01 00:00:00(UTC) 开始的毫秒数
    long epochMilli = instant.toEpochMilli();
    // 1630037379188
    System.out.println(epochMilli);

    // 通过给定的毫秒数获取 Instant 实例，类似与 java.util.Date
    Instant instant1 = Instant.ofEpochMilli(1630037379188L);
    // 2021-08-27T04:09:39.188Z
    System.out.println(instant1);
}
```

### 2.4 DateTimeFormatter 类

```java
@Test
public void test3 () {
    // 方式一：ISO_LOCAL_DATE_TIME、ISO_LOCAL_DATE、ISO_LOCAL_TIME
    DateTimeFormatter formatter1 = DateTimeFormatter.ISO_LOCAL_DATE_TIME;
    // 格式化：日期 -> 字符串
    LocalDateTime localDateTime = LocalDateTime.now();
    String format = formatter1.format(localDateTime);
    // 2021-08-27T12:16:48.302
    System.out.println(localDateTime);
    // 2021-08-27T12:16:48.302
    System.out.println(format);

    // 解析：字符串 -> 日期
    TemporalAccessor accessor = formatter1.parse("2021-08-27T12:16:48.302");
    // {},ISO resolved to 2021-08-27T12:16:48.302
    System.out.println(accessor);

    // 方式二：
    // ofLocalizedDateTime()
    // FormatStyle.LONG / FormatStyle.MEDIUM / FormatStyle.SHORT ：适用于 LocalDateTime
    DateTimeFormatter formatter2 = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.LONG);
    // 格式化
    // 2021年8月27日 下午12时51分38秒
    System.out.println(formatter2.format(localDateTime));

    // ofLocalizedDate()
    // FormatStyle.FULL / FormatStyle.LONG / FormatStyle.MEDIUM / FormatStyle.SHORT ：适用于 LocalDate
    DateTimeFormatter formatter3 = DateTimeFormatter.ofLocalizedDate(FormatStyle.FULL);
    // 格式化
    // 2021年8月27日 星期五
    System.out.println(formatter3.format(LocalDate.now()));

    // 方式三：自定义格式。如：ofPattern("yyyy-MM-dd hh:mm:ss")
    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd hh:mm:ss");
    // 格式化
    // 2021-08-27 12:59:40
    System.out.println(formatter.format(LocalDateTime.now()));

    // 解析
    // {SecondOfMinute=40, HourOfAmPm=0, NanoOfSecond=0, MicroOfSecond=0, MinuteOfHour=59, MilliOfSecond=0},ISO resolved to 2021-08-27
    System.out.println(formatter.parse("2021-08-27 12:59:40"));
}
```

### 2.5 Duration 类

```java
@Test
public void testDuration () {
    // Duration:用于计算两个“时间”间隔，以秒和纳秒为基准
    LocalTime localTime = LocalTime. now();
    LocalTime localTime1 = LocalTime.of(15, 23, 32);
    // between()：静态方法，返回 Duration 对象，表示两个时间的间隔
    Duration duration = Duration.between(localTime1, localTime);
    // PT-2H-16M-37.791S
    System.out.println(duration);

    // -8198
    System.out.println(duration.getSeconds());
    // 209000000
    System.out.println(duration.getNano());

    LocalDateTime localDateTime = LocalDateTime.of(2016, 6, 12, 15, 23, 32);
    LocalDateTime localDateTime1 = LocalDateTime.of(2017, 6, 12, 15, 23,  32);

    Duration duration1 = Duration.between(localDateTime1, localDateTime);
    // -365
    System.out.println(duration1.toDays());
}
```



