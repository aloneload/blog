---
title: java多线程 
categories: 
- 编程语言
tags: 
- java 
- 多线程 
---

## 多任务
多任务：操作系统可以同时运行多个任务。

进程：指在系统中能独立运行并作为资源分配的基本单位，它是由一组机器指令、数据和堆栈等组成的，是一个能独立运行的活动实体。

线程：线程是进程中的一个实体，作为系统调度和分派的基本单位。

- 解决方案：

1.启动多个进程

2.启动多个线程

3.多进程+多线程

如何调度进程和线程，完全由操作系统决定，程序自己不能决定什么时候执行，执行多长时间。

- 计算密集型 vs. IO密集型
计算密集型任务的特点是要进行大量的计算，消耗CPU资源，，全靠CPU的运算能力。这种计算密集型任务虽然也可以用多任务完成，但是任务越多，花在任务切换的时间就越多，CPU执行任务的效率就越低，所以，要最高效地利用CPU，计算密集型任务同时进行的数量应当等于CPU的核心数。

第二种任务的类型是IO密集型，涉及到网络、磁盘IO的任务都是IO密集型任务，这类任务的特点是CPU消耗很少，任务的大部分时间都在等待IO操作完成（因为IO的速度远远低于CPU和内存的速度）。对于IO密集型任务，任务越多，CPU效率越高，但也有一个限度。常见的大部分任务都是IO密集型任务，比如Web应用。IO密集型任务执行期间，99%的时间都花在IO上，花在CPU上的时间很少。合理的线程数计算公式：
    Nthreads = NCPU * UCPU * (1 + W/C)  
    其中：NCPU=Runtime.getRuntime().availableProcessors()cpu核心数,UCPU利用率在（0,1）之间，w/C为w等待时长和计算时长的比值



## 并行和并发

并发是两个任务共享时间段，并行则是两个任务在同一时间发生，比如运行在多核 CPU上。如果一个程序要运行两个任务，并且只有一个 CPU 给它们分配了不同的时间片，那么这就是并发，而不是并行。



- 多线程优点
1.资源利用更好

2.程序响应更快

- 多线程代价
1.增加资源消耗，上下文切换开销

2.设计更复杂，对于共享资源的控制

- 竞态条件与临界区
竞态条件：当两个线程竞争同一资源时，如果对资源的访问顺序敏感，就称存在竞态条件。

临界区：导致竞态条件发生的代码区称作临界区。

如何避免：在临界区中使用适当的同步就可以避免竞态条件。

在同一程序中运行多个线程本身不会导致问题，问题在于多个线程访问了相同的资源。如，同一内存区（变量，数组，或对象）、系统（数据库，web services等）或文件。实际上，这些问题只有在一或多个线程向这些资源做了写操作时才有可能发生，只要资源没有发生变化,多个线程读取相同的资源就是安全的。


## java多线程
一个线程的生命周期



- 创建一个线程
Java 提供了三种创建线程的方法：

1. 通过实现 Runnable 接口；
    ```java
    public class MyRunnable implements Runnable {
   public void run(){
    System.out.println("MyRunnable running");
   }
}
    ```
1. 通过继承 Thread 类本身；
    ```java
    public class MyThread extends Thread {
   public void run(){
     System.out.println("MyThread running");
   }
}
    ```
1. 通过 Callable 和 Future 创建线程。
- 创建线程的三种方式的对比
1. 采用实现 Runnable、Callable 接口的方式创见多线程时，线程类只是实现了 Runnable 接口或 Callable 接口，还可以继承其他类。
2. 使用继承 Thread 类的方式创建多线程时，编写简单，如果需要访问当前线程，则无需使用 Thread.currentThread() 方法，直接使用 this 即可获得当前线程。
* Thread 方法
下表列出了Thread类的一些重要方法：

