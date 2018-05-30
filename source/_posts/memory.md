---
title: 深入并发
date: 2018-05-29 17:41:52
categories: 编程语言
tags: 
    java内存模型
    golang内存模型
    并发

---
# 并发编程模型的分类

执行体：可以是线程也可以是协程，执行代码的承载体
    1. 共享内存：执行体之间通过写-读内存中的公共状态来隐式进行通信
    1. 消息传递：执行体间必须通过明确的发送消息来显式进行通信

___
顺序一致性内存模型是一个理论参考模型 ,处理器内存模型是硬件级的内存模型，JMM是一个语言级的内存模型

# 顺序一致性内存模型
    1. 一个线程中的所有操作必须按照程序的顺序来执行
    1. （不管程序是否同步）所有线程都只能看到一个单一的操作执行顺序
在顺序一致性模型中，所有操作完全按程序的顺序串行执行

 # 处理器内存模型
 ![](cpu.png) ![](cpu2.png)
 涉及术语：Socket|Processor，core，HT超线程，DRAM,SRAM对应上图对号入座

## MESI协议：解决内存可见性
 ![](mesi.png)
 
## 重排序
在计算机中，软件技术和硬件技术有一个共同的目标：在不改变程序执行结果的前提下，尽可能的开发并行度
    1. 编译器优化
    1. 指令级并行
    1. 内存重排序(现代的处理器都会允许对写-读操作重排序)

    对于1，JMM的编译器重排序规则会禁止特定类型的编译器重排序,对于2,3属于处理器重排序，JMM通过内存屏障指令来禁止特定类型的处理器重排。对于3，`a=1;b=2`如果处理器先执行`a=1`,写入缓存，再读取b，再将a=1刷入内存，从内存角度看，先读取b,然后设置的a=1

## 内存屏障
处理器使用写缓冲区通过批处理的方式刷新写缓冲区，允许对写-读操作重排序，但仅对当前处理器可见，会影响其他处理器对内存的操作，因此需要有种指令阻止这种重排序，有四种LoadLoad ，StoreStore ，LoadStore ，StoreLoad ，例如StoreLoad 意味着store happen before load

    根据对不同类型读/写操作组合的执行顺序的放松,分以下几种类型：

        1. TSO：放松程序中写-读操作的顺序
        2. PSO：在前面1的基础上，继续放松程序中写-写操作的顺序
        3. RMO和PowerPC：在前面1和2的基础上，继续放松程序中读-写和读-读操作的顺序

## 如何实现原子性
在计算机中，数据通过总线在处理器和内存之间传递。每次处理器和内存之间的数据传递都是通过一系列步骤来完成的，这一系列步骤称之为总线事务（bus transaction）。总线事务包括读事务（read transaction）和写事务（write transaction）。读事务从内存传送数据到处理器，写事务从处理器传送数据到内存，每个事务会读/写内存中一个或多个物理上连续的字。这里的关键是，总线会同步试图并发使用总线的事务。在一个处理器执行总线事务期间，总线会禁止其它所有的处理器和I/O设备执行内存的读/写。总线的这些工作机制可以把所有处理器对内存的访问以串行化的方式来执行；在任意时间点，最多只能有一个处理器能访问内存。这个特性确保了单个总线事务之中的内存读/写操作具有原子性

## cache 伪共享
由于CPU加载数据最小单位cacheLine，所以写同一个line的不同属性会发生cachemiss，jdk8采用@Contended
    ```java
    @Contended
    public class App {
        public int a;
        public int b;
    }
    ```
 # java内存模型

Java线程之间的通信由Java内存模型（本文简称为JMM）控制，JMM决定一个线程对共享变量的写入何时对另一个线程可见（记住，这是语言层面的内存抽象模型）
![](java1.png)
从整体来看，是线程通信过程必须要经过主内存。JMM通过控制主内存与每个线程的本地内存之间的交互，来为java程序员提供以下内存可见性保证

    1. 单线程程序，JMM保证执行结果与该程序在顺序一致性模型中的执行结果相同
    2. 正确同步的多线程程序，JMM通过限制编译器和处理器的重排序来为程序员提供内存可见性保证

