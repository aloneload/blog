---
title: java集合
date: 2018-09-06 10:22:23
tags: java
categories: java
---
- ArrayList:数组实现，适合随机访问，插入和删除除了尾部外性能较差
- LinkList: 链表实现，插入删除高效，随机访问差
- Queue/Deque: java 标准队列实现
- TreeSet: 利用TreeMap实现，value为Dummy对象"PRESENT",其插入元素以键的形式放入TreeMap里面
- 同步时可用Collections包装
    `
    static List synchronize...
    `
- Collections.sort(),底层调用Arrays.sort(),对于
    1. 小数据集，采用二分插入
    1. 原始数据类型，采用双轴快速排序
    1. 对象数据类型，采用TimSort,优化过的归并和二分插入排序
    ![list图](list.png)

- HashMap: put,get操作可以达到常数时间的性能，所以是绝大多数利用键值存取场景的首选
- TreeMap: 基于红黑树的一种顺序访问的Map,get,put,remove操作是O(log(n))
- 基本约定:
    1. equals 相等，hashCode 一定要相等。
    1. 重写了hashCode 也要重写equals
- LinkedHashMap顺序存储，例如重写removeEldestEntry可以实现定量过期删除
- 负载因子*容量>元素数量,同时元素数量应该是2的幂（确保hash 均匀），倍数扩容
- ConcurrentHashMap二级hash,进行分段加锁保证性能
- 31*N可以被编译器优化为左移5位后减1，有较高的性能

![Map](map.png)