```
序号	方法描述
1	public void start()
使该线程开始执行；Java 虚拟机调用该线程的 run 方法。
2	public void run()
如果该线程是使用独立的 Runnable 运行对象构造的，则调用该 Runnable 对象的 run 方法；否则，该方法不执行任何操作并返回。
3	public final void setName(String name)
改变线程名称，使之与参数 name 相同。
4	public final void setPriority(int priority)
 更改线程的优先级。
5	public final void setDaemon(boolean on)
将该线程标记为守护线程或用户线程。
6	public final void join(long millisec)
等待该线程终止的时间最长为 millis 毫秒。
7	public void interrupt()
中断线程。
8	public final boolean isAlive()
测试线程是否处于活动状态。
```


 上述方法是被Thread对象调用的。下面的方法是Thread类的静态方法。


```
序号	方法描述
1	public static void yield()
暂停当前正在执行的线程对象，并执行其他线程。
2	public static void sleep(long millisec)
在指定的毫秒数内让当前正在执行的线程休眠（暂停执行），此操作受到系统计时器和调度程序精度和准确性的影响。
3	public static boolean holdsLock(Object x)
当且仅当当前线程在指定的对象上保持监视器锁时，才返回 true。
4	public static Thread currentThread()
返回对当前正在执行的线程对象的引用。
5	public static void dumpStack()
将当前线程的堆栈跟踪打印至标准错误流。
```
## java 同步
    Java 同步块（synchronized block）用来标记方法或者代码块是同步的。Java 同步块用来避免竞争。Java 中的同步块用 synchronized 标记。同步块在 Java 中是同步在某个对象上。所有同步在一个对象上的同步块在同时只能被一个线程进入并执行操作。所有其他等待进入该同步块的线程将被阻塞，直到执行该同步块中的线程退出。
## 线程通信

    1. 通过共享对象通信
    1. 忙等待
    1. wait()，notify()和 notifyAll()
    1. 不要对常量字符串或全局对象调用 wait()，在 wait()/notify()机制中，不要使用全局对象，字符串常量等。应该使用对应唯一的对象
    1. 丢失的信号
        为了避免信号丢失， 用一个变量来保存是否被通知过。在 notify 前，设置自己已经被通知过。在 wait 后，设置自己没有被通知过，需要等待通知
    1. 假唤醒(由于莫名其妙的原因，线程有可能在没有调用过 notify()和 notifyAll()的情况下醒来)解决为添加while判断，以下代码为最佳实践

```java
public class PoolThread {
    MonitorObject myMonitorObject = new MonitorObject();
    boolean wasSignalled = false;

    public void doWait() {
        synchronized (myMonitorObject) {
            while (!wasSignalled) {
                try {
                    myMonitorObject.wait();
                } catch (InterruptedException e) {
                }
            }
            //clear signal and continue running.
            wasSignalled = false;
        }
    }

    public void doNotify() {
        synchronized (myMonitorObject) {
            wasSignalled = true;
            myMonitorObject.notify();
        }
    }
}
```

- 管程 (英语：Monitors，也称为监视器) 是对多个工作线程实现互斥访问共享资源的对象或模块。这些共享资源一般是硬件设备或一群变量。管程实现了在一个时间点，最多只有一个线程在执行它的某个子程序   
- 避免死锁

    1. 加锁顺序
    1. 加锁时限
    1. 死锁检测
    
## 阻塞与非阻塞算法
- 阻塞算法：阻塞线程直到请求操作可以被执行例如，java.util.concurrent.BlockingQueue阻塞数据结构
- 非阻塞算法：通知请求线程操作不能够被执行，并返回（AtomicBoolean，AtomicInteger，AtomicLong，AtomicReference 都是非阻塞数据结构）
- 非阻塞算法的好处：
    
    1. 当一个线程请求不能够被执行时，不再阻塞，请求线程有一个自由选择
    1. 一个线程阻塞不会引起其他线程挂起，不会发生死锁（活锁依然会产生）
    1. 由于挂起的线程切换较少，性能提高
    1. 如果线程阻塞，将会竞争下一轮的notify，如果不阻塞，将会更快的执行
## Volatile 变量

