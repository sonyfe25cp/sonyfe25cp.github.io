---
layout: post
title: "Concurrency in JAVA"
category: java
---


###Introduction

电脑可以同时做多件事情，于是并发就成了码农的必备技能。复习下并发中的各种知识和问题。

###Synchronization

线程之间的交互主要是通过共享对象或者其变量来实现，于是就容易出现线程冲突、内存不一致的情况。

####Thread Interference

线程冲突，是指不同的线程在操作同一个数据时出现的插入现象。

```
class Counter {
    private int c = 0;
    public void increment() {
        c++;
    }
    public void decrement() {
        c--;
    }
    public int value() {
        return c;
    }
}
```
当线程A和B都在对上面的对象操作时，就有可能按照下面顺序发生事情：

	Thread A: Retrieve c.
	Thread B: Retrieve c.
	Thread A: Increment retrieved value; result is 1.
	Thread B: Decrement retrieved value; result is -1.
	Thread A: Store result in c; c is now 1.
	Thread B: Store result in c; c is now -1.

于是A的结果就都被B给覆盖掉了，于是错了。

####Memory Consistency Errors

>Memory consistency errors occur when different threads have inconsistent views of what should be the same data

假定有这么个计数器，并且有A和B两个线程。
```
int counter = 0;
```
A对这个计数器进行了
```
counter++;
```
而B则在执行
```
System.out.println(counter);
```
假定这两段代码是在同一个线程内，则打印出的一定是1；当分别在不同线程时，并不能保证B打印出的是1还是0。

解决办法：利用同步的方法来确保数据一致。

####Synchronized Methods

Java中有两类同步：同步方法和同步声明。下面先说同步方法，同步声明之后再说。

```
public class SynchronizedCounter {
    private int c = 0;
    public synchronized void increment() {
        c++;
    }
    public synchronized void decrement() {
        c--;
    }
    public synchronized int value() {
        return c;
    }
}
```
使用了`synchronized`的方法会起到下面的作用：

* 首先，就不能有两个线程同时使用这个方法了。其他线程必须要等待第一个使用该方法的线程释放资源
* 第二，由于上面的特性，就保证了第一个线程修改的数据资源，可以对其他所有线程生效。

这种同步的方法就解决了前面提出的两种问题。

####Intrinsic Locks and Synchronization

同步是建立在锁机制的思想上。每个对象都有一个自身锁，当一个线程获取了该锁之后，其他线程就不能获取它，直到该线程释放资源。其他线程就处于block的状态。

#####Synchronized Method

当一个线程去执行synchronized方法时，它去申请的是这个对象的锁，然后操作方法，然后释放。

#####Synchronized Statements

对于同步方法，还可以采用同步声明的方式实现：

```
public void addName(String name) {
    synchronized(this) {
        lastName = name;
        nameCount++;
    }
    nameList.add(name);
}
```

这里，每次在执行addName方法时，都会去请求对象锁，然后执行操作，跟同步方法的效果就一致了。


然而有的时候，我们并不需要去同步一个对象，而只是要同步其中的成员变量，这时候就可以用同步声明。

例如对象MsLunch，其中c1和c2永远不会同时修改，于是c1和c2的操作就没有必要同步，仅需要同步它们自身即可。

```
public class MsLunch {
    private long c1 = 0;
    private long c2 = 0;
    private Object lock1 = new Object();
    private Object lock2 = new Object();
    public void inc1() {
        synchronized(lock1) {
            c1++;
        }
    }
    public void inc2() {
        synchronized(lock2) {
            c2++;
        }
    }
}
```

这样就可以确保c1和c2被分别同步。

#####Reentrant Synchronization

一个线程不能够去申请已经被其他线程占有的锁，但是可以得到它已经有的锁。也就是可以让线程申请同一个锁来再次抢占资源。

####原子性

原子性保证了这个操作要么发生，要么不发生，它不会被打断，也就不会发生之前提到的线程冲突的问题。

