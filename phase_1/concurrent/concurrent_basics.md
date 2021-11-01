# **基础知识**

[TOC]



## **一、概念**

### 1、进程和线程

​		**进程**：计算机系统资源的基本单位，每一个进程都有一个自己的地址空间。

​		**线程**：是进程的一部分，是 CPU 调度的一个基本单位

### 2、并发和并行

​		**并发**：是指一个处理器同时处理多个任务

​		**并行**：是指多个处理器或者是多核的处理器同时处理多个不同的任务

### 3、并发编程的优缺点

​		**优点**：充分利用多核CPU的计算能力、方便进行业务拆分，提升应用性能

​		**缺点**：频繁的上下文切换、线程安全

### 4、保证共享资源线程安全有哪些方法

​		**同步代码块**：synchronized(锁对象)，保证只让1个线程在同步代码块中执行

​		**同步方法**：synchronized void method()，保证只让1个线程在同步方法中执行

​		**Lock锁**：同步代码块/同步方法具有的功能Lock锁都有,除此之外更强大，更体现面向对象



 

## 二、**线程**

### 1、新建线程的方法

1. #### **继承Thread类**

   ```java
   public class 创建线程继承Thread类 {
       public static void main(String[] args) {
           //设置线程名字
           Thread.currentThread().setName("main thread");
           MyThread myThread = new MyThread();
           myThread.setName("子线程:");
           //开启线程
           myThread.start();
           for(int i = 0;i<5;i++){
               System.out.println(Thread.currentThread().getName() + i);
           }
       }
   }
   
   class MyThread extends Thread{
       //重写run()方法
       public void run(){
           for(int i = 0;i < 10; i++){
               System.out.println(Thread.currentThread().getName() + i);
           }
       }
   }
   ```

   

2. #### **实现Runnable接口**

   ```java
   public class 创建线程实现Runnable接口 {
       public static void main(String[] args) {
           //设置线程名字
           Thread.currentThread().setName("main thread:");
           Thread thread = new Thread(new MyRunnable());
           thread.setName("子线程:");
           //开启线程
           thread.start();
           for(int i = 0; i <5;i++){
               System.out.println(Thread.currentThread().getName() + i);
           }
       }
   }
   
   class MyRunnable implements Runnable {
   
       @Override
       public void run() {
           for (int i = 0; i < 10; i++) {
               System.out.println(Thread.currentThread().getName() + i);
           }
       }
   }
   ```

   

3. #### **实现Callable接口**

   ```java
   public class 创建线程实现Callable接口 {
       public static void main(String[] args) {
           //设置线程名字
           Thread.currentThread().setName("main thread:");
           //执行Callable 方式，需要FutureTask 实现实现，用于接收运算结果
           FutureTask<Integer> futureTask = new FutureTask<Integer>(new MyCallable());
           Thread thread = new Thread(futureTask);
           thread.setName("子线程:");
           //开启线程
           thread.start();
           //接收线程运算后的结果
           try {
               Integer sum = futureTask.get();
               System.out.println(sum);
           } catch (InterruptedException e) {
               e.printStackTrace();
           } catch (ExecutionException e) {
               e.printStackTrace();
           }
           for(int i = 0; i <5;i++){
               System.out.println(Thread.currentThread().getName() + i);
           }
       }
   }
   
   class MyCallable implements Callable<Integer> {
   
       @Override
       public Integer call() throws Exception {
           int sum = 0;
           for (int i = 0; i < 5; i++) {
               System.out.println(Thread.currentThread().getName() + i);
               sum += i;
           }
           return sum;
       }
   }
   ```

   

4. #### **线程池**

   ```java
   public class 创建线程线程池 {
       public static void main(String[] args) {
           //创建线程池
           ExecutorService executorService = Executors.newFixedThreadPool(10);
           ThreadPool threadPool = new ThreadPool();
           for(int i =0;i<5;i++){
               //为线程池分配任务
               executorService.submit(threadPool);
           }
           //关闭线程池
           executorService.shutdown();
       }
   }
   
   class ThreadPool implements Runnable {
   
       @Override
       public void run() {
           for(int i = 0 ;i<10;i++){
               System.out.println(Thread.currentThread().getName() + ":" + i);
           }
       }
   
   }
   
   ```

   

### 2、线程操作

#### **线程暂时休眠（sleep）**

public static void sleep(long millis, int nanos) ；

