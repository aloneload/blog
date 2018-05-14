---
title: JVM与GC
categories: 
- 编程语言
tags: 
- java 
- JVM 
---
JVM体系结构
----

![这里写图片描述](http://img.blog.csdn.net/20170926190651863?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

**GC作用在粉红色区域，即方法区和堆**

GC算法
----

 - 常采用两种算法：引用计数算法和基于root根节点图搜索的算法（跟踪算法）
 - 引用算法是每次引用对象计数器加一，gc回收计数为0的对象，缺点是较难处理循环引用（可通过弱软引用解除循环中一方），Python有其实现
 - 跟踪算法从称为gc root的根节点基于图搜索，最后回收不连通图的对象


其中，跟踪算法考虑以下场景
引入：如果现在要清理D:盘可有三种策略：
1.如果无效文件很多，有用文件很少，可将有用文件选定后复制到C:,在格式化D盘，之前文件拷回去
2.如果无效文件很少，有用文件很多，可选定无效文件后直接删除
3.如果2执行好多次以后，比如风景文件夹下有人物照片，人物照片底下有家人照片，这时候可能选择盘下所有的人物照片放到人物文件夹下，相当于整理文件

 - 上述1为复制（copying）,2标记-清除（mark-sweep）,3标记-压缩（mark-compact）

gc涉及内存图
![这里写图片描述](http://img.blog.csdn.net/20170926193247759?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
1.复制算法发生在新生代（New/Young）,因为大部分对象在伊甸园（Eden）刚创建就夭折了，选取eden,s0存活对象复制到s1(存活1区),回收这两个区，交换两个存活区（也叫from to 区）
2.当对象进行上述1默认15次回收后，对象晋升到old区，或者对象非常大（vip）,将直接在年老代创建，老年到刚开始会实行标记-清理
3.当2进行多次，内存碎片过多，大对象将难以创建，于是实行标记整理到内存一端，留下可用连续空间

 - 根据各代算法应用不同的gc算法，提高gc效率


GC类型
1.Minor GC 针对新生代的GC
2.Major GC 针对旧生代的GC
3.Full GC 针对永久代，新生代，旧生代三者的GC

搜集器
![这里写图片描述](http://img.blog.csdn.net/20170926195137623?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

1.串行收集器（serial），顺序搜集，单线程资源好控制，stop the world(stw)
2.并行收集器（parall），并行搜集，回收速度快，资源同步复杂，stop the world
3.并发收集器（Concurrent），不中断应用，资源更为复杂

Young Gen :
均采用复制算法，eden不足时触发，eden官方建议此值为堆的3/8
serial copying:单cpu32位系统client模式均采用此
parallel scavenge:多cpu,64位,server默认模式
parNew：serial copying多线程版本，可搭配cms
Old Gen:
Serial MSC:串行使用标记清除算法整理算法，client或者32默认
Parallel Compacting:并行标记整理算法，并行标记串行计算对象要移动的地址，最后并行整理，server或者多核默认
Concurrent Mark Sweep(CMS):并发使用标记清除算法，暂停时间短，但相当复杂，GC总时间长，暂停时间主要是发生在GC root 刚开始以及重新标记阶段，算法简述：1串行标记gc root 直达对象（stw）2并发标记可达对象3调整2期间应用产生的新建及变化对象(stw)4并行回收

组合选择
单核小内存单机程序
```
-XX:+UseSerialGC
```
多CPU，吞吐量优先，计算型
```
-XX:UseParellGC
-XX:UseParellOldGC
```
多CPU，响应优先，低暂停
```
-XX:UseConcMarkSweepGC
-XX:ParNewGC
```

调优
--
堆：越大越好，过大会增加GC时间和产生垃圾对象，堆每次调整都会发生Full GC,因此一般将以下两值设置为同样大小，但是要预防系统宕机，可将最大堆设置的大点
```
-Xms=1024MB:堆的最小值
-Xmx=1024MB:堆得最大值
```
新生代：增大Eden会减低Minor GC的频率，一般固定新生代大小
由于永久代和老年代回收会触发Full GC,尽可能让对象待在survivor
```
-XX:NewSize=1024MB 新生代初始大小
-XX:MaxNewSize=1024MB 新生代最大值
-XX:NewRadio=m New与Old的比值
-Xmn:1024MB 新生代大小
-XX:SurvivorRadio=m Eden和Survivor的比值，官方建议8：1，但自己测试好多都是1：1？

```
旧生代（随便叫了）：太大，单次GC时间过长，太小，GC频率高

监控
--
JVM参数
```
-XX:+PrintGC 输出GC简要信息
-XX:+PrintGCDetail 输出GC详单
-XX:+PrintGCTimeStamps 输出GC时间戳
-XX:+PrintGCAppicationStopedTime 输出GC暂停时间
```
命令行工具
top观察CPU负载
![这里写图片描述](http://img.blog.csdn.net/20170927160751005?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
vmstat观察上下文切换
free -m 查看内存
jps 找到java程序的pid
![这里写图片描述](http://img.blog.csdn.net/20170927152245828?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
jstack主要用来查看某个Java进程内的线程堆栈信息
 jmap（Memory Map）和jhat（Java Heap Analysis Tool）
 ![这里写图片描述](http://img.blog.csdn.net/20170927153239053?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 dump 用法：
 jmap -dump:format=b,file=dumpFileName pid
 jhat查看 ：jhat -port 7000 dumpFileName
 浏览器输入host:port 查看
 ![这里写图片描述](http://img.blog.csdn.net/20170927155106557?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 jstat（JVM统计监测工具
 ![这里写图片描述](http://img.blog.csdn.net/20170927153855063?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
 


```
S0C、S1C、S0U、S1U：Survivor 0/1区容量（Capacity）和使用量（Used）
EC、EU：Eden区容量和使用量
OC、OU：年老代容量和使用量
PC、PU：永久代容量和使用量
YGC、YGT：年轻代GC次数和GC耗时
FGC、FGCT：Full GC次数和Full GC耗时
GCT：GC总耗时
```

 Jconsole
 
 
有以下代码
```
public class JvmTest {

	public static void main(String[] args) throws InterruptedException {
		List<JvmTest> global = new ArrayList<>();
		while (true) {
			global.add(new JvmTest());
			Thread.sleep(1);
		}
	}
}
```
![这里写图片描述](http://img.blog.csdn.net/20170927163230726?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYWxvbmVsb2Fk/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
一般出现阶梯上升或波形上升时，内存迟早要耗尽，常见的就是全局集合中的对象未释放，如图所示，jvm8移除了持久代，非堆增加了几个概念。
观察以上代码10分钟，分别切换Eden，survivor，old,便能轻易理解垃圾回收的整个过程，由于不停地new对象，因此非堆中的code chche 也逐渐增多。
