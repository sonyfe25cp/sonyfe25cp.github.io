---
layout: post
title: "Concurrency in JAVA"
category: java
---


###Introduction

电脑可以同时做多件事情，于是并发就成了码农的必备技能。复习下并发中的各种知识和问题。

###Thread Interference

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

###Memory Consistency Errors

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

###Synchronized Methods

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

###Intrinsic Locks and Synchronization

同步是建立在锁机制的思想上。每个对象都有一个自身锁，当一个线程获取了该锁之后，其他线程就不能获取它，直到该线程释放资源。其他线程就处于block的状态。

####Synchronized Method

当一个线程去执行synchronized方法时，它去申请的是这个对象的锁，然后操作方法，然后释放。

####Synchronized Statements

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

####Reentrant Synchronization

一个线程不能够去申请已经被其他线程占有的锁，但是可以得到它已经有的锁。也就是可以让线程申请同一个锁来再次抢占资源。

###原子性

原子性保证了这个操作要么发生，要么不发生，它不会被打断，也就不会发生之前提到的线程冲突的问题。

###Liveness

>A concurrent application's ability to execute in a timely manner is known as its liveness.

这里主要介绍死锁的问题。

###Deadlock

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

###Starvation and Livelock




