###Liveness

>A concurrent application's ability to execute in a timely manner is known as its liveness.

这里主要介绍死锁的问题。

####Deadlock

死锁就是说两个线程都在等对方释放资源，于是就永远的等下去了。

```
public class Deadlock {
    static class Friend {
        private final String name;
        public Friend(String name) {
            this.name = name;
        }
        public String getName() {
            return this.name;
        }
        public synchronized void bow(Friend bower) {
            System.out.format("%s: %s"
                + "  has bowed to me!%n", 
                this.name, bower.getName());
            bower.bowBack(this);
        }
        public synchronized void bowBack(Friend bower) {
            System.out.format("%s: %s"
                + " has bowed back to me!%n",
                this.name, bower.getName());
        }
    }
    public static void main(String[] args) {
        final Friend alphonse =
            new Friend("Alphonse");
        final Friend gaston =
            new Friend("Gaston");
        new Thread(new Runnable() {
            public void run() { alphonse.bow(gaston); }
        }).start();
        new Thread(new Runnable() {
            public void run() { gaston.bow(alphonse); }
        }).start();
    }
}
```

####Starvation and Livelock

#####Starvation

>Starvation describes a situation where a thread is unable to gain regular access to shared resources and is unable to make progress. 

例如一个对象提供了个同步方法，但是这个方法执行时间很长，当一个线程获得它之后，其他线程都在等它结束，就是这种情况。

#####Livelock

如果一个线程负责提供给另一个线程结果，而这个线程还负责提供给第三个线程结果，那么这就形成了LiveLock。整个线程并没有block，它们只是在等前面的结果。

###Guarded Blocks

线程之间经常需要协调资源，如：

```
public void guardedJoy() {
    // Simple loop guard. Wastes
    // processor time. Don't do this!
    while(!joy) {}
    System.out.println("Joy has been achieved!");
}
```

这样做会让线程在无限制的轮训，cpu就满了，很没有意义。
改成如下这样能好一点：

```
public synchronized void guardedJoy() {
    // This guard only loops once for each special event, which may not
    // be the event we're waiting for.
    while(!joy) {
        try {
            wait();
        } catch (InterruptedException e) {}
    }
    System.out.println("Joy and efficiency have been achieved!");
}
```

配合

```
public synchronized notifyJoy() {
    joy = true;
    notifyAll();
}
```

就可以使得某个线程在获得该对象锁之后，执行了notifyJob后会notifyAll，然后某个获得了锁的就可以执行guardedJoy了。

可以利用notify和wait构成生产者-消费者模式：

```
public class Drop {
    // Message sent from producer
    // to consumer.
    private String message;
    // True if consumer should wait
    // for producer to send message,
    // false if producer should wait for
    // consumer to retrieve message.
    private boolean empty = true;
    public synchronized String take() {
        // Wait until message is
        // available.
        while (empty) {
            try {
                wait();
            } catch (InterruptedException e) {}
        }
        // Toggle status.
        empty = true;
        // Notify producer that
        // status has changed.
        notifyAll();
        return message;
    }
    public synchronized void put(String message) {
        // Wait until message has
        // been retrieved.
        while (!empty) {
            try { 
                wait();
            } catch (InterruptedException e) {}
        }
        // Toggle status.
        empty = false;
        // Store message.
        this.message = message;
        // Notify consumer that status
        // has changed.
        notifyAll();
    }
}
```

```
import java.util.Random;
public class Producer implements Runnable {
    private Drop drop;
    public Producer(Drop drop) {
        this.drop = drop;
    }
    public void run() {
        String importantInfo[] = {
            "Mares eat oats",
            "Does eat oats",
            "Little lambs eat ivy",
            "A kid will eat ivy too"
        };
        Random random = new Random();
        for (int i = 0;
             i < importantInfo.length;
             i++) {
            drop.put(importantInfo[i]);
            try {
                Thread.sleep(random.nextInt(5000));
            } catch (InterruptedException e) {}
        }
        drop.put("DONE");
    }
}
```