millis：以毫秒为单位的睡眠时间长度

nanos： 0-9999999睡眠时间延长纳秒

```java
public class 线程操作 {
    public static void main(String[] args) {
        try {
            //线程休眠1000毫秒
            System.out.println("主线程开始休眠！");
            long start = System.currentTimeMillis();
            Thread.sleep(1000L, 900000);
            long end = System.currentTimeMillis();
            System.out.println("主休眠耗时："+(end -start)+"毫秒！");
            System.out.println("主线程结束休眠！");
        }catch (Exception e){
            e.printStackTrace();
        }
    }
}

/**运行结果**/
主线程开始休眠！
主休眠耗时：1001毫秒！
主线程结束休眠！
```



#### **线程强制运行（join）**

**线程强制运行期间，其他线程无法运行，必须等待此线程完成之后，才可以继续运行**

```java
public class 线程操作 {
    public static void main(String[] args) {
        try {
            Thread thread = Thread.currentThread();
            thread.setName("主线程");
            Thread subThread = new Thread(new MyRunnableTest());
            subThread.setName("子线程");
            //开启线程
            subThread.start();
            for(int i=0;i<10;i++){
                Thread.sleep(1000L);
                System.out.println(Thread.currentThread().getName() + i);
                if(i>5){
                    subThread.join();
                }
            }
        }catch (Exception e){
            e.printStackTrace();
        }
    }
}

class MyRunnableTest implements Runnable {

    @Override
    public void run() {
        try {
            for(int i=0;i<10;i++){
                Thread.sleep(1000L);
                System.out.println(Thread.currentThread().getName() + i);
            }
        }catch (Exception e){
            e.printStackTrace();
        }
    }
}

/**运行结果**/
主线程0
子线程0
子线程1
主线程1
子线程2
主线程2
子线程3
主线程3
子线程4
子线程5
子线程6
子线程7
子线程8
子线程9
主线程4
主线程5
主线程6
主线程7
主线程8
主线程9
```



#### **线程礼让（yield）**

能够让当前线程从“运行状态”进入到“就绪状态”，从而让其他等待线程获取执行权，但是不能保证在当前线程调用yield()之后，其他线程就一定能获得执行权，也有可能是当前线程又回到“运行状态”继续运行

```java
public class 线程操作 {
    public static void main(String[] args) {
        MyRunnableTest my = new MyRunnableTest() ;    // 实例化MyThread对象
        Thread t1 = new Thread(my,"线程A") ;
        Thread t2 = new Thread(my,"线程B") ;
        t1.start() ;
        t2.start() ;
    }
}

class MyRunnableTest implements Runnable {

    @Override
    public void run() {
        for(int i=0;i<5;i++){
            try{
                Thread.sleep(1000) ; //休眠一下
            }catch(Exception e){
                e.printStackTrace();
            }
            if(i==2){
                System.out.print(Thread.currentThread().getName() +"线程礼让：") ;
                Thread.currentThread().yield() ;    // 首先获取当前线程，然后线程礼让
            }
            System.out.println(Thread.currentThread().getName()
                    + "运行，i = " + i) ;    // 取得当前线程的名字
        }
    }
}


/**运行结果**/
线程B运行，i = 0
线程A运行，i = 0
线程B运行，i = 1
线程A运行，i = 1
线程A线程礼让：线程A运行，i = 2
线程B线程礼让：线程B运行，i = 2
线程A运行，i = 3
线程B运行，i = 3
线程A运行，i = 4
线程B运行，i = 4
```



#### **线程中断（interrupt）**

只是改变中断状态，不会中断一个正在运行的线程。需要用户自己去监视线程的状态为并做处理。

更确切的说，如果线程被Object.wait, Thread.join和Thread.sleep三种方法之一阻塞，此时调用该线程的interrupt()方法，那么该线程将抛出一个 InterruptedException中断异常（该线程必须事先预备好处理此异常），从而提早地终结被阻塞状态。如果线程没有被阻塞，这时调用 interrupt()将不起作用，直到执行到wait(),sleep(),join()时,才马上会抛出 InterruptedException

