---
title: 大数据整理 
categories: 
- 编程语言
tags: 
- 大数据  
---

> 这是最好的时代，也是最坏的时代。         --狄更斯

 1. ABC时代：ABC即人工智能（AI）、大数据（Big Data）、云计算（Cloud Computing）三个词语的英文首字母缩写，这三个领域已然成为当下最为热门的三大领域。
 2. 4V特征：
     - 海量化（volumes）
     - 多样化（variety）
     - 快速化（velocity）
     - 价值化（value）
 3. 舍恩伯格在《大数据时代》中指出，面对大数据人类的思维和认知要发生转变：
     - 全样本（不是随机抽样）
     - 概率化（不迷恋精确性）
     - 相关性（次化因果关系）
  
 4.  	按照业务处理实时性划分为实时处理和批处理，按照系统功能及侧重点划分为侧重数据保存的存储性系统和侧重数据分析的密集型计算系统
 
 5. 大数据离不开分布式和集群技术，因此系统间通信必不可少
     
     5. 基于分层的结构（TCP/IP,各个系统中几乎都有用到分层的概念）
     6. 基于对象的结构（RPC，rest也算是一种低性能rpc通信）
     7. 基于数据的结构（web，经常传递各种含有数据的java bean）
     8. 基于消息的结构（ESB,各种MQ）
 6. 分布式的目的是避免单点故障，提高可靠性（HA,在P的情况下选择A还是C?）,所以数据都是有大量副本存在的，副本在各个节点存放的典型算法有哈希算法，按照数据量存放，按照数据范围存放等。既然有多个副本，保证一致性协议有中心副本控制协议（Primary-secondary协议或Master-slaver）和去中心化副本控制协议（Paxos算法），对于Hadoop生态来说，普遍采用中心化简化系统，但为了可靠性，又在zookeeper中采用去中心化选举出primary节点后，转为中心化
 
 
 7. 进程间通信IPC有管道，套接字，共享内存涉及IO有 阻塞式同步、非阻塞式同步、多路复用同步模式以及异步模式
 8. 典型超级计算机系统结构
    
     - 并行向量处理（PVP）
     - 对称式多处理器（SMP）
     - 分布式共享内存（DSM）
     - 大规模并行处理（MPP）
 9. 缓存系统中常用的策略
    
     - LFU:淘汰最不常用的
     - LRU:淘汰最近最少使用
     - ARC:LRU和LRU结合
     - 其他基于时间的淘汰策略
 10. ETL:Extraction-Transformation-Loading的缩写，中文名为数据抽取、转换和加载。ETL负责将分布的、异构数据源中的数据如关系数据、平面数据文件等抽取到临时中间层后进行清洗、转换、集成，最后加载到数据仓库或数据集市中，成为联机分析处理、数据挖掘的基础。ETL是BI项目中最重要的一个环节，通常情况下ETL会花掉整个项目的1/3的时间，ETL设计的好坏直接关系到BI项目的成败。ETL也是一个长期的过程，只有不断地发现问题并解决问题，才能使ETL运行效率更高，为项目后期开发提供准确数据。
 11. Hadoop生态
     ![这里写图片描述](http://img.blog.csdn.net/20170906114210485?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
     
      
	-  HDFS分布式文件系统

 ![这里写图片描述](http://img.blog.csdn.net/20170906112206136?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
checkpoint
![checkpoint](http://img.blog.csdn.net/20170906113211329?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
读写文件
![这里写图片描述](http://img.blog.csdn.net/20170906113506297?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 - YARN统一资源管理框架

![这里写图片描述](http://img.blog.csdn.net/20170906114408537?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 - MapReduce分布式计算框架

![这里写图片描述](http://img.blog.csdn.net/20170906170927622?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 - Zookeeper分布式集群协调系统

![这里写图片描述](http://img.blog.csdn.net/20170906171803701?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](http://img.blog.csdn.net/20170906171925082?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 - Hive数据仓库工具

![这里写图片描述](http://img.blog.csdn.net/20170906172209453?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 - Hbase分布式列式数据库

![这里写图片描述](http://img.blog.csdn.net/20170906172350545?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

 - storm实时流处理

![这里写图片描述](http://img.blog.csdn.net/20170906172624562?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
