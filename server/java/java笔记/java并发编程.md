---
title: java并发编程
date: 2021-9-29 10:17:09
author: 小蝌蚪
img: 
coverImg: 
top: false
cover: false
toc: true
mathjax: false
password: 
summary: 介绍java并发编程的知识
tags: [并发编程]
categories: [开发知识, java]
comments: false
---



## java并发编程(JUC)

### JUC相关知识简介

#### 定义

> JUC就是 java.util .concurrent 工具包的简称。这是一个JDK 1.5 开始出现的处理线程的工具包，

#### 进程,线程与程序

##### 进程

系统进行资源分配和调度的基本单位,是操作系统结构的基础,是线程的容器。

##### 程序

程序是指令、数据及其组织形式的描述，进程是程序的实体。

##### 线程

是操作系统能够进行运算调度的最小单位,是进程中的实际运作单位。一个进程中可以并发多个线程，每条线程并行执行不同的任务。 

###### 线程的状态

- new 新建
- runnable  准备就绪
- blocked 阻塞
- wating  不见不散
- timed_waiting  过时不候
- terminaled  终结

######  wait/sleep 的区别 

- sleep是线程的方法, 而 wait是object的方法,任何类都可以调用.
- sleep不占有锁, 自然也不会释放锁, wait线程占有锁, 调用方法后会释放锁.
- 都可以被 interrupted 方法中断。

###### 线程分类

- 用户线程 , 用户自定义的默认普通线程;
- 主线程, 程序启动时,进程被创建,主线程也随之被创建.
- 守护线程, 运行在后台,是一种特殊线程,需用户设置.比如垃圾回收线程

当主线程结束后,用户线程还在运行,JVM 存活; 如果没有用户线程,都是守护线程,JVM 结束 

```java
public class MyThreadTest {
    public static void main(String[] args) {

        System.out.println(Thread.currentThread().getName()+"开始执行");
       // startChildThread();  //测试没有用户线程时候,注销该代码
        Thread bb = new Thread("bb");
        //设置为守护线程, 必须设置在start开始前
        bb.setDaemon(true);
        bb.start();
        System.out.println(bb.getName()+"开始执行");
        System.out.println(Thread.currentThread().getName()+"结束执行");
    }

    private static void startChildThread() {
        new Thread(()->{
            while (true){  //模拟子线程一直存活
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName());
            }

        },"aa").start();
    }
}

```

###### 创建线程

- 继承Thread类
- 实现Runnable接口
- 实现Callable接口
- 线程池方式





#### 并发与并行

##### 串行模式

所有任务按顺序一个接一个执行

##### 并行模式

同时执行多个任务。并行的效率从代码层次上强依赖于多进程/多线程代码，从硬件角度上
则依赖于多核 CPU。 

##### 并发(concurrent)

多个程序可以同时运行的现象，更细化的是多进程可以同时运行或者多指令可以同时运行。同一时刻多个线程在访问同一个资源，多个线程对一个点 

要解决大并发问题，通常是将大任务分解成多个小任务

#### 管程

是保证了同一时刻只有一个进程在管程内活动,即管程内定义的操作在同一时刻只被一个进程调用(由编译器实现).JVM 中同步是基于进入和退出管程(monitor)对象实现的，每个对象都会有一个管程(monitor)对象，管程(monitor)会随着 java 对象一同创建和销毁 



### 实现并发访问的方式

#### Synchronized

synchronized 是 Java 中的关键字，是一种同步锁。它修饰的对象有以下几种：

- 修饰一个代码块，被修饰的代码块称为同步语句块，其作用的范围是大括号{}
	括起来的代码，作用的对象是调用这个代码块的对象, 锁的是括号里配置的对象； 
- 修饰一个方法，被修饰的方法称为同步方法，其作用的范围是整个方法，作用
	的对象是调用这个方法的对象；  
	- 普通方法, 锁的是当前对象实例;
	- 静态方法, 锁的是当前类Class对象;