```java
public class 线程操作 {
    public static void main(String[] args) {
        MyRunnableTest mt = new MyRunnableTest() ;    // 实例化Runnable子类对象
        Thread t = new Thread(mt,"子线程");        // 实例化Thread对象
        t.start() ;    // 启动线程
        try{
            Thread.sleep(2000) ;    // 线程休眠2秒
        }catch(InterruptedException e){
            System.out.println("3、休眠被终止") ;
        }
        t.interrupt() ;    // 中断线程执行
    }
}

class MyRunnableTest implements Runnable {

    @Override
    public void run(){    // 覆写run()方法
        System.out.println("1、进入run()方法") ;
        try{
            Thread.sleep(10000) ;    // 线程休眠10秒
            System.out.println("2、已经完成了休眠") ;
        }catch(InterruptedException e){
            System.out.println("3、休眠被终止") ;
        }
        System.out.println("4、run()方法正常结束") ;
    }
}


/**运行结果**/
1、进入run()方法
3、休眠被终止
4、run()方法正常结束
```



#### **线程优先级（getPriority&setPriority）**



优先级分为最低，最高，普通三个（Thread.MIN_PRIORITY，Thread.MAX_PRIORITY，Thread.NORM_PRIORITY）

优先级高的线程拿到cpu执行权的概率比较大而已，并不是一定能拿到

```java
public class 线程操作 {
    public static void main(String[] args) {
       for(int i=0;i<5;i++){
            Thread t1 = new Thread(new MyRunnableTest(),"线程A") ;    // 实例化线程对象
            Thread t2 = new Thread(new MyRunnableTest(),"线程B") ;    // 实例化线程对象
            Thread t3 = new Thread(new MyRunnableTest(),"线程C") ;    // 实例化线程对象
            t1.setPriority(Thread.MIN_PRIORITY) ;    // 优先级低
            t2.setPriority(Thread.NORM_PRIORITY) ;    // 优先级中
            t3.setPriority(Thread.MAX_PRIORITY) ;    // 优先级高
            t1.start() ;    // 启动线程
            t2.start() ;    // 启动线程
            t3.start() ;    // 启动线程
            try{
                Thread.sleep(500) ;    // 线程休眠2秒
            }catch(InterruptedException e){
                e.printStackTrace();
            }
            System.out.println("************");
        }
    }
}

class MyRunnableTest implements Runnable {

    @Override
    public void run(){    // 覆写run()方法
        System.out.println(Thread.currentThread().getName()
                + "运行") ;    // 取得当前线程的名字
    }
}


/**运行结果**/
线程A运行
线程C运行
线程B运行
************
线程B运行
线程C运行
线程A运行
************
线程B运行
线程C运行
线程A运行
************
线程A运行
线程B运行
线程C运行
************
线程A运行
线程B运行
线程C运行
************
```

 

### 3、线程的状态及转换

![线程状态转换图](.\图片\线程状态转换图.jpg)

**dump 文件里，值得关注的线程状态有：**
	死锁，**Deadlock（重点关注）**

​	执行中，Runnable 

​	等待资源，**Waiting on condition（重点关注）**

​	等待获取监视器，**Waiting on monitor entry（重点关注）**

​	暂停，Suspended

​	对象等待中，Object.wait() 或 TIMED_WAITING

​	阻塞，**Blocked（重点关注）** 

​	停止，Parked

1. **Waiting to lock 和 Blocked**

   ```
   "RMI TCP Connection(267865)-172.16.5.25" daemon prio=10 tid=0x00007fd508371000 nid=0x55ae waiting for monitor entry [0x00007fd4f8684000]
      java.lang.Thread.State: BLOCKED (on object monitor)
   at org.apache.log4j.Category.callAppenders(Category.java:201)
   - waiting to lock <0x00000000acf4d0c0> (a org.apache.log4j.Logger)
   at org.apache.log4j.Category.forcedLog(Category.java:388)
   at org.apache.log4j.Category.log(Category.java:853)
   at org.apache.commons.logging.impl.Log4JLogger.warn(Log4JLogger.java:234)
   at com.tuan.core.common.lang.cache.remote.SpyMemcachedClient.get(SpyMemcachedClient.java:110)
   ```

   说明：
   1）线程状态是 Blocked，阻塞状态。说明线程等待资源超时！
   2）“ waiting to lock <0x00000000acf4d0c0>”指，线程在等待给这个 0x00000000acf4d0c0 地址上锁（英文可描述为：trying to obtain 0x00000000acf4d0c0 lock）。
   3）在 dump 日志里查找字符串 0x00000000acf4d0c0，发现有大量线程都在等待给这个地址上锁。如果能在日志里找到谁获得了这个锁（如locked < 0x00000000acf4d0c0 >），就可以顺藤摸瓜了。
   4）“waiting for monitor entry”说明此线程通过 synchronized(obj) {……} 申请进入了临界区，从而进入了下图1中的“Entry Set”队列，但该 obj 对应的 monitor 被其他线程拥有，所以本线程在 Entry Set 队列中等待。
   5）第一行里，"RMI TCP Connection(267865)-172.16.5.25"是 Thread Name 。tid指Java Thread id。nid指native线程的id。prio是线程优先级。[0x00007fd4f8684000]是线程栈起始地址。

   