```
import java.util.Random;
public class Consumer implements Runnable {
    private Drop drop;
    public Consumer(Drop drop) {
        this.drop = drop;
    }
    public void run() {
        Random random = new Random();
        for (String message = drop.take();
             ! message.equals("DONE");
             message = drop.take()) {
            System.out.format("MESSAGE RECEIVED: %s%n", message);
            try {
              Thread.sleep(random.nextInt(5000));
            } catch (InterruptedException e) {}
        }
    }
}
```

执行：

```
public class ProducerConsumerExample {
    public static void main(String[] args) {
        Drop drop = new Drop();
        (new Thread(new Producer(drop))).start();
        (new Thread(new Consumer(drop))).start();
    }
}
```
###Immutable Objects

>An object is considered immutable if its state cannot change after it is constructed

不可改变的对象在并发程序中很实用，由于它们不会被改变，于是不用担心线程冲突、数据不一致的问题。但是，码农不太喜欢用，因为他们会担心重新创建一个对象的代价和更新数据的代价。事实上，创建一个对象没那么严重。

也就是说，该用final的就要用。

###High Level Concurrency Objects

下面来一些高级货，concurrency包里有很多现成的东西。

####Lock Objects

Synchroized代码依赖于锁，而对象自身的锁虽然易用，但有比较多的限制。这里有了新的锁。

```
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;
import java.util.Random;
public class Safelock {
    static class Friend {
        private final String name;
        private final Lock lock = new ReentrantLock();
        public Friend(String name) {
            this.name = name;
        }
        public String getName() {
            return this.name;
        }
        public boolean impendingBow(Friend bower) {
            Boolean myLock = false;
            Boolean yourLock = false;
            try {
                myLock = lock.tryLock();
                yourLock = bower.lock.tryLock();
            } finally {
                if (! (myLock && yourLock)) {
                    if (myLock) {
                        lock.unlock();
                    }
                    if (yourLock) {
                        bower.lock.unlock();
                    }
                }
            }
            return myLock && yourLock;
        }            
        public void bow(Friend bower) {
            if (impendingBow(bower)) {
                try {
                    System.out.format("%s: %s has"
                        + " bowed to me!%n", 
                        this.name, bower.getName());
                    bower.bowBack(this);
                } finally {
                    lock.unlock();
                    bower.lock.unlock();
                }
            } else {
                System.out.format("%s: %s started"
                    + " to bow to me, but saw that"
                    + " I was already bowing to"
                    + " him.%n",
                    this.name, bower.getName());
            }
        }
        public void bowBack(Friend bower) {
            System.out.format("%s: %s has" +
                " bowed back to me!%n",
                this.name, bower.getName());
        }
    }
    static class BowLoop implements Runnable {
        private Friend bower;
        private Friend bowee;
        public BowLoop(Friend bower, Friend bowee) {
            this.bower = bower;
            this.bowee = bowee;
        }    
        public void run() {
            Random random = new Random();
            for (;;) {
                try {
                    Thread.sleep(random.nextInt(10));
                } catch (InterruptedException e) {}
                bowee.bow(bower);
            }
        }
    }           
    public static void main(String[] args) {
        final Friend alphonse =
            new Friend("Alphonse");
        final Friend gaston =
            new Friend("Gaston");
        new Thread(new BowLoop(alphonse, gaston)).start();
        new Thread(new BowLoop(gaston, alphonse)).start();
    }
}
```
####Executors

之前自己写的多线程对小项目比较合适，对于大点的项目，需要有线程管理功能，于是就用Executor。

#####The Executor Interface

这个接口其实就是干了个 `start()`的事情。

#####The ExecutorService Interface

`ExecutorService`接口可以提供`executor`的功能，还可以接收runnable和callable的对象，用于后续的调度。submit方法会返回Future对象，可以用来接收执行之后的结果。

