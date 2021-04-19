java 

[java虚拟机](https://www.infoworld.com/article/3272244/what-is-the-jvm-introducing-the-java-virtual-machine.html)





### 多线程

##### 开启线程的3种方式

- 通过继承Thread方式

```
自定义类,继承Thread
重写run方法
调用start方法
```

- 通过实现Runnable接口

```
自定义类实现Runnable方法,并实现run方法
创建线程对象,并将第一步中的对象通过构造方法传入
调用线程的start()方法
```

静态代理

```
代理类与被代理类都实现了同一个接口;
被代理类以构造函数参数形式传入代理类
```

所以,开启新线程就是使用了静态代理模式.

- 通过实现callable接口

```
自定义类实现callable接口, 需要返回值类型
重写call方法,并抛出异常
创建callable对象 t1
创建执行服务 ExecutorService ser=Executors.newFixedThreadPool(3)
提交执行: Future<Object> r1= ser.submit(t1);
获取结果:  Object o1=r1.get();
关闭服务:  ser.shutdownNow();
```

##### 线程状态

![image-20210322095505600](https://raw.githubusercontent.com/tadpole145/images/main/image-20210322095505600.png)

![image-20210322095658338](https://raw.githubusercontent.com/tadpole145/images/main/image-20210322095658338.png)



##### 线程常用方法

![image-20210322095841556](https://raw.githubusercontent.com/tadpole145/images/main/image-20210322095841556.png)



###### 线程优先级

优先级范围1-10, 最低1,最高10,正常是5.  通过setPriority(),getPriority()来查看与修改.

##### 线程分类

- 用户线程, 虚拟机必须确保其执行完毕;
- 守护线程(Daemon线程),用于后台记录日志,监控内存,垃圾回收(gc线程)等, 虚拟机不必等待其执行完毕;

##### 并发

同一个对象被多个线程同时操作

##### 线程同步

为解决并发问题,让使用同一对象的多个线程形成一个队列,挨个访问对象.从而保证线程安全.

##### 同步方法

synchronized方法:  给方法加锁,只有锁被释放了,后面的线程拿到锁才能执行该方法.

synchronized块: 锁住多线程共同操作的变量,才能使线程安全.

##### 死锁

多个线程各自占有一些共享资源,并且互相等待其它线程占有的资源才能运行,从而导致2个或者多个线程都在等待对方释放资源,都停止执行的情况.一个代码块同时拥有"2个以上对象的锁"时,可能会发生死锁.

产生死锁的4个必要条件

1. 互斥条件: 一个资源每次只能被一个进程使用.
2. 请求与保持条件: 一个进程因请求资源而阻塞时,对已获得的资源保持不放;
3. 不剥夺条件: 进程已获得的资源,在未使用完之前,不能强行剥夺.
4. 循环等待条件: 若干进程之间形成一种头尾相接的循环资源等待关系.

##### 显示锁

jdk 1.5显示定义了同步锁,使用lock对象充当.锁提供了对共享资源的独占访问,每次只能有一个线程对锁对象加锁. ReentrantLock实现了Lock接口,它拥有synchronized相同的并发性和内存语义,在实现线程安全的控制中,常用该显示锁.

```
private final ReentrantLock lock= new ReentrantLock();
//最好在try...catch...finally里面执行..确保锁最终可以开关锁.
lock.lock;
lock.unLock;
```

##### 线程池

ExecutorService:  线程池接口, 常见子类有ThreadPoolExecutor;

- void execute(Runnable cmd) : 执行命令,无返回值,执行Runnable;
- <T> Future<T> submit(Callable<T> task) : 执行任务,有返回值,执行Callable
- void shutdown(); 关闭连接池;

Executors : 工具类,线程池的工厂类,用于创建并返回不同类型的线程池.