2. **Waiting on condition 和 TIMED_WAITING**

   ```
   "RMI TCP Connection(idle)" daemon prio=10 tid=0x00007fd50834e800 nid=0x56b2 waiting on condition [0x00007fd4f1a59000]
      java.lang.Thread.State: TIMED_WAITING (parking)
   at sun.misc.Unsafe.park(Native Method)
   - parking to wait for  <0x00000000acd84de8> (a java.util.concurrent.SynchronousQueue$TransferStack)
   at java.util.concurrent.locks.LockSupport.parkNanos(LockSupport.java:198)
   at java.util.concurrent.SynchronousQueue$TransferStack.awaitFulfill(SynchronousQueue.java:424)
   at java.util.concurrent.SynchronousQueue$TransferStack.transfer(SynchronousQueue.java:323)
   at java.util.concurrent.SynchronousQueue.poll(SynchronousQueue.java:874)
   at java.util.concurrent.ThreadPoolExecutor.getTask(ThreadPoolExecutor.java:945)
   at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:907)
   at java.lang.Thread.run(Thread.java:662)
   ```

   说明：

   1）“TIMED_WAITING (parking)”中的 timed_waiting 指等待状态，但这里指定了时间，到达指定的时间后自动退出等待状态；parking指线程处于挂起中。

   2）“waiting on condition”需要与堆栈中的“parking to wait for <0x00000000acd84de8> (a java.util.concurrent.SynchronousQueue$TransferStack)”结合来看。首先，本线程肯定是在等待某个条件的发生，来把自己唤醒。其次，SynchronousQueue 并不是一个队列，只是线程之间移交信息的机制，当我们把一个元素放入到 SynchronousQueue 中时必须有另一个线程正在等待接受移交的任务，因此这就是本线程在等待的条件。
   3）别的就看不出来了。

   

3. **in Obejct.wait() 和 TIMED_WAITING**

   ```
   "RMI RenewClean-[172.16.5.19:28475]" daemon prio=10 tid=0x0000000041428800 nid=0xb09 in Object.wait() [0x00007f34f4bd0000]
      java.lang.Thread.State: TIMED_WAITING (on object monitor)
   at java.lang.Object.wait(Native Method)
   - waiting on <0x00000000aa672478> (a java.lang.ref.ReferenceQueue$Lock)
   at java.lang.ref.ReferenceQueue.remove(ReferenceQueue.java:118)
   - locked <0x00000000aa672478> (a java.lang.ref.ReferenceQueue$Lock)
   at sun.rmi.transport.DGCClient$EndpointEntry$RenewCleanThread.run(DGCClient.java:516)
   at java.lang.Thread.run(Thread.java:662)
   ```

   说明：

   1）“TIMED_WAITING (on object monitor)”，对于本例而言，是因为本线程调用了 java.lang.Object.wait(long timeout) 而进入等待状态。

   2）“Wait Set”中等待的线程状态就是“ in Object.wait() ”。当线程获得了 Monitor，进入了临界区之后，如果发现线程继续运行的条件没有满足，它则调用对象（一般就是被 synchronized 的对象）的 wait() 方法，放弃了 Monitor，进入 “Wait Set”队列。只有当别的线程在该对象上调用了 notify() 或者 notifyAll() ，“ Wait Set”队列中线程才得到机会去竞争，但是只有一个线程获得对象的 Monitor，恢复到运行态。

   3）RMI RenewClean 是 DGCClient 的一部分。DGC 指的是 Distributed GC，即分布式垃圾回收。

   4）请注意，是先 locked <0x00000000aa672478>，后 waiting on <0x00000000aa672478>，之所以先锁再等同一个对象，请看下面它的代码实现：

   ```java
   static private class Lock { };
   private Lock lock = new Lock();
   public Reference<? extends T> remove(long timeout)
   {
     synchronized (lock) {
       Reference<? extends T> r = reallyPoll();
       if (r != null) return r;
       for (;;) {
         lock.wait(timeout);
         r = reallyPoll();
         ……
       }
   }
   ```

   即，线程的执行中，先用 synchronized 获得了这个对象的 Monitor（对应于 locked <0x00000000aa672478> ）；当执行到 lock.wait(timeout);，线程就放弃了 Monitor 的所有权，进入“Wait Set”队列（对应于 waiting on <0x00000000aa672478> ）。
   5）从堆栈信息看，是正在清理 remote references to remote objects ，引用的租约到了，分布式垃圾回收在逐一清理呢。

   