，synchronized 关键字不能被继承。如果在父类中的某个方法使用了 synchronized 关键字，而在子类中覆盖了这个方法，在子类中的这个方法默认情况下并不是同步的，而必须显式地在子类的这个方法中加上synchronized 关键字才可以。当然，还可以在子类方法中调用父类中相应的方
法，这样虽然子类中的方法不是同步的，但子类调用了父类的同步方法，因此，子类的方法也就相当于同步了。

Synchronized修饰情况下, 获取锁的线程释放锁只会有两种情况：

1. 获取锁的线程执行完了该代码块，然后线程释放对锁的占有；
2. 线程执行发生异常，此时 JVM 会让线程自动释放锁。

如果这个获取锁的线程由于要等待 IO 或者其他原因被阻塞,那么其它线程需要一直等待.

#### Lock接口

Lock 锁实现提供了比使用同步方法和语句可以获得的更广泛的锁操作。它与synchronized不同点如下:

1. synchronized 是 Java 语言的关键字，因此是内置特性。Lock 是一个接口，通过它的实现类可以实现同步访问；
2. synchronized 不需要用户去手动释放锁 ,而 Lock 则必须要用户去手动释放锁，如果没有主动释放锁，就有可能导致出现死锁现象。 
3.  synchronized 与 wait()/notify()这两个方法一起使用可以实现等待/通知模式, 而lock通过调用newCondition返回的Condition类实现通知. 用 notify()通知时，JVM 会随机唤醒某个等待的线程， 使用 Condition 类可以进行选择性通知.
4. 通过 Lock 可以知道有没有成功获取锁，而 synchronized 却无法办到。
5. Lock 可以提高多个线程进行读操作的效率。 在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而当竞争资源非常激烈时（即有大量线程同时竞争），此时 Lock 的性能要远远优于synchronized。 

相同点:

synchronized 和lock都是可重入锁, 只不过一个是显式的,一个是隐式的.

##### 接口详解

```java
public interface Lock { 
    //用来获取锁。如果锁已被其他线程获取，则进行等待。由于lock不会自动释放锁,当发生异常时候,会产生死锁现象, 因此必须在try...finally里面,最后将锁释放掉.
   void lock(); 
   void lockInterruptibly() throws InterruptedException; 
   boolean tryLock(); 
   boolean tryLock(long time, TimeUnit unit) throws InterruptedException; 
   void unlock(); 
    /*
    * Condition 类实现等待/通知模式。它的2个常用方法:
    * 1. await()会使当前线程等待,同时会释放锁,
    * 2. signal()用于唤醒一个等待的线程
    */
   Condition newCondition(); 
} 
```

##### 常用实现类

- ReentrantLock 可重入锁;
-  ReadWriteLock  接口, 定义2个方法, 一个用于获取读锁,一个用于获取写锁.将文件的读写操作分开，分成 2 个锁来分配给线程，从而使得多个线程可以同时进行读操作。ReentrantReadWriteLock 实现了 ReadWriteLock 接口。读锁与写锁是互斥的,当一个被占用,另外一个只能等待释放.

### 线程通信

线程间通信的模型有两种：共享内存和消息传递

### 集合的线程安全

1. List的线程安全解决
	-  Vector(jdk1.0 , 过时)
	-  Collections. synchronizedList (List list) (不推荐);
	-  CopyOnWriteArrayList(推荐)
2. HashSet的线程不安全
	- CopyOnWriteArraySet
3. HashMap的线程不安全
	- ConcurrentHashMap

 



### Callable接口介绍

Thread 类的构造方法根本没有 Callable 参数,Java 库具有具体的 FutureTask 类型，该类型实现 Runnable 和 Future,并方便地将两种功能组合在一起。 可以通过为其构造函数提供 Callable 来创建FutureTask。然后，将 FutureTask 对象提供给 Thread 的构造函数以创建Thread 对象。因此，间接地使用 Callable 创建线程。 

- 在主线程中需要执行比较耗时的操作时，但又不想阻塞主线程时，可以把这些作业交给 Future 对象在后台完成.

