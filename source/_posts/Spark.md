---
title: 大数据之Spark 
categories: 
- 编程语言
tags: 
- spark 
- 大数据 
---
概览
--

 - Apache Spark是一个高效的通用的集群计算系统。 它提供高层级的Java, Scala 和 Python 接口，和优化的通用图计算引擎。 同时支持丰富的高级工具集，如处理SQL和结 构化数据的 Spark SQL ，机器学习 MLlib，图处理的 GraphX ，和 Spark Streaming。

速度
--

 - 比内存中的HadoopMapReduce快10倍 比硬盘上的HadoopMapReduce快100 倍
 - Spark 有一个高级的 DAG 执行引擎，支持循环迭代的数据流和内存中的计算

易用
--

 - 用 Java、Scala 或者 Python 迅速开发应用

通用
--

 - Spark提供一套高层工具栈，包括 SparkSQL，机器学习的MLlib，GraphX，和 Spark Streaming。你可以在一个应用中无缝地结合这些框架。

![这里写图片描述](http://spark.apache.org/images/spark-stack.png)

集成 Hadoop
---------

 - Spark也很容 易独立运行，可以读取任 何Hadoop数据源
 

下载 Spark
--------
官网下载：https://d3kbcqa49mib13.cloudfront.net/spark-2.2.0-bin-hadoop2.7.tgz
Maven依赖：

```
groupId: org.apache.spark
artifactId: spark-core_2.11
version: 2.2.0
```

运行
--

```
#使用 --master参数指定 一个分部式系统的主URL，或者 local指定本地单线程运行， 或 local[N]指定本地N线程。
#Scala 
./bin/spark-shell --master local[2]
#Python
./bin/pyspark --master local[2]
#通用的 spark-submit 脚本来启动示例程序
./bin/spark-submit examples/src/main/python/pi.py 10
```
为了看起来简洁，以下均为Python解释运行

```
$ ./bin/pyspark --master local

>>> textFile = sc.textFile("README.md")
>>> textFile.count()
104
>>> textFile.first()
u'# Apache Spark'
>>> linesWithSpark = textFile.filter(lambda line : "Spark" in line)
>>> linesWithSpark.count()
20
>>> textFile.map(lambda line : len (line.split())).reduce(lambda a, b : a if ( a>b)else b)
22
>>> wordCounts = textFile.flatMap(lambda line : line.split()).map(lambda word : ( word , 1 )).reduceByKey(lambda a , b : a + b )
>>> wordCounts.collect()
>>> linesWithSpark.cache()
>>> linesWithSpark.count()
20
```
maven项目
```
package com.spark.practice;

import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.Function;

public class SimpleApp {
	public static void main(String[] args) {
		String logFile = "/Users/wyq/spark/spark-2.1.1-bin-hadoop2.6/README.md";// 可以是你系统上任意的英文txt文件
		SparkConf conf = new SparkConf().setAppName("Simple Application").setMaster("local");
		JavaSparkContext sc = new JavaSparkContext(conf);
		JavaRDD<String> logData = sc.textFile(logFile).cache();
		long numAs = logData.filter(new Function<String, Boolean>() {
			public Boolean call(String s) {
				return s.contains("a");
			}
		}).count();
		long numBs = logData.filter(new Function<String, Boolean>() {
			public Boolean call(String s) {
				return s.contains("b");
			}
		}).count();
		System.out.println("Lines with a: " + numAs + ", lines with b: " + numBs);
	}
}
```
运行结果：Lines with a: 62, lines with b: 30
架构及原理
-----
![这里写图片描述](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1506524100289&di=9451b302322da49e3533bfadaaf4bde6&imgtype=0&src=http://images2015.cnblogs.com/blog/1004194/201608/1004194-20160829174157699-296881431.png)
![这里写图片描述](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1506524851070&di=38c61b5d7c9b845e68fed0a5624097d3&imgtype=0&src=http://www.th7.cn/d/file/p/2013/09/16/6990330450aa29d4888ad23b37ea2197.jpg)
 - 每一个 Spark 应用程序都包含一个 驱动程序(driver program)，它会运行 用户的 main函数，并在集群上执行各种 并行操作(parallel operations)。Spark 提供的最 主要的抽象概念是 弹性分布式数据集(resilient   distributed dataset) (RDD)， 它是一个元 素集合，被分区地分布到集群的不同节点上，可以并行操作。RDDs可以从   HDFS(或者 任意其他支持 Hadoop 的文件系统) 上的一个文件开始创建，或者通过转换驱动程序 (driver   program)中已经存在的 Scala 集合得到。 用户也可以让 Spark 将一个 RDD持  久化(persist)到内存中，使其能在并行操作中被有效地重复使用。最后，RDD能自动从节点 故障中恢复。
 - Spark 的第二个抽象概念是共享变量(shared variables)，它可以在并行操作中使用。 在默认情况下，当 Spark 将一个函数以任务集(a set of tasks)的形式在不同节点上并行运 行时， 会将该函数所使用的每个变量的拷贝传递给每一个任务(task)中。有时候，一个 变量需要在任务之间，或任务与驱动程序之间进行共享。 Spark 支持两种类型的共享变 量: 广播变量(broadcast variables)，它可以在所有节点的内存中缓存一个值;累加器 (accumulators):只能用于做加法的变量，例如计数器(counters)或求和器(sums)。
 

RDD
---

 - RDDs 支持两种操作:转换(transformations)和 动作(actions)
 - 所有转换都是 惰性的(lazy)
 - 可以使用 持久化(persist)(或者 缓存(cache) )方法，把一个 RDD 持久化 (persist)到内存
 - 转换(Transformation) 
filter(func)
flatMap(func)
 mapPartitions(func)
mapPartitionsWithIndex(func)
sample(withReplacement, fraction, seed)
union(otherDataset)
intersection(otherDataset) distinct([numTasks])) groupByKey([numTasks])
reduceByKey(func, [numTasks])
aggregateByKey(zeroValue)(seqOp, combOp, [numTasks])
sortByKey([ascending], [numTasks])
join(otherDataset, [numTasks])
动作(Action) 
reduce(func)
collect()
count() first() take(n)
takeSample(withReplacement, num, [seed])
takeOrdered(n, [ordering]) saveAsTextFile(path)
saveAsSequenceFile(path) (Java and Scala)
saveAsObjectFile(path) (Java and Scala) countByKey()
foreach(func)

Spark Streaming
---------------

![这里写图片描述](http://img.blog.csdn.net/20170927202240124?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 

 - Spark Streaming提供了一个称为 离散流 或 DStream的高层次的抽象，它代表一个持 续的流数据
 - Spark Streaming还提供了 窗口的计算 ，它允许你通过滑动窗口对数据进行转 换
![这里写图片描述](http://img.blog.csdn.net/20170928085951718?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

Spark SQL
---------

 - Spark SQL 允许在 Spark 中执行以 SQL、HiveQL 或 Scala 表示的关系型查询语句。 此组件的核心是一种新型的  RDD:SchemaRDD。SchemaRDDs 由行对象(Row objects)  组成，同时还有一个描述每行中列元素数据类型的模式(schema)。一个 SchemaRDD 类 似于一个传统关系型数据库中的表。一   SchemaRDD 可以从现有的 RDD、Parquet 文件， JSON 数据集，或运行 HiveQL 以处理存储在 Apache   Hive 中的数据中创建。