## happens-before
1. 程序顺序规则：一个线程中的每个操作，happens- before 于该线程中的任意后续操作
1. 传递性：如果A happens- before B，且B happens- before C，那么A happens- before C
1. volatile变量规则：对一个volatile域的写，happens- before 于任意后续对这个volatile域的读
    ```java
        class Example {
        int a = 0;
        volatile boolean flag = false;

        public void writer() {
            a = 1; // 1
            flag = true; // 2
        }

        public void reader() {
            if (flag) {                //3
                int i =  a;           //4
            }
        }
        }
    ```
    假设线程A执行writer()方法之后，线程B执行reader()方法。根据happens before规则，这个过程建立的happens before 关系可以分为两类

        1.  根据程序次序规则，1 happens before 2; 3 happens before 4。
        2.  根据volatile规则，2 happens before 3。
        3.  根据happens before 的传递性规则，1 happens before 4。
    volatile写的内存语义

        - 当写一个volatile变量时，JMM会把该线程对应的本地内存中的共享变量刷新到主内存
        - 当读一个volatile变量时，JMM会把该线程对应的本地内存置为无效。线程接下来将从主内存中读取共享变量

1. 监视器锁规则：对一个监视器锁的解锁，happens- before 于随后对这个监视器锁的加锁
    ```java
    class Example {
        int a = 0;

        public synchronized void writer() { // 1
            a++; // 2
        } // 3

        public synchronized void reader() { // 4
            int i = a; // 5

        }
    }
    ```
    假设线程A执行writer()方法，随后线程B执行reader()方法。根据happens before规则，这个过程包含的happens before 关系可以分为两类：

    - 根据程序次序规则，1 happens before 2, 2 happens before 3; 4 happens before 5, 5 happens before 6。
    - 根据监视器锁规则，3 happens before 4。
    - 根据happens before 的传递性，2 happens before 5。

    锁释放和获取的内存语义
    - 当线程释放锁时，JMM会把该线程对应的本地内存中的共享变量刷新到主内存中
    - 当线程获取锁时，JMM会把该线程对应的本地内存置为无效。从而使得被监视器保护的临界区代码必须要从主内存中去读取共享变量

**注**：happens-before仅仅要求前一个操作（执行的结果）对后一个操作可见，并不意味着前一个操作必须要在后一个操作之前执行，比如单线程中a=3;b=4;b的赋值可能先于a的赋值发生

concurrent包的源代码实现，会发现一个通用化的实现模式

1. 首先，声明共享变量为volatile；
1. 然后，使用CAS的原子条件更新来实现线程之间的同步；
1. 同时，配合以volatile的读/写和CAS所具有的volatile读和写的内存语义来实现线程之间的通信。


## as-if-serial
不管怎么重排序，（单线程）程序的执行结果不能被改变。编译器，runtime 和处理器都必须遵守as-if-serial语义，如果两个操作访问同一个变量，且这两个操作中有一个为写操作，此时这两个操作之间就存在数据依赖性。单个处理器中执行的指令序列和单个线程中执行的操作不会改变这种数据依赖性，多线程程序中，对存在控制依赖的操作重排序，可能会改变程序的执行结果，因此顺序性保证交由程序员

## 常见java通信方式
   1. A线程写volatile变量，随后B线程读这个volatile变量。
   1. A线程写volatile变量，随后B线程用CAS更新这个volatile变量。
   1.  A线程用CAS更新一个volatile变量，随后B线程用CAS更新这个volatile变量。
   1. A线程用CAS更新一个volatile变量，随后B线程读这个volatile变量。

 Java的CAS会使用现代处理器上提供的高效机器级别原子指令，这些原子指令以原子方式对内存执行读-改-写操作，这是在多处理器中实现同步的关键


# go内存模型

执行体为Goroutine,内存模型的目的是定义变量的读写在执行体中的可见性，编译期重排和CPU指令重排导致代码执行逻辑和书写逻辑不一致，go内存模型基于CSP，goroutine通过channel通信