- 当主线程将来需要时，就可以通过 Future 对象获得后台作业的计算结果或者执行状态 
- 一般 FutureTask 多用于耗时的计算，主线程可以在完成自己的任务后，再去获取结果。 
- 仅在计算完成时才能检索结果；如果计算尚未完成，则阻塞 get 方法 
- 一旦计算完成，就不能再重新开始或取消计算 
- get 方法而获取结果只有在计算完成时获取，否则会一直阻塞直到任务转入完成状态，然后会返回结果或者抛出异常 
- get 只计算一次,因此 get 方法放到最后 



### JUC三大辅助类

#### CountDownLatch 类

可以设置一个计数器,然后通过 countDown 方法来进行减 1 的操作，使用 await 方法等待计数器不大于 0，然后继续执行 await 方法之后的语句。 

主要方法介绍

-  await 方法, 调用时线程会阻塞;
- countDown 方法, 计数器减1, 调用者不会阻塞;

当计数器=0时, 因为await方法阻塞的线程会被唤醒,继续执行

```java
// 示例: 6个同学陆续离开教室后,值日生最后锁门
public class CountDownLatDemo {


    public static void main(String[] args) throws InterruptedException {
        //定义数值为6的计数器
        CountDownLatch countDownLatch = new CountDownLatch(6);
        for (int i = 0; i < 6; i++) {
            new Thread(() -> {
                try {
//                        TimeUnit.SECONDS.sleep(5);
                    //模拟执行耗时操作
                    Thread.sleep(5 * 1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println(Thread.currentThread().getName() + "同学离开了");
                //计数器-1
                countDownLatch.countDown();

            }, String.valueOf(i)).start();
        }
        //主线程休息
        System.out.println("主线程开始休息");
        countDownLatch.await();

        System.out.println("都走完了,值日生锁门");
    }

}
```

