---
title: jdk常用工具类
date: 2018-07-02 16:08:57
tags: java
      编程语言
categories: 编程语言
---
Java里面给一个类的操作工具惯用的设计是给这个类后缀加s(Object->Objects,Array->Arrays,collection->collections)
### Objects

```java
//比较两个对象是否相等（首先比较内存地址，然后比较a.equals(b)，只要符合其中之一返回true）
public static boolean equals(Object a, Object b);

//深度比较两个对象是否相等(首先比较内存地址,相同返回true;如果传入的是数组，则比较数组内的对应下标值是否相同)
public static boolean deepEquals(Object a, Object b);

//返回对象的hashCode，若传入的为null，返回0
public static int hashCode(Object o);

//返回传入可变参数的所有值的hashCode的总和（这里说总和有点牵强，具体参考Arrays.hashCode()方法）
public static int hash(Object... values);

//返回对象的String表示，若传入null，返回null字符串
public static String toString(Object o)

//返回对象的String表示，若传入null，返回默认值nullDefault
public static String toString(Object o, String nullDefault)

//使用指定的比较器c 比较参数a和参数b的大小（相等返回0，a大于b返回整数，a小于b返回负数）
public static <T> int compare(T a, T b, Comparator<? super T> c) 

//如果传入的obj为null抛出NullPointerException,否者返回obj
public static <T> T requireNonNull(T obj) 

//如果传入的obj为null抛出NullPointerException并可以指定错误信息message,否者返回obj
public static <T> T requireNonNull(T obj, String message)
```
### Arrays
1. List转换成为数组
    ```java 
    String[] arr = (String[])list.toArray(new String[size]);
    ```
1. 数组转换成为List
    ```java
    String[] arr = new String[] {"1", "2"};
    List list = Arrays.asList(arr);
    ```
1. 数组的复制
    ```java
    java.lang.System
 
    public static void arraycopy(Object src,
                             int srcPos,
                             Object dest,
                             int destPos,
                             int length)
    ```
    还有常用的copyof(array,length)：把数组array复制成一个长度为length的新数组
### Files && Paths
- Paths.get()
- Path.normallize()
- Files.exists()
- Files.createDirectory()
- Files.copy()
- Files.move()
- Files.delete()
- Files.walkFileTree()