### 4、守护线程

特点：主线程结束了，守护线程也就结束了

java中最大的守护线程是GC，当所有线程结束了，GC也就结束了

```java
public class 守护线程 {
    public static void main(String[] args) {
        Thread threadA = new Thread(new RunnableA(), "线程A");
        Thread threadB = new Thread(new RunnableB(), "线程B");
        threadB.setDaemon(true);
        threadA.start();
        threadB.start();
    }
}

class RunnableA implements Runnable {
    @Override
    public void run() {
        for(int i = 0 ; i < 5 ;i++){
            try {
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + i);
        }

    }
}

class RunnableB implements Runnable {

    @Override
    public void run() {

        for(int i = 0 ; i < 10;i++){
            try {
                Thread.sleep(150);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + i);
        }
    }
}

/**运行结果**/
线程A0
线程B0
线程A1
线程B1
线程A2
线程A3
线程B2
线程A4

Process finished with exit code 0
```

 

## **三、内存模型及关键字**

### 1、java内存模型

画图解释

 

### 2、并发三大特性

#### 原子性

​		含义：要么所有的指令都执行，要么都不执行。这样才能保证并发操作的安全性和一致性。

​		问题示例：

```java
public class 原子性示例 {
    public static void main(String[] args) throws InterruptedException {
        int type = 0;//类型
        int num = 50000;//自增次数
        int sleepTime = 5000;//等待计算时间
        int begin;//开始的值
        Increment increment;
        //不进行原子性保护的大范围操作
        increment = new Increment();
        begin = increment.getCount();
        System.out.println("Java中普通的自增操作不是原子性操作。");
        System.out.println("当前运行类：" +increment.getClass().getSimpleName() +  "，count的初始值是：" + increment.getCount());
        for (int i = 0; i < num; i++) {
            new Thread(() -> {
                increment.increment();
            }).start();
        }
        //等待足够长的时间，以便所有的线程都能够运行完
        Thread.sleep(sleepTime);
        System.out.println("进过" + num + "次自增，count应该 = " + (begin + num) + ",实际count = " + increment.getCount());
    }
}


class Increment {
    private int count = 1;

    public void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}
/** 运行结果 **/
Java中普通的自增操作不是原子性操作。
当前运行类：Increment，count的初始值是：1
进过50000次自增，count应该 = 50001,实际count = 49998

Process finished with exit code 0
```



#### 可见性

​		含义：一个线程对共享变量的修改，另外一个线程能够立刻看到，我们称为可见性。

​		问题示例：

```java
public class 可见性示例 {
    private static boolean stop;

    public static void main(String[] args) throws InterruptedException {
        //普通情况下，多线程不能保证可见性
        new Thread(() -> {
            System.out.println("线程A开始运行...");
            while (!stop) ;
            System.out.println("线程A运行结束.");
        }).start();
        Thread.sleep(10);
        new Thread(() -> {
            System.out.println("线程B开始运行...");
            stop = true;
            System.out.println("线程B运行结束.");
        }).start();
    }
}

/** 运行结果 **/
线程A开始运行...
线程B开始运行...
线程B运行结束.
```



#### 有序性

​		含义：程序按照代码的先后顺序执行。

​		问题示例：

```java
public class 有序性示例 {
    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            System.out.println("线程A："+Singleton.getInstance().num);
        }).start();
        new Thread(() -> {
            System.out.println("线程B："+Singleton.getInstance().num);
        }).start();
    }
}

class Singleton {
    private static Singleton instance;

    public static int num = 0;

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
            num ++;
        }else{
            num --;
        }
        return instance;
    }

}

/** 运行结果 **/
线程A：1
线程B：2

Process finished with exit code 0
```

 