![image-20210930104647482](https://gitee.com/tadpole145/images/raw/main/20210930104647.png)

#### CyclicBarrier

在使用中CyclicBarrier 的构造方法第一个参数是目标障碍数，每次执行 CyclicBarrier 一
次障碍数会加一，如果达到了目标障碍数，才会执行 cyclicBarrier.await()之后的语句。

```java
//示例, 收集7龙珠,可以召唤神龙.
public class CyclicBarrierDemo {
    private static final int Number = 7;
    public static void main(String[] args) throws BrokenBarrierException, InterruptedException {
        //目标障碍数达到, 执行runnable中的回调.
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7, () -> System.out.println("集齐"+Number+"颗神珠, 现在可以召唤神龙啦"));

        for (int i = 1; i <=7; i++) {
            new Thread(()->{
                try {
                    //模拟耗时操作
                    Thread.sleep(2000);
                    System.out.println(String.format("收集到了%s颗龙珠",Thread.currentThread().getName()));
                    //目标数+1
                    cyclicBarrier.await();
                } catch ( Exception e) {
                    e.printStackTrace();
                }
            },String.valueOf(i)).start();
        }



    }
}
```



#### 信号灯Semaphore

Semaphore 的构造方法中传入的第一个参数是最大信号量（可以看成最大线程池），每个信号量初始化为一个最多只能分发一个许可证。使用 acquire 方法获得许可证，release 方法释放许可 

```java
//模拟3辆车抢1个车位
public class SephamoreDemo {
    public static void main(String[] args) {
        //构造1个信号量
        Semaphore semaphore = new Semaphore(1);
        //模拟3辆车抢1个车位
        for (int i = 0; i < 3; i++) {
            new Thread(()->{
                System.out.println(String.format("第%s个车在找车位",Thread.currentThread().getName()));
                try {
                    //获得许可证
                    semaphore.acquire();
                    System.out.println(String.format("第%s个车停车成功",Thread.currentThread().getName()));
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    //释放许可证
                    semaphore.release();
                    System.out.println(String.format("第%s个车离开了车位",Thread.currentThread().getName()));
                }

            } ,String.valueOf(i)).start();
        }
    }
}

```



### ReadWriteLock接口

读写锁接口,提供读锁和写锁方法, 其实现类主要有ReentrantReadWriteLock.它有两个锁，一个是读操作相关的锁，称为共享锁；一个是写相关的锁，称为排他锁;

线程进入读锁的前提条件:

- 没有其它线程的写锁,

线程进入写锁的前提条件

- 没有其它线程的读锁
- 没有其它线程的写锁

读写锁的三个重要特性

1. 公平选择性, 支持非公平和公平的锁获取方式,吞吐量还是非公平优于公平;
2. 重进入: 读锁和写锁都支持重进入
3. 锁降级: 遵循获取写锁、获取读锁再释放写锁的次序，写锁能够降级成为读锁。

```java
// 读写锁案例
class MyCache {
    //创建 map 集合
    private volatile Map<String, Object> map = new HashMap<>();
    //创建读写锁对象
    private ReadWriteLock rwLock = new ReentrantReadWriteLock();

    //放数据
    public void put(String key, Object value) {
        //添加写锁
        rwLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "正在写操作" + key);
            //暂停一会
            TimeUnit.MICROSECONDS.sleep(300);
            //放数据
            map.put(key,value);
            System.out.println(Thread.currentThread().getName()+"写完了数据"+key);

        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            //释放写锁
            rwLock.writeLock().unlock();
        }
    }


    //取数据
    public Object get(String key) {
        //添加读锁
        rwLock.readLock().lock();
        Object result = null;

        try {
            System.out.println(Thread.currentThread().getName() + "------正在读操作" + key);
            //暂停一会
            TimeUnit.MICROSECONDS.sleep(300);
            //放数据
            result=  map.get(key);
            System.out.println(Thread.currentThread().getName()+"--------取完了数据"+key);

        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            //释放写锁
            rwLock.readLock().unlock();
        }
        return result;
    }

    }

public class ReadWriteLockDemo {
    public static void main(String[] args) {
        MyCache myCache = new MyCache();
        
        for (int i = 0; i < 5; i++) {
            String key = String.valueOf(i);
            new Thread(()->{
                myCache.put(key,key);
            },key).start();
        }


        for (int i = 0; i < 5; i++) {
            String key = String.valueOf(i);
            new Thread(()->{
                myCache.get(key);
            },key).start();
        }
    }
}

```

![image-20210930140410945](https://gitee.com/tadpole145/images/raw/main/20210930140410.png)

总结:

- 读锁共享,写锁排它.  
- 在线程持有读锁的情况下, 其它线程也可以获取读锁,但是任何线程都不能获取写锁
- 当前线程持有写锁的情况下,自己可以获取读锁, 但是其余线程无法获取读锁,因为写锁的排它.

### BlockQuene接口

BlockingQueue是一个接口,它继承了Queue和Collection接口,它具有多个子接口或者实现类.

阻塞队列，顾名思义，首先它是一个队列, 通过一个共享的队列，可以使得数据由队列的一端输入，从另外一端输出

- 当队列是空的，从队列中获取元素的操作将会被阻塞 
- 当队列是满的，从队列中添加元素的操作将会被阻塞 
- 试图从空的队列中获取元素的线程将会被阻塞，直到其他线程往空的队列插入新的元素 
- 试图向已满的队列中添加新元素的线程将会被阻塞，直到其他线程从队列中移除一个或多
	个元素或者完全清空

#### 常见队列

先进先出:  先插入的队列的元素也最先出队列，类似于排队的功能。

后进先出: ：后插入队列的元素最先出队列，这种队列优先处理最近发生的事件(栈) 

#### 阻塞

 在某些情况下会挂起线程（即阻塞），一旦条件满足，被挂起的线程又会自动被唤起 

#### BlockingQueue 核心方法

![image-20210930154511745](https://gitee.com/tadpole145/images/raw/main/20210930154511.png)

![image-20210930154534895](https://gitee.com/tadpole145/images/raw/main/20210930154534.png)

#### 常见的BlockingQueue 实现类

#####  ArrayBlockingQueue(常用)

基于数组的有界阻塞队列实现，在 ArrayBlockingQueue 内部，维护了一个定长数组，以便缓存队列中的数据对象.，ArrayBlockingQueue 内部还保存着两个整形变量，分别标识着队列的头部和尾部在数组中的位置。 

ArrayBlockingQueue 在生产者放入数据和消费者获取数据，都是共用同一个锁对象，由此也意味着两者无法真正并行运行，这点尤其不同于LinkedBlockingQueue；

在创建 ArrayBlockingQueue 时，我们还可以控制对象的内部锁是否采用公平锁，默认采用非公平锁。 

##### LinkedBlockingQueue(常用) 

基于链表的有界(默认为integer.MAX_VALUE)阻塞队列，内部也维持着一个数据缓冲队列（该队列由一个链表构成），当生产者往队列中放入一个数据时，队列会从生产者手中获取数据，并缓存在队列内部，而生产者立即返回；只有当队列缓冲区达到最大值缓存容量时，才会阻塞生产者队列，直到消费者从队列中消费掉一份数据，生产者线程会被唤醒.

 LinkedBlockingQueue 之所以能够高效的处理并发数据，还因为其对于生产者端和消费者端分别采用了独立的锁来控制数据同步，这也意味着在高并发的情况下生产者和消费者可以并行地操作队列中的数据，以此来提高整个队列的并发性能。 

##### DelayQueue 

使用优先级队列实现的延迟无界阻塞队列。只有当其指定的延迟时间到了，才能够从队列中获取到该元素。不会阻塞生产者,只会阻塞消费者.

##### PriorityBlockingQueue 

支持优先级排序的无界阻塞队列。它并不会阻塞数据生产者(无界)，而只会在没有可消费的数据时，阻塞数据的消费者。生产者生产数据的速度绝对不能快于消费者消费数据的速度，否则时间一长，会最终耗尽所有的可用堆内存空间。 内部控制线程同步的锁采用的是公平锁。

##### SynchronousQueue 

不存储元素的阻塞队列，也即单个元素的队列。

### 线程池介绍

##### 基本介绍

线程池（英语：thread pool）：一种线程使用模式。线程过多会带来调度开销，进而影响缓存局部性和整体性能。而线程池维护着多个线程，等待着监督管理者分配可并发执行的任务。这避免了在处理短时间任务时创建与销毁线程的代价。线程池不仅能够保证内核的充分利用，还能防止过分调度。 

主要特点为： 

- 降低资源消耗: 通过重复利用已创建的线程降低线程创建和销毁造成的销耗。 
- 提高响应速度: 当任务到达时，任务可以不需要等待线程创建就能立即执行。 
- 提高线程的可管理性: 线程是稀缺资源，如果无限制的创建，不仅会销耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控。

##### 创建方式

Java 中的线程池是通过 Executor 框架实现的，该框架中用到了 Executor，Executors，ExecutorService，ThreadPoolExecutor 这几个类 

Executors是个快捷创建线程池的工具类, 但是不支持通过该类创建线程池

- newFixedThreadPool, 允许请求队列长度为Integer.MAX_VALUE,容易内存溢出
- newSingleThreadExecutor,允许请求队列长度为Integer.MAX_VALUE,容易内存溢出
- newCachedThreadPool,  允许创建的线程数为Integer.MAX_VALUE,容易堆积大量的请求,导致容易内存溢出
- newScheduleThreadPool,允许创建的线程数为Integer.MAX_VALUE,容易堆积大量的请求,导致容易内存溢出

**阿里巴巴手册要求开发者尽量通过ThreadPoolExecutor来结合自己业务来指定使用的线程池**

```java
 public ThreadPoolExecutor(int corePoolSize,  //核心线程池数
                              int maximumPoolSize,  //最大线程池数
                              long keepAliveTime,   //存活时间
                              TimeUnit unit,    //时间单位
                              BlockingQueue<Runnable> workQueue, //阻塞队列
                              ThreadFactory threadFactory,  //线程工厂
                              RejectedExecutionHandler handler //拒绝策略
                          ) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```

##### 工作原理

- 在创建了线程池后，线程池中的线程数为零 
- 当调用 execute()方法添加一个请求任务时，线程池会做出如下判断：
	- 如果正在运行的线程数量小于 corePoolSize，那么马上创建线程运行这个任务;
	- 如果正在运行的线程数量大于或等于 corePoolSize，那么将这个任务放入队列；
	- 如果这个时候队列满了且正在运行的线程数量还小于maximumPoolSize，那么还是要创建非核心线程立刻运行这个任务；
	-  如果队列满了且正在运行的线程数量大于或等于 maximumPoolSize，那么线程
		池会启动饱和拒绝策略来执行。
- 当一个线程完成任务时，它会从队列中取下一个任务来执行;
- 当一个线程无事可做超过一定的时间（keepAliveTime）时，线程会判断
	- 如果当前运行的线程数大于 corePoolSize，那么这个线程就被停掉。
	- 所以线程池的所有任务完成后，它最终会收缩到 corePoolSize 的大小。

##### 拒绝策略

- Abortpolicy(默认):直接抛出 RejectedExecutionException异常阻止系统正常运行;
- CallerRunsPolicy:"调用者运行“种调节机制,该策略既不会抛弃任务,也不会抛出异常,而是将某些任务回退到调用者,从而降低新任务的流量。
- DiscardOldestPolicy: 抛弃队列中等待最久的任务,然后把当前任务加人队列中尝试再次提交当前任务
- DiscardPolicy 该策略默默地丢弃无法理的任务,不予任何处理也不抛出异常如果允许任务丢失,这是最好的一种策略

### CompletableFuture

CompletableFuture 在 Java 里面被用于异步编程，异步通常意味着非阻塞，可以使得我们的任务单独运行在与主线程分离的其他线程中，并且通过回调可以在主线程中得到异步任务的执行状态，是否完成，和是否异常等信息。

CompletableFuture 实现了 Future, CompletionStage 接口，实现了 Future接口就可以兼容现在有线程池框架，而 CompletionStage 接口才是异步编程的接口抽象，里面定义多种异步方法，通过这两者集合，从而打造出了强大的CompletableFuture 类。 

####  Future 与 CompletableFuture 

Futrue 在 Java 里面，通常用来表示一个异步任务的引用，在 Future 里面有 isDone 方法来 判断任务是否处理结束，还有 get 方法可以一直阻塞直到任务结束然后获取结果，但整体来说这种方式，还是同步的，因为需要客户端不断阻塞等待或者不断轮询才能知道任务是否完成。 

Future 的主要缺点如下：

1. 不支持手动完成 , 只能主动取消或者一直等待它执行完成 ;
2. 不支持进一步的非阻塞调用 , Future 不支持回调函数, 调用get后也就无法进一步执行额外的任务.
3. 不支持链式调用 
4. 不支持多个 Future 合并
5. 不支持异常处理 

####  CompletableFuture 的使用

**场景一**, 主线程里面创建一个 CompletableFuture，然后主线程调用 get 方法会阻塞，最后我们在一个子线程中使其终止。 

```java
/** 
* 主线程里面创建一个 CompletableFuture，然后主线程调用 get 方法会阻塞，最后我们
在一个子线程中使其终止 
* @param args 
*/ 
public static void main(String[] args) throws Exception{ 
   CompletableFuture<String> future = new CompletableFuture<>(); 
   new Thread(() -> { 
       try{ 
           System.out.println(Thread.currentThread().getName() + "子线程开始干活"); 
           //子线程睡 5 秒 
           Thread.sleep(5000); 
           //在子线程中完成主线程 
           future.complete("success"); 
      }catch (Exception e){ 
           e.printStackTrace(); 
      } 
  }, "A").start(); 
   //主线程调用 get 方法阻塞 
   System.out.println("主线程调用 get 方法获取结果为: " + future.get()); 
   System.out.println("主线程完成,阻塞结束!!!!!!"); 
} 
```



场景二: 没有返回值的异步任务 

```java
/** 
 * 没有返回值的异步任务 
 * @param args 
 */ 
public static void main(String[] args) throws Exception{ 
    System.out.println("主线程开始"); 
    //运行一个没有返回值的异步任务 
    CompletableFuture<Void> future = CompletableFuture.runAsync(() -
> { 
        try { 
            System.out.println("子线程启动干活"); 
            Thread.sleep(5000); 
            System.out.println("子线程完成"); 
        } catch (Exception e) { 
            e.printStackTrace(); 
        } 
    }); 
    //主线程阻塞 
    future.get(); 
    System.out.println("主线程结束"); 
} 
```

场景三: 有返回值的异步任务

```java
public static void main(String[] args) throws Exception{ 
    System.out.println("主线程开始"); 
    //运行一个有返回值的异步任务 
    CompletableFuture<String> future = 
CompletableFuture.supplyAsync(() -> { 
        try { 
            System.out.println("子线程开始任务"); 
            Thread.sleep(5000); 
        } catch (Exception e) { 
            e.printStackTrace(); 
        } 
        return "子线程完成了!"; 
    }); 
    //主线程阻塞 
    String s = future.get(); 
    System.out.println("主线程结束, 子线程的结果为:" + s); 
} 
```

场景四:   线程依赖 

```java
//当一个线程依赖另一个线程时，可以使用 thenApply 方法来把这两个线程串行化。
private static Integer num = 10; 
 
/** 
 * 先对一个数加 10,然后取平方 
 * @param args 
 */ 
public static void main(String[] args) throws Exception{ 
    System.out.println("主线程开始"); 
    CompletableFuture<Integer> future = 
CompletableFuture.supplyAsync(() -> { 
        try { 
            System.out.println("加 10 任务开始"); 
            num += 10; 
        } catch (Exception e) { 
            e.printStackTrace(); 
        } 
        return num; 
    }).thenApply(integer -> { 
        return num * num; 
    }); 
    Integer integer = future.get(); 
    System.out.println("主线程结束, 子线程的结果为:" + integer); 


```

场景五: 消费处理结果 

```java
public static void main(String[] args) throws Exception{ 
    System.out.println("主线程开始"); 
   CompletableFuture.supplyAsync(() -> { 
        try { 
            System.out.println("加 10 任务开始"); 
            num += 10; 
        } catch (Exception e) { 
            e.printStackTrace(); 
        } 
        return num; 
    }).thenApply(integer -> { 
        return num * num; 
    }).thenAccept(new Consumer<Integer>() { 
        @Override 
        public void accept(Integer integer) { 
            System.out.println("子线程全部处理完成,最后调用了 accept,结果为:" + 
integer); 
        } 
    }); 
} 
```

场景六: 异常处理

```java
public static void main(String[] args) throws Exception{ 
    System.out.println("主线程开始"); 
    CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> { 
        int i= 1/0; 
        System.out.println("加 10 任务开始"); 
        num += 10; 
        return num; 
    }).exceptionally(ex -> { 
        System.out.println(ex.getMessage()); 
        return -1; 
    }); 
    System.out.println(future.get()); 
} 
```

场景七:handle 类似于 thenAccept/thenRun 方法,是最后一步的处理调用,但是同时可以处理异常 

```java
public static void main(String[] args) throws Exception{ 
   System.out.println("主线程开始"); 
   CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> { 
       System.out.println("加 10 任务开始"); 
       num += 10; 
       return num; 
  }).handle((i,ex) ->{ 
       System.out.println("进入 handle 方法"); 
       if(ex != null){ 
           System.out.println("发生了异常,内容为:" + ex.getMessage()); 
           return -1; 
      }else{ 
           System.out.println("正常完成,内容为: " + i); 
           return i; 
      } 
    }); 
   System.out.println(future.get()); 
}
```

场景八:  结果合并

```java
// 合并2个有依赖关系的CompletableFutures 的执行结果 
public static void main(String[] args) throws Exception{ 
   System.out.println("主线程开始"); 
   //第一步加 10 
   CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> { 
       System.out.println("加 10 任务开始"); 
       num += 10; 
       return num; 
  }); 
   //合并 
   CompletableFuture<Integer> future1 = future.thenCompose(i -> 
           //再来一个 CompletableFuture 
           CompletableFuture.supplyAsync(() -> { 
               return i + 1; 
          })); 
   System.out.println(future.get()); 
   System.out.println(future1.get()); 
} 
//thenCombine 合并两个没有依赖关系的 CompletableFutures 任务 
public static void main(String[] args) throws Exception{ 
   System.out.println("主线程开始"); 
 
   CompletableFuture<Integer> job1 = CompletableFuture.supplyAsync(() -> { 
       System.out.println("加 10 任务开始"); 
       num += 10; 
       return num; 
  }); 
 
   CompletableFuture<Integer> job2 = CompletableFuture.supplyAsync(() -> { 
          System.out.println("乘以 10 任务开始"); 
       num = num * 10; 
       return num; 
  }); 
   //合并两个结果 
   CompletableFuture<Object> future = job1.thenCombine(job2, new 
BiFunction<Integer, Integer, List<Integer>>() { 
       @Override 
       public List<Integer> apply(Integer a, Integer b) { 
           List<Integer> list = new ArrayList<>(); 
           list.add(a); 
           list.add(b); 
           return list; 
      } 
  }); 
   System.out.println("合并结果为:" + future.get()); 
 
} 
```

场景九:  合并多个任务的结果 allOf 与 anyOf 

```java
//allOf: 一系列独立的 future 任务，等其所有的任务执行完后做一些事情 
/** 
* 先对一个数加 10,然后取平方 
* @param args 
*/ 
public static void main(String[] args) throws Exception{ 
   System.out.println("主线程开始"); 
   List<CompletableFuture> list = new ArrayList<>(); 
   CompletableFuture<Integer> job1 = CompletableFuture.supplyAsync(() -> { 
       System.out.println("加 10 任务开始"); 
       num += 10; 
       return num; 
  }); 
   list.add(job1); 
 
   CompletableFuture<Integer> job2 = CompletableFuture.supplyAsync(() -> { 
       System.out.println("乘以 10 任务开始"); 
      num = num * 10; 
       return num; 
  }); 
   list.add(job2); 
 
   CompletableFuture<Integer> job3 = CompletableFuture.supplyAsync(() -> { 
       System.out.println("减以 10 任务开始"); 
       num = num * 10; 
       return num; 
  }); 
   list.add(job3); 
 
   CompletableFuture<Integer> job4 = CompletableFuture.supplyAsync(() -> { 
       System.out.println("除以 10 任务开始"); 
       num = num * 10; 
       return num; 
  }); 
   list.add(job4); 
   //多任务合并 
   List<Integer> collect = 
list.stream().map(CompletableFuture<Integer>::join).collect(Collectors.toList()); 
   System.out.println(collect); 
}    
       
       
       
//anyOf: 只要在多个 future 里面有一个返回，整个任务就可以结束，而不需要等到每一个future 结束 

/** 
* 先对一个数加 10,然后取平方 
* @param args 
*/  
public static void main(String[] args) throws Exception{ 
   System.out.println("主线程开始"); 
   CompletableFuture<Integer>[] futures = new CompletableFuture[4]; 
   CompletableFuture<Integer> job1 = CompletableFuture.supplyAsync(() -> { 
       try{ 
           Thread.sleep(5000); 
           System.out.println("加 10 任务开始");  
 
           num += 10; 
           return num; 
      }catch (Exception e){ 
           return 0; 
      } 
  }); 
   futures[0] = job1; 
 
   CompletableFuture<Integer> job2 = CompletableFuture.supplyAsync(() -> { 
       try{ 
           Thread.sleep(2000); 
           System.out.println("乘以 10 任务开始"); 
           num = num * 10; 
           return num; 
      }catch (Exception e){ 
           return 1; 
      } 
 
  }); 
   futures[1] = job2; 
 
   CompletableFuture<Integer> job3 = CompletableFuture.supplyAsync(() -> { 
       try{ 
           Thread.sleep(3000); 
           System.out.println("减以 10 任务开始"); 
           num = num * 10; 
           return num; 
      }catch (Exception e){ 
           return 2; 
      } 
 
  }); 
   futures[2] = job3; 
 
   CompletableFuture<Integer> job4 = CompletableFuture.supplyAsync(() -> { 
       try{ 
           Thread.sleep(4000); 
           System.out.println("除以 10 任务开始");            num = num * 10; 
           return num; 
      }catch (Exception e){ 
           return 3; 
      } 
  }); 
   futures[3] = job4; 
   CompletableFuture<Object> future = CompletableFuture.anyOf(futures); 
   System.out.println(future.get()); 
} 
```