olatile 变量最新的值总是对跑在其他 CPU 上的线程可见。其他线程每次会从主存中读取变量的值，而不是比如线程所运行 CPU 的 CPU 缓存中。（可见性，并不保证原子性）

- 单线程写，多线程读
    ```java
    public class SingleWriterCounter {
        private volatile long count=0;
        public void  inc(){
            this.count++;
        }
        public long getCount(){
            return this.count;
        }
    }
    ```
    只有一个线程调用inc()（单线程保证原子性）,多个线程调用getCount()（Volatile可见性保证）,线程安全，上述数据机构可以衍生多个volatile变量，只需保证只有一个线程写即可
- 多线程写

    如果确实需要多线程写一个变量，必须同步

    ```java
    private  long count=0;
    public synchronized  void   inc(){
        this.count++;
    }
    public synchronized long getCount(){
        return this.count;
    }
    ```
    更好的方式：java原子变量来代替上述同步方法()
    ```java
    private AtomicLong count = new AtomicLong(0);

    public void inc() {
        boolean updated = false;
        while (!updated) {
            long prevCount = this.count.get();
            updated = this.count.compareAndSet(prevCount, prevCount + 1);
        }
    }

    public long getCount() {
        return this.count.get();
    }
    ```
    上述代码compareAndSet是一个原子操作，如果操作失败，将会进入下次循环（自旋），直到操作成功，这部分代码并未加锁，称为乐观锁，乐观假定只有一个线程操作，如果有多个线程同时操作，会丢弃目前线程所获取到的值，但是任然不使用锁。可想而知，乐观锁适用于共享内存不是很高的场景，如果共享的数据结构有多个变量，可用AtomicReference 来达到目的，但是，如果数据结构非常复杂，比如很长的队列，也不适用于此场景
- CAS
    CAS（Compare and swap）比较和替换是设计并发算法时用到的一种技术。简单来说，比较和替换是使用一个期望值和一个变量的当前值进行比较，如果当前变量的值与我们期望的值相等，就使用一个新值替换当前变量的值。适用java提供的cas可在硬件层面是支持，运行代码更快
- ABA问题
    A-B-A 问题指的是一个变量被从 A 修改到了 B，然后又被修改回 A 的一种情景。其他线程对于这种情况却一无所知。
    解决：增加类似版本或者计数器的变量。java提供AtomicStampedReference 类
    ```java
     public static class IntendedModification {
        public AtomicBoolean completed = new AtomicBoolean(false);
    }

    private AtomicStampedReference<IntendedModification> ongoinMod = new AtomicStampedReference<IntendedModification>(new IntendedModification(), 0);

    public void modify() {

        boolean modified = false;
        while (!modified) {
            IntendedModification currentlyOngoingMod = ongoinMod.getReference();
            int stamp = ongoinMod.getStamp();
            IntendedModification newMod = new IntendedModification();
            newMod.completed = new AtomicBoolean(true);

            modified = ongoinMod.compareAndSet(currentlyOngoingMod, newMod, stamp, stamp + 1);


        }
    }
    ```
    上述非阻塞算法Java 已经提供了实现，ConcurrentLinkedQueue（offer(E e) 将指定元素插入此队列的尾部。poll()  获取并移除此队列的头，如果此队列为空，则返回 null。）

- 同步器

    众多同步器（锁，信号量，阻塞队列）用来保护临界区的代码，其实现会包含下列部分过程

    1. 状态
    1. 访问条件
    1. 状态变化
    1. 通知策略
    1. Test-and-Set 方法（或者set方法）
    
## 饥饿和公平
- Java 中导致饥饿的原因
    1. 高优先级线程吞噬所有的低优先级线程的 CPU 时间
    1. 线程被永久堵塞在一个等待进入同步块的状态
    1. 线程在等待一个本身(在其上调用 wait())也处于永久等待完成的对象
- 在 Java 中实现公平性方案，需要:
   1.  使用锁，而不是同步块。
    1. 公平锁。
    1. 注意性能方面。
