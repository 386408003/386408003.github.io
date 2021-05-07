---
layout: post
title: Java集合排序的两种方式
categories: Java
description: 多年前写的青涩文章，留个纪念！
keywords: Collections, Collections, Sort, Java
---

# Java集合排序的两种方式

今天工作中遇到需要将一个List排序，List中的数据是Map，由于自己基础不好，所以特地学习了下Java中的集合排序（使用的都是Collections这个辅助类的sort方法），记录一下以备使用，如果有错误也请大家指出，同时有大家需要的话也可分享，记得注明出处哦！^_^

- 第一种情况使用个人感觉不是太方便，所排序的对象必须实现Comparable接口，重写compareTo()方法。
  - Collections.sort(List list)

- 第二种情况需要new Comparator()对象，并实现compare()方法，使用匿名类的方式，比较灵活。
  - Collections.sort(List list,Comparator comp)

```java
// 创建一个Student类
public class Student implements Comparable<Student> {
    private String name;
    private Integer age;

    public Student(String name,Integer age) {
        this.name = name;
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public Integer getAge() {
        return age;
    }
    public void setAge(Integer age) {
        this.age = age;
    }
    @Override
    public String toString() {
        return "Student name = " + name + ", age = " + age;
    }
    @Override
    public int compareTo(Student s) {
        return this.age - s.age;//这样对此对象进行升序排列
    }
}

// 测试类
public class Test {
    public static void main(String[] args) {
        List<Student> list = new ArrayList<Student>();
        Student s1 = new Student("小明", 27);
        Student s2 = new Student("小红", 20);
        Student s3 = new Student("张三", 50);
        Student s4 = new Student("李四", 33);
        list.add(s1);
        list.add(s2);
        list.add(s3);
        list.add(s4);
 
        Iterator iterator = list.iterator();
        System.out.println("正序排列:");
        Collections.sort(list);
        while(iterator.hasNext()){
            Student s = (Student)iterator.next();
            System.out.println(s);
        }
 
        System.out.println("倒序排序:");
        Comparator comparator = Collections.reverseOrder();
        Collections.sort(list,comparator);
        Iterator iterator_reverse = list.iterator();
        while(iterator_reverse.hasNext()){
            Student s = (Student)iterator_reverse.next();
            System.out.println(s);
        }
    }
}

// 排序List<Map<String, Object>>按照List中的Map的某个键值进行排序
// 这种排序方式所对比的类没有实现Comparable接口也可实现排序
public void testComparatorMap(){
    Collections.sort(list, new Comparator<Map<String, String>>() {
        public int compare(Map<String, String> m1, Map<String, String> m2) {
            int n1 = Integer.parseInt(m1.get("NUMBER"));
            int n2 = Integer.parseInt(m2.get("NUMBER"));
            return n1 - n2;
        }
    });
}

//升序：
public int compare(Object a,Object b){
    return a.val - b.val;
}
//降序：
public int compare(Object a,Object b){
    return b.val - a.val;
}
```