### 3、MESI缓存一致性协议、内存屏障、Happens before原则

#### MESI缓存一致性协议

​	MESI分别代表缓存行数据所处的四种状态，通过对这四种状态的切换，来达到对缓存数据进行管理的目的。

​	

|           状态            |                             描述                             | 监听任务                                                     |
| :-----------------------: | :----------------------------------------------------------: | :----------------------------------------------------------- |
|     M 修改（Modify）      | 该缓存行有效，数据被修改了，和内存中的数据不一致，数据只存在于本缓存行中 | 缓存行必须时刻监听所有试图读该缓存行相对应的内存的操作，其他缓存须在本缓存行写回内存并将状态置为E之后才能操作该缓存行对应的内存数据 |
| E 独享、互斥（Exclusive） | 该缓存行有效，数据和内存中的数据一致，数据只存在于本缓存行中 | 缓存行必须监听其他缓存读主内存中该缓存行相对应的内存的操作，一旦有这种操作，该缓存行需要变成S状态 |
|     S 共享（Shared）      | 该缓存行有效，数据和内存中的数据一致，数据同时存在于其他缓存中 | 缓存行必须监听其他缓存是该缓存行无效或者独享该缓存行的请求，并将该缓存行置为I状态 |
|     I 无效（Invalid）     |                       该缓存行数据无效                       | 无                                                           |



#### 内存屏障

​		内存屏障是指“由于编译器的优化和缓存的使用，导致对内存的写入操作不能及时的反应出来，也就是说当完成对内存的写入操作之后，读取出来的可能是旧的内容”

​		内存屏障的分类：

1. 编译器引起的内存屏障
2. 缓存引起的内存屏障
3. 乱序执行引起的内存屏障

#### Happens before原则

- 如果操作1 happens-before 操作2，那么第操作1的执行结果将对操作2可见，而且操作1的执行顺序排在第操作2之前。
- 两个操作之间存在happens-before关系，并不意味着一定要按照happens-before原则制定的顺序来执行。如果重排序之后的执行结果与按照happens-before关系来执行的结果一致，那么这种重排序并不非法。

 

### 4、synchronized关键字

加锁原理--monitor

​		重量级锁也就是通常说synchronized的对象锁，锁标识位为10，其中指针指向的是monitor对象（也称为管程或监视器锁）的起始地址。每个对象都存在着一个 monitor 与之关联，对象与其 monitor 之间的关系有存在多种实现方式，如monitor可以与对象一起创建销毁或当线程试图获取对象锁时自动生成，但当一个 monitor 被某个线程持有后，它便处于锁定状态。

锁升级过程--自旋锁、用户态及内核态

		锁升级的方向是：无锁——>偏向锁——>轻量级锁——>重量级锁，并且膨胀方向不可逆。

1.偏向锁
偏向锁是JDK6中引入的一项锁优化，大多数情况下，锁不仅不存在多线程竞争，而且总是由同一线程多次获得，为了让线程获得锁的代价更低而引入了偏向锁。
偏向锁会偏向于第一个获得它的线程，如果在接下来的执行过程中，该锁没有被其他的线程获取，则持有偏向锁的线程将永远不需要同步。

2.轻量级锁
如果明显存在其它线程申请锁，那么偏向锁将很快升级为轻量级锁。

3.自旋锁
自旋锁原理非常简单，如果持有锁的线程能在很短时间内释放锁资源，那么那些等待竞争锁的线程就不需要做内核态和用户态之间的切换进入阻塞挂起状态，它们只需要等一等（自旋），等持有锁的线程释放锁后即可立即获取锁，这样就避免用户线程和内核的切换的消耗。

4.重量级锁
指的是原始的Synchronized的实现，重量级锁的特点：其他线程试图获取锁时，都会被阻塞，只有持有锁的线程释放锁之后才会唤醒这些线程。



用户进程缓冲区

你看一些程序在读取文件时，会先申请一块内存数组，称为buffer，然后每次调用read，读取设定字节长度的数据，写入buffer。之后的程序都是从buffer中获取数据，当buffer使用完后，在进行下一次调用，填充buffer。所以说：用户缓冲区的目的就是是为了减少系统调用次数，从而降低操作系统在用户态与核心态切换所耗费的时间。除了在进程中设计缓冲区，内核也有自己的缓冲区。

内核缓存区

当一个用户进程要从磁盘读取数据时，内核一般不直接读磁盘，而是将内核缓冲区中的数据复制到进程缓冲区中。但若是内核缓冲区中没有数据，内核会把对数据块的请求，加入到请求队列，然后把进程挂起，为其它进程提供服务。等到数据已经读取到内核缓冲区时，把内核缓冲区中的数据读取到用户进程中，才会通知进程，当然不同的IO模型，在调度和使用内核缓冲区的方式上有所不同。



使用示例：原子性、可见性、有序性

```java
public class 原子性示例 {
    public int inc = 0;

    public synchronized void increase() {
        inc++;
    }

    public static void main(String[] args) throws InterruptedException {
        final 原子性示例 test = new 原子性示例();
        for(int i=0;i<10;i++){
            new Thread(){
                public void run() {
                    for(int j=0;j<1000;j++)
                        test.increase();
                    System.out.println(Thread.currentThread().getName() + "已完成");
                    Thread.currentThread().interrupt();
                };
            }.start();
        }

        while(Thread.activeCount()>2) {  //保证前面的线程都执行完
            System.out.println(Thread.activeCount() + "未完成");
            Thread.yield();
        }
        System.out.println(test.inc);
    }
}

/** 运行结果 **/
10000

Process finished with exit code 0
```



```java
public class 可见性示例 {
    private static boolean stop;

    public static void main(String[] args) throws InterruptedException {
        //普通情况下，多线程不能保证可见性
        new Thread(() -> {
            System.out.println("线程A开始运行...");
            while (!getStop()) ;
            System.out.println("线程A运行结束.");
        }).start();
        Thread.sleep(10);
        new Thread(() -> {
            System.out.println("线程B开始运行...");
            stop = true;
            System.out.println("线程B运行结束.");
        }).start();
    }

    public static synchronized boolean getStop(){
        return stop;
    }
}

/** 运行结果 **/
线程A开始运行...
线程B开始运行...
线程B运行结束.
线程A运行结束.

Process finished with exit code 0
```



```java
public class 有序性示例 {
    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            System.out.println("线程A："+Singleton.getInstance().num);
        }).start();
        new Thread(() -> {
            System.out.println("线程B："+Singleton.getInstance().num);
        }).start();
    }
}

class Singleton {
    private static Singleton instance;

    public static int num = 0;

    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
            num ++;
        }else{
            num --;
        }
        return instance;
    }
}

/** 运行结果 **/
线程A：1
线程B：0

Process finished with exit code 0
```

 

### 5、volatile关键字

原理:

“观察加入volatile关键字和没有加入volatile关键字时所生成的汇编代码发现，加入volatile关键字时，会多出一个lock前缀指令”

　　lock前缀指令实际上相当于一个内存屏障（也成内存栅栏），内存屏障会提供3个功能：

　　1）它确保指令重排序时不会把其后面的指令排到内存屏障之前的位置，也不会把前面的指令排到内存屏障的后面；即在执行到内存屏障这句指令时，在它前面的操作已经全部完成；

　　2）它会强制将对缓存的修改操作立即写入主存；

　　3）如果是写操作，它会导致其他CPU中对应的缓存行无效。

使用示例：原子性、可见性、有序性

```java
public class 原子性示例 {
    public volatile int inc = 0;

    public void increase() {
        inc++;
    }

    public static void main(String[] args) {
        final 原子性示例 test = new 原子性示例();
        for(int i=0;i<10;i++){
            new Thread(){
                public void run() {
                    for(int j=0;j<1000;j++)
                        test.increase();
                };
            }.start();
        }

        while(Thread.activeCount()>1)  //保证前面的线程都执行完
            Thread.yield();
        System.out.println(test.inc);
    }
}

/** 运行结果 **/
10000

Process finished with exit code 0
```



```java
public class 可见性示例 {
    private static volatile boolean stop;

    public static void main(String[] args) throws InterruptedException {
        //普通情况下，多线程不能保证可见性
        new Thread(() -> {
            System.out.println("线程A开始运行...");
            while (!stop) ;
            System.out.println("线程A运行结束.");
        }).start();
        Thread.sleep(10);
        new Thread(() -> {
            System.out.println("线程B开始运行...");
            stop = true;
            System.out.println("线程B运行结束.");
        }).start();
    }
}

/** 运行结束 **/
线程A开始运行...
线程B开始运行...
线程B运行结束.
线程A运行结束.

Process finished with exit code 0
```

 