- Slipped Conditions：所谓 Slipped conditions，就是说， 从一个线程检查某一特定条件到该线程操作此条件期间，这个条件已经被其它线程改变，导致第一个线程在该条件上执行了错误的操作
## 代码实践
### Semaphore 
- Semaphore(int count):创建拥有count个许可证的信号量
- acquire()/acquire(int num):获取1/num个许可证
- release()/release(int num):释放1/num个许可证
    ```java
    public class SeDemo {
        public static void main(String[] args) {
            Semaphore semaphore = new Semaphore(2);
            Person a = new Person(semaphore, "A");
            Person b = new Person(semaphore, "B");
            Person c = new Person(semaphore, "C");
            a.start();
            b.start();
            c.start();
        }
    }

    class Person extends Thread {
        private Semaphore semaphore;

        public Person(Semaphore semaphore, String name) {
            this.semaphore = semaphore;
            setName(name);
        }

        public void run() {
            System.out.println(getName() + "is waitng...");
            try {
                semaphore.acquire();
                System.out.println(getName() + "is servering...");
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(getName() + "is done!");
            semaphore.release();
        }
    }
    ```
### CoundDownLatch
- CountDownLatch(int count):必须发生count个数量才可以打开锁存器
- await();等待锁存器
- contDown():触发事件

    ```java   
    public class CDDemo {
        public static void main(String[] args) {
            CountDownLatch countDownLatch = new CountDownLatch(3);
            new Racer(countDownLatch, "A").start();
            new Racer(countDownLatch, "B").start();
            new Racer(countDownLatch, "C").start();
            for (int i = 0; i < 3; i++) {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(3 - i);
                countDownLatch.countDown();
                if (i == 2) {
                    System.out.println("start");
                }

            }
        }

    }

    class Racer extends Thread {
        private final CountDownLatch countDownLatch;

        public Racer(CountDownLatch countDownLatch, String name) {
            this.countDownLatch = countDownLatch;
            setName(name);

        }

        @Override
        public void run() {
            try {
                countDownLatch.await();
                for (int i = 0; i < 3; i++) {
                    System.out.println(getName() + ":" + i);

                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    ```
### CyclicBarrier
- CyclicBarrier(int num):等待线程的数量
- cyclicBarrier(int num,Runnable action):等待线程的数量以及所有线程到达后的操作
- await():到达临界点后暂停线程
    ```java
    public class CDDemo {
        public static void main(String[] args) {
            CyclicBarrier cyclicBarrier = new CyclicBarrier(3, new Runnable() {
                @Override
                public void run() {
                    System.out.println("game start");
                }
            });
            new Player(cyclicBarrier, "A").start();
            new Player(cyclicBarrier, "B").start();
            //new Player(cyclicBarrier,"C").start();
        }

    }

    class Player extends Thread {
        private CyclicBarrier cyclicBarrier;

        public Player(CyclicBarrier cyclicBarrier, String name) {
            setName(name);
            this.cyclicBarrier = cyclicBarrier;
        }

        @Override
        public void run() {
            try {
                cyclicBarrier.await();
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (BrokenBarrierException e) {
                e.printStackTrace();
            }
        }
    }
    ```
### Exchanger
- Exchanger\<v\>:指定进行交换的数据类型
- V exchange(V object):等待线程到达，交换数据 
    ```java
        class A extends Thread {
        private Exchanger<String> ex;

        public A(Exchanger<String> ex) {
            this.ex = ex;
        }

        @Override
        public void run() {
            String str = null;
            try {
                str = ex.exchange("Hello");
                System.out.println(getName() + ":" + str);
                str = ex.exchange("world");
                System.out.println(getName() + ":" + str);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

        }

    }

    class B extends Thread {
        private Exchanger<String> ex;

        public B(Exchanger<String> ex) {
            this.ex = ex;
        }

        @Override
        public void run() {
            String str = null;
            try {
                str = ex.exchange("Hi");
                System.out.println(getName() + ":" + str);
                str = ex.exchange("worldB");
                System.out.println(getName() + ":" + str);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

        }

    }
    ```
