---
layout: post
title: 尚硅谷JavaSE集合学习
categories: Java
description: Java学习记录
keywords: Map, Collection, Java
---

### 1. 集合类Collection

#### 1. 概述

1. Collection中添加的类要实现equals()方法，其中多个API调用时都会调用对象的equals()方法比较元素是否相等。

2. 优缺点

![](/images/posts/java/collection/8-20210408111700.png)

3. 接口继承树

![](/images/posts/java/collection/8-20210408111936.png)



![](/images/posts/java/collection/8-20210408140254.png)



#### 2. 集合跟数组的相互转换

```java
// 集合 --> 数组：Collection的toArray()方法。
Collection coll = new ArrayList();
coll.add(123);
coll.add(345);
Object[] obj = coll.toArray();

// 数组 --> 集合：调用Arrays类的静态方法asList()
List<String> list = Arrays.asList(new String[]{"AA", "BB"});

// 下面的情况需要注意（是个坑）：
List list1 = Arrays.asList(new int[]{123, 456});
System.out.println(list1.size()); // 1
List list2 = Arrays.asList(new Integer[]{123, 456});
System.out.println(list2.size()); // 2
```

#### 3. 迭代器Iterator

```java
Collection coll = new ArrayList();
coll.add(123);
coll.add(345);
Iterator iterator = coll.iterator();
// 遍历方式一：Iterator迭代器遍历
while(iterator.hasNext()) {
    System.out.println(iterator.next());
}
// 遍历方式二：jdk5.0新增foreach遍历，本质上还是迭代器
for(Object obj : coll) {
    System.out.println(obj);
}

// 错误调用方式一：
while((iterator.next()) != null) {
    System.out.println(iterator.next());
}
// 错误调用方式二：
// 每次调用iterator()都会生成一个信息的迭代器对象
while(coll.iterator().hasNext()) {
    System.out.println(iterator.next());
}
// 错误调用方式三：
// 还未调用next()或调用next()方法后已经调用过了remove()方法，再调用remove()会报错。
while(iterator.hasNext()) {
    // iterator.remove(); // 报错
    Object obj = iterator.next();
    iterator.remove();
    // iterator.remove(); // 报错
}
```



### 2. List接口

#### 1. 概述

1. 三个实现类：ArrayList、LinkedList、Vector异同点。

![](/images/posts/java/collection/8-20210408141825.png)



#### 2. ArrayList源码分析

![](/images/posts/java/collection/8-20210408144154.png)

- 总结：常用方法
  - 增    add(Object o)
  - 删    remove(int index) / remove(Object o)
  - 改    set(int index, Object o)
  - 查    get(int index)
  - 插    add(int index, Object o)
  - 长度    size()
  - 遍历    ①Iterator ②增强for ③普通for

#### 3. LinkedList源码分析

![](/images/posts/java/collection/8-20210408151713.png)

#### 4. Vector源码分析

![](/images/posts/java/collection/8-20210408153131.png)



### 3. Set接口

#### 1. 概述

1. 三个实现类：HashSet、LinkedHashSet、TreeSet异同点。

![](/images/posts/java/collection/8-20210408161918.png)

#### 2. Set的特性，以HashSet为例

1. HashSet大概结构如图：

![](/images/posts/java/collection/8-20210408172522.png)

2. HashSet特性：

![](/images/posts/java/collection/8-20210408165659.png)

#### 3. LinkedHashSet

1. LinkedHashSet大概结构如图：

![](/images/posts/java/collection/8-20210408174436.png)

2. 优点：

   对于对于频繁的遍历操作，LinkedHashSet效率高于HashSet。

#### 4. TreeSet

1. TreeSet特性

![](/images/posts/java/collection/8-20210408180449.png)

### 4. 小总结

1. 集合Collection中存储自定义类型对象，需要自定义类重写哪个方法？为什么？
   - equals()方法。 contains() / remove() / retainsAll() 方法都需要调用equals()方法。
   - List：equals()方法
   - Set：
     - HashSet、LinkedHashSet：equals()、hashCode()方法
     - TreeSet：Comparable的compareTo()、Comparator的compare(Object o1, Object o2)方法

2. ArrayList、LinkedList、Vector三者异同点？
   - 同：都实现了List接口，有序可重复。
   - 异：
     - Vector线程安全的，List的古老实现类，效率低。扩容2倍。
     - ArrayList线程非安全的，效率高，底层使用数据实现，直接找某个元素效率比LinkedList高。扩容1.5倍。
     - LinkedList线程非安全的，底层使用双向链表实现，频繁的插入删除操作效率比ArrayList高。

3. Set存储数据的特点，常见的实现类有什么？说一下彼此的特点？
   - 无序的、不可重复的。
   - HashSet、LinkedHashSet、TreeSet
   - LinkedHashSet相当于HashSet又维护了一对双向链表，可按照插入顺序遍历输出。TreeSet可进行自然排序或者定制排序。



### 5. Map接口

#### 1. 概述