## Happens Before
Happens-before用来指明Go程序里的内存操作的局部顺序。如果一个内存操作事件e1 happens-before e2，则e2 happens-after e1也成立；如果e1不是happens-before e2,也不是happens-after e2，则e1和e2是并发的。

从本质上来讲，happens-before规则确定了CPU缓冲和主存的同步时间点（通过内存屏障等指令），从而使得对变量的读写顺序可被确定–也就是我们通常说的“同步”。

-  单独的goroutine里面，虽然有重排，但是最终结果和代码顺序执行结果一致
    ```go
    var a, b, c int

    func main() {
        a = 1
        b = 2
        c = a + 2
        log.Println(a, b, c)
    }
    ```
    a=1和b=2执行顺序不保证，但是c=a+2 happen before a=1

在多个Goroutine里如果要访问一个共享变量，我们就必须使用同步工具来建立happens-before条件，来保证对该变量的读操作能读到期望的修改值。

 以下是具体的可被利用的Go语言的happens-before规则

1. 如果package p 引用了package q，q的init()方法 happens-before p
1.  main.main()方法 happens-after所有package的init()方法结束
1. go语句创建新的goroutine happens-before 该goroutine执行
1. Goroutine的退出并不保证happens-before任何事件
1. 对一个缓冲Channel的发送操作(send) happens-before 相应Channel的接收操作完成
1. 关闭一个Channel happens-before 从该Channel接收到最后的返回值0
    ```go
    //a = "hello, world" happens-before c <- 0，print(a) happens-after <-c， 根据上面的规则1）以及happens-before的可传递性，a = "hello, world" happens-beforeprint(a)
    var c = make(chan int, 10)
    var a string

    func f() {
        a = "hello, world"
        //c <- 0
        close(c)
        //关闭操作在<-c接收到0之前发送
        
    }
    func main() {
        go f()
        <-c
        print(a)
    }
    ```
1. 不带缓冲的Channel的接收操作（receive） happens-before 相应Channel的发送操作完成
    ```go
    //c是不带缓冲的Channel，a = "hello, world" happens-before <-c happens-beforec <- 0 happens-before print(a)， 但如果c是缓冲队列，如定义c = make(chan int, 1), 那结果就不确定了
    var c = make(chan int)
    var a string

    func f() {
        a = "hello, world"
        <-c
    }
    func main() {
        go f()
        c <- 0
        print(a)
    }
    ```
1. 任何sync.Mutex或sync.RWMutex 变量（l），定义 n < m， 第n次 l.Unlock() happens-before 第m次l.lock()调用返回
    ```go
    //a = "hello, world" happens-before l.Unlock() happens-before 第二个 l.Lock() happens-beforeprint(a)
    var l sync.Mutex
    var a string

    func f() {
        a = "hello, world"
        l.Unlock()
    }
    func main() {
        l.Lock()
        go f()
        l.Lock()
        print(a)
}
    ```
1. once.Do(f)中的f() happens-before 任何多个once.Do(f)调用的返回，且f()有且只有一次调用
    ```go
    //调用两次doprint()，但实际上setup只会执行一次，并且并发的once.Do(setup)都会等待setup返回后再继续执行
    var a string
    var once sync.Once
    func setup() {
        a = "hello, world"
        fmt.Println(1)
    }
    func doprint() {
        once.Do(setup)
        fmt.Println(2)
    }
    func main() {
        go doprint()
        go doprint()
        time.Sleep(time.Second)
    }
    ```

## 错误的同步
感受下以下代码的捉摸不定
    ```go

    var a, b int
    //输出四种可能的结果
    func f() {
        a = 1
        b = 2
    }

    func g() {
        print(b)
        print(a)
    }

    func main() {
        go f()
        g()
    }

```
**目的是告诉我们一定要显式地使用同步**


# 参考

> [并发编程网](http://ifeve.com/java-memory-model-0/)

> [Go的内存模型](https://studygolang.com/articles/1597)