### Phaser
- Phaser()/Phaser(int num); 使用指定0/num个party创建Phaser
- register():注册party
- arriveAndAdvance()：到达时等待所有party到达
- arriveAndDeregister(): 到达时注销线程自己
    ```java
    public class PDemo {
        public static void main(String[] args) {
            Phaser phaser = new Phaser(1);
            System.out.println("starting...");
            new Worker(phaser, "fuwuyuan").start();
            new Worker(phaser, "chushi").start();
            new Worker(phaser, "shangcaiyuan").start();
            for (int i = 0; i <= 3; i++) {
                phaser.arriveAndAwaitAdvance();
                System.out.println("Order" + i + "finished");

            }
            phaser.arriveAndDeregister();
        }
    }

    class Worker extends Thread {
        private Phaser phaser;

        public Worker(Phaser phaser, String name) {
            setName(name);
            this.phaser = phaser;
            phaser.register();
        }

        @Override
        public void run() {
            for (int i = 0; i <= 3; i++) {
                System.out.println("curent ordrer is :" + getName() + i);
                if (i == 3) {
                    phaser.arriveAndDeregister();
                } else {
                    phaser.arriveAndAwaitAdvance();
                }
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }

            }
        }
    }
    ```
### 执行器
- 用于启动并控制线程的执行
- 核心接口为Executor，包含一个execute(Runnable)用于指定被执行的线程
- ExecuteService接口用于控制线程执行和管理线程
- ThreadPoolEcecutor/ScheduledThreadPoolExecutor/ForkJoinPool
- Callable\<v\>:表示具有返回值的线程
- Future\<V\>:表示Callable的返回值
    ```java
        
    public class ExecutorDemo {
        public static void main(String[] args) {
            ExecutorService es = Executors.newFixedThreadPool(2);
            Future<Integer> result1 = es.submit(new Task(1, 50));
            Future<Integer> result2 = es.submit(new Task(50, 101));
            try {
                System.out.println(result1.get() + result2.get());
            } catch (InterruptedException e) {
                e.printStackTrace();
            } catch (ExecutionException e) {
                e.printStackTrace();
            }
        }
    }

    class Task implements Callable<Integer> {
        private int begin;
        private int end;

        public Task(int begin, int end) {
            this.begin = begin;
            this.end = end;
        }

        @Override
        public Integer call() throws Exception {
            int sum = 0;
            for (int i = begin; i < end; i++) {
                sum += i;
            }
            return sum;
        }
    }
    ```
    lock用法
    ```java
    class Data{
        int i=0;
        Lock lock= new   ReentrantLock();
        void operate(){
            lock.lock();
            i++;
            System.out.println(i);
            lock.unlock();
        }
    }
    ```
    java ForkJoinPool使用
    ```java
    
    public class ForkDemo {
        public static void main(String[] args) throws ExecutionException, InterruptedException {
            ForkJoinPool forkJoinPool = new ForkJoinPool();

            ForkJoinTask<Long> result = forkJoinPool.submit(new MTask(0, 100000001));
            System.out.println(result.get());
        }
    }

    class MTask extends RecursiveTask<Long> {
        private int begin, end;
        static final int M = 1000;

        public MTask(int begin, int end) {
            this.begin = begin;
            this.end = end;
        }

        @Override
        protected Long compute() {
            long sum = 0;
            if (end - begin <= M) {
                for (int i = begin; i < end; i++) {
                    sum += i;
                }
            } else {
                int mid = (begin + end) / 2;
                MTask left = new MTask(begin, mid);
                left.fork();
                MTask right = new MTask(mid + 1, end);
                right.fork();
                Long r1 = left.join();
                Long r2 = right.join();
                sum = r1 + r2;

            }
            return sum;
        }

    }

    ```