#####ScheduledExecutorService

可以用来执行定时任务，或者后延多久来启动。

####Thread Pools

大部分的Exccutor是用pool实现的，由于在大项目中频繁的创建线程很浪费内存，于是大多采用这种方式。

newFixedThreadPool可以确保每次只有固定数量的线程在工作，而且若其中有线程不工作，它会重新启动一个新的线程。
newSingleThreadExecutor可以使得一次只执行一个任务。
其他特殊需求可以自行使用ThreadPoolExecutor或者ScheduledThreadPoolExecutor

####Fork/Join

Fork/Join是为了更好的利用多核的。针对于那种可以切分为更细的任务。目标是把全部处理器资源利用起来。

#####Basic Use

```
if (my portion of the work is small enough)
  do the work directly
else
  split my work into two pieces
  invoke the two pieces and wait for the results
```

基本代码应该长上面这样，把这块代码包装在`ForkJoinTask`的子类，`RecursiveTask`or`RecursiveAction`。

写完这个类之后，创建任务，然后调用invoke()。

#####Blurring for Clarity

假定你要去模糊处理一个图片，原始图片用数组存储。处理方法是把每个像素点的值处理成它周围点的平均值，由于一个图可能很大，那么就要处理很久。

```
public class ForkBlur extends RecursiveAction {
    private int[] mSource;
    private int mStart;
    private int mLength;
    private int[] mDestination;  
    // Processing window size; should be odd.
    private int mBlurWidth = 15;  
    public ForkBlur(int[] src, int start, int length, int[] dst) {
        mSource = src;
        mStart = start;
        mLength = length;
        mDestination = dst;
    }
    protected void computeDirectly() {
        int sidePixels = (mBlurWidth - 1) / 2;
        for (int index = mStart; index < mStart + mLength; index++) {
            // Calculate average.
            float rt = 0, gt = 0, bt = 0;
            for (int mi = -sidePixels; mi <= sidePixels; mi++) {
                int mindex = Math.min(Math.max(mi + index, 0),
                                    mSource.length - 1);
                int pixel = mSource[mindex];
                rt += (float)((pixel & 0x00ff0000) >> 16)
                      / mBlurWidth;
                gt += (float)((pixel & 0x0000ff00) >>  8)
                      / mBlurWidth;
                bt += (float)((pixel & 0x000000ff) >>  0)
                      / mBlurWidth;
            }          
            // Reassemble destination pixel.
            int dpixel = (0xff000000     ) |
                   (((int)rt) << 16) |
                   (((int)gt) <<  8) |
                   (((int)bt) <<  0);
            mDestination[index] = dpixel;
        }
    }
```

接下来实现`compute()`方法，它要么实现模糊，要么切割任务。

```
protected static int sThreshold = 100000;
protected void compute() {
    if (mLength < sThreshold) {
        computeDirectly();
        return;
    }    
    int split = mLength / 2;    
    invokeAll(new ForkBlur(mSource, mStart, split, mDestination),
              new ForkBlur(mSource, mStart + split, mLength - split,
                           mDestination));
}
```

如果之前方法是继承的RecursiveAction，那么可以再ForkJoinPool中直接运行：

1. 创建任务
`ForkBlur fb = new ForkBlur(src, 0, src.length, dst);`
2. 创建ForkJoinPool，`ForkJoinPool pool = new ForkJoinPool();`
3. 运行任务`pool.invoke(fb);`

更专业的用法可以看 java.utils.Arrays的parallelSort()


####Concurrent Collections

* BlockingQueue：先进先出队列
* ConcurrentMap：正常map
* ConcurrentNavigableMap ：支持近似匹配，具体事项是CurrentSkipListMap


####Concurrent Random Numbers

在多线程中用`int i = ThreadLocalRandom.current() .nextInt(4, 77);`而不是Math.random()，这个性能更好




