```java
public class 有序性示例 {
    public static void main(String[] args) throws InterruptedException {
        new Thread(() -> {
            System.out.println("线程A："+Singleton.getInstance().num);
        }).start();
        new Thread(() -> {
            System.out.println("线程B："+Singleton.getInstance().num);
        }).start();
    }
}

class Singleton {
    private volatile static Singleton instance;

    public static int num = 0;

    public static Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
            num ++;
        }else{
            num --;
        }
        return instance;
    }

}
/** 运行结果 **/
线程A：1
线程B：0

Process finished with exit code 0
```



### 6、CAS操作（自旋锁）

​	原理：自旋锁是一种十分常见的锁结构，自旋锁在申请资源但是申请不到的情况下并不会挂起，而是会选择持续申请。这种锁结果适用于每个线程占用较少时间的锁，并且线程阻塞状态切换的代价远高于等待的代价时使用。

使用示例

原理--比较交换：

​		是实现我们平时所说的自旋锁或乐观锁的核心操作。它的实现很简单，就是用一个预期的值和内存值进行比较，如果两个值相等，就用预期的值替换内存值，并返回 true。否则，返回 false。

存在的问题--ABA等

​		就是说一个线程把数据A变为了B，然后又重新变成了A。此时另外一个线程读取的时候，发现A没有变化，就误以为是原来的那个A。

 

## **四、**生产者消费者--使用synchornized实现

 

```java
public class 生产者消费者使用synchornized实现 {
    public static void main(String[] args) {
        System.out.println("Hello World!");
        Product product = new Product();
        Comsuer comsuer = new Comsuer(product);
        Produce produce = new Produce(product);
        new Thread(produce).start();
        new Thread(produce).start();
        new Thread(produce).start();
        new Thread(produce).start();
        new Thread(produce).start();
        new Thread(produce).start();
        new Thread(produce).start();
        new Thread(comsuer).start();
        new Thread(comsuer).start();
        new Thread(comsuer).start();
        try {
            Thread.sleep(1000 * 3);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.exit(-1);
    }
}


class Produce implements Runnable {
    Product product;
    public Produce(Product product){
        this.product=product;

    }
    @Override
    public void run() {

        while (true){
            product.push();
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}

class Comsuer implements Runnable {
    Product product;
    public Comsuer(Product product){
        this.product = product;
    }
    @Override
    public void run() {
        while (true){
            product.take();
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

    }
}

class Product {
    final int MAX_NUMBER = 10;
    private int max;

    public synchronized void push() {
        while (max >= MAX_NUMBER) {
            try {
                System.out.println(Thread.currentThread().getName() + "生产过剩，当前：" + max);
                this.wait();
            } catch (Exception e) {

            }
        }
        this.max++;
        System.out.println(Thread.currentThread().getName() + "生产+1，当前：" + max);
        notifyAll();
    }

    public synchronized void take() {
        while (max <= 0) {
            try {
                System.out.println(Thread.currentThread().getName() + "消费已完，当前：" + max);
                this.wait();
            } catch (Exception e) {
            }
        }
        this.max--;
        System.out.println(Thread.currentThread().getName() + "消费-1，当前：" + max);
        notifyAll();
    }
}

/** 运行结果 **/
Hello World!
Thread-0生产+1，当前：1
Thread-1生产+1，当前：2
Thread-6生产+1，当前：3
Thread-5生产+1，当前：4
Thread-4生产+1，当前：5
Thread-2生产+1，当前：6
Thread-3生产+1，当前：7
Thread-7消费-1，当前：6
Thread-8消费-1，当前：5
Thread-9消费-1，当前：4
Thread-0生产+1，当前：5
Thread-3生产+1，当前：6
Thread-8消费-1，当前：5
Thread-1生产+1，当前：6
Thread-9消费-1，当前：5
Thread-5生产+1，当前：6
Thread-4生产+1，当前：7
Thread-6生产+1，当前：8
Thread-7消费-1，当前：7
Thread-2生产+1，当前：8
Thread-1生产+1，当前：9
Thread-2生产+1，当前：10
Thread-7消费-1，当前：9
Thread-9消费-1，当前：8
Thread-8消费-1，当前：7
Thread-6生产+1，当前：8
Thread-0生产+1，当前：9
Thread-4生产+1，当前：10
Thread-3生产过剩，当前：10
Thread-5生产过剩，当前：10

Process finished with exit code -1
```

 