## 线程池
在Java中，线程池的概念是Executor这个接口，具体实现为ThreadPoolExecutor类
- 构造参数
    1. int corePoolSize => 该线程池中核心线程数最大值
    1. int maximumPoolSize该线程池中线程总数最大值
    1. long keepAliveTime非核心线程闲置超时时长
    1. TimeUnit unit keepAliveTime的单位，TimeUnit是一个枚举类型
    1. BlockingQueue<Runnable> workQueue该线程池中的任务队列：维护着等待执行的Runnable对象
        常用的workQueue类型：

            SynchronousQueue：这个队列接收到任务的时候，会直接提交给线程处理，而不保留它，如果所有线程都在工作怎么办？那就新建一个线程来处理这个任务！所以为了保证不出现<线程数达到了maximumPoolSize而不能新建线程>的错误，使用这个类型队列的时候，maximumPoolSize一般指定成Integer.MAX_VALUE，即无限大

            LinkedBlockingQueue：这个队列接收到任务的时候，如果当前线程数小于核心线程数，则新建线程(核心线程)处理任务；如果当前线程数等于核心线程数，则进入队列等待。由于这个队列没有最大值限制，即所有超过核心线程数的任务都将被添加到队列中，这也就导致了maximumPoolSize的设定失效，因为总线程数永远不会超过corePoolSize

            ArrayBlockingQueue：可以限定队列的长度，接收到任务的时候，如果没有达到corePoolSize的值，则新建线程(核心线程)执行任务，如果达到了，则入队等候，如果队列已满，则新建线程(非核心线程)执行任务，又如果总线程数到了maximumPoolSize，并且队列也满了，则发生错误

            DelayQueue：队列内元素必须实现Delayed接口，这就意味着你传进去的任务必须先实现Delayed接口。这个队列接收到任务时，首先先入队，只有达到了指定的延时时间，才会执行任务

- ThreadPoolExecutor.execute(Runnable command)添加任务
- 常见四种线程池，Java通过Executors提供了四种线程池，这四种线程池都是直接或间接配置ThreadPoolExecutor的参数实现的
    1. CachedThreadPool()线程数无限制
    1. FixedThreadPool()固定线程数超出的线程会在队列中等待
    1. ScheduledThreadPool()支持定时及周期性任务执行
    1. SingleThreadExecutor()所有任务按照指定顺序执行，即遵循队列的入队出队规则

## AQS
维护着一个volatile int state 和一个FIFO线程等待队列
- acquire
    ```java
    while(synchronization state does not allow acquire){
        enqueue current thread if not already quened;
        plssible blok current thread;
    }
    dequene current thread if it was queued;
    ```
- release
    ```java
    update synchronization state;
    if (state may permit a blocked thread to acqire){
        unblock one or more queued threads
    }
    ```
- AQS在功能上有独占控制和共享控制两种功能
## 备注
- 什么是线程安全的代码？（不具有原子性的代码都不是线程安全的）
- 线程逃逸规则
    （如果一个资源（对象，数组，文件，数据库连接，套接字）的创建，使用，销毁（销毁”指不再有引用指向对象）都在同一个线程内完成，且永远不会脱离该线程的控制，则该资源的使用就是线程安全的。）
- “不变”（Immutable）和“只读”（Read Only）是不同的。当一个变量是“只读”时，变量的值不能直接改变，但是可以在其它变量发生改变的时候发生改变。
- 自旋锁是为了防止假唤醒(下面while),wasSignalled是为了防止唤醒过期
    ```java
    public class MyWaitNotify3{

    MonitorObject myMonitorObject = new MonitorObject();
    boolean wasSignalled = false;

    public void doWait(){
        synchronized(myMonitorObject){
        while(!wasSignalled){
            try{
            myMonitorObject.wait();
            } catch(InterruptedException e){...}
        }
        //clear signal and continue running.
        wasSignalled = false;
        }
    }

    public void doNotify(){
        synchronized(myMonitorObject){
        wasSignalled = true;
        myMonitorObject.notify();
        }
    }
    }
    ```