##### 	[红黑树详解](https://www.yycoding.xyz/post/2014/3/27/introduce-red-black-tree)

1. 优缺点+相关理解

![](/images/posts/java/collection/8-20210412131911.png)

2. 接口继承树

![](/images/posts/java/collection/8-20210412125944.png)

#### 2. Map特性，以HashMap为例

1. HashMap大概结构如图：
   - 1.7版本
   
![](/images/posts/java/collection/8-20210412143659.png)
   
   - 1.8版本
   
   ![](/images/posts/java/collection/8-20210412144039.png)
   
1. Map特性（HashMap为例）

![](/images/posts/java/collection/8-20210412134413.png)

3. HashMap中出现的常量

![](/images/posts/java/collection/8-20210412145818.png)

总结：常用方法

- 增    put(Object key, Object value)
- 删    remove(Object key)
- 改    put(Object key, Object value)
- 查    get(Object key)
- 长度    size()
- 遍历    ①keySet() ②values() ③entrySet()

#### 3.LinkedHashMap

1. 主要区别是newNode时，LinkedHashMap创建的是LinkedHashMap.Entry这个对象。

   Entry<K,V> before, after; 这两个引用能够记录添加的元素的先后顺序。

![](/images/posts/java/collection/8-20210412150907.png)

2. HashSet通过HashMap实现，那么put操作放入的value值是什么？

   全都是同一个空对象的常量。

   ```java
   // Dummy value to associate with an Object in the backing Map
   private static final Object PRESENT = new Object();
   
   public boolean add(E e) {
       return map.put(e, PRESENT)==null;
   }
   ```



#### 4. Map遍历（三种方式）

```java
@Test
public void testForEach () {
    Map<String, Object> map = new HashMap<>();
    map.put("AA", 123);
    map.put("BB", "AB");
    map.put("CC", 12.5);
    map.put("DD", "asd");

    // 1. 遍历所有key集：keySet()
    Set<String > set = map.keySet();
    // 1.1 Iterator迭代器
    Iterator<String> iterator = set.iterator();
    System.out.println("1.1 iterator");
    while (iterator.hasNext()) {
        String s = iterator.next();
        System.out.println(String.format("key:%s-->value:%s", s, map.get(s)));
    }
    // 1.2 增强for循环
    System.out.println("1.2 foreach");
    for (String s : set) {
        System.out.println(String.format("key:%s-->value:%s", s, map.get(s)));
    }

    // 2. 遍历所有的value集：values()
    Collection<Object> values = map.values();
    // 2.1 Iterator迭代器
    Iterator<Object> iterator1 = values.iterator();
    System.out.println("2.1 iterator");
    while (iterator1.hasNext()) {
        Object obj = iterator1.next();
        System.out.println("value:" + obj);
    }
    // 2.2 增强for循环
    System.out.println("2.2 foreach");
    for (Object obj : values) {
        System.out.println("value:" + obj);
    }

    // 3. 遍历所有的key-value：entrySet()
    Set<Map.Entry<String, Object>> entries = map.entrySet();
    // 3.1 Iterator迭代器
    Iterator<Map.Entry<String, Object>> iterator2 = entries.iterator();
    System.out.println("3.1 iterator");
    while (iterator2.hasNext()) {
        Map.Entry<String, Object> entry = iterator2.next();
        System.out.println(String.format("key:%s-->value:%s", entry.getKey(), entry.getValue()));
    }
    // 3.2 增强for循环
    System.out.println("3.2 foreach");
    for (Map.Entry<String, Object> entry : entries) {
        System.out.println(String.format("key:%s-->value:%s", entry.getKey(), entry.getValue()));
    }
}
```

#### 5. 其他Map实现类

1. TreeMap：类似TreeSet。
2. Hashtable：Map的古老实现类。
3. Properties：常用来处理配置文件，是键值对都是String类型。



### 6. Collections常用方法

1. reverse(List)：反转List中的元素。

2. shuffle(List)：对List集合元素进行随机排序。

3. swap(List, int, int)：将List集合中的 i 和 j 位置上的元素交换。

4. 排序类

   - sort(List)
   - sort(List, Comparator)
   - max(List)
   - max(List, Comparator)
   - min(List)
   - min(List, Comparator)

5. int frequency(Collection, Object)：返回集合中指定元素出现的次数。

6. copy(List dest, List src)：将src中元素复制到dest中。

   ```java
   @Test
   public void testCollectionsCopy () {
       List<String> list = new ArrayList<>();
       list.add("AA");
       list.add("G");
       list.add("FD");
   
       // 1. 抛异常：java.lang.IndexOutOfBoundsException: Source does not fit in dest
       // List<String> dest = new ArrayList<>();
       // 2. 依旧抛异常，因为dest.size()还是0，只是底层数组初始化长度有值。
       // List<String> dest = new ArrayList<>(list.size());
       // 3. 可以使用此方法
       List<String> dest = Arrays.asList(new String[list.size()]);
       Collections.copy(dest, list);
       System.out.println(dest);
   }
   ```

   







































