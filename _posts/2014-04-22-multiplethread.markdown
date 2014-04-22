---
layout: post
title: "多线程的注意问题"
date: 2014-04-22 19:28:01
category: java
---

由于最近处理的数据都算是较大的数据，动辄就是几千万条甚至几亿条，于是写代码的时候基本就都是用多线程来处理，玩命的压榨CPU的性能。可之前在实验室的时候，对多线程编程写的比较少，而且也没有怎么系统的学它，基本都是靠看看java doc或者别人的blog来学习。现在在天天使用的时候就开始狂踩坑。

以下是需要特别注意的几点：

### i++

经常需要统计下目前的程序已经运行了多少条，于是习惯性的在多线程外放一个i，然后在线程里面进行i++，然后在运行结束的时候，直接log出来。如下面的代码：

{% highlight java %}
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class TestMultipleThread {
    static Logger logger = LoggerFactory.getLogger(TestMultipleThread.class);
    public static void main(String[] args) {
        TestMultipleThread tmt = new TestMultipleThread();
        tmt.run();
    }

    private void run() {
        int cpu = Runtime.getRuntime().availableProcessors();
        ThreadPoolExecutor service = new ThreadPoolExecutor(cpu, cpu, 1, TimeUnit.HOURS,
                new ArrayBlockingQueue<Runnable>(cpu * 4),
                new ThreadPoolExecutor.CallerRunsPolicy());
        for(int i = 0; i < 1000; i ++){
            service.submit(new Worker(i));
        }
        service.shutdown();
        try {
            service.awaitTermination(1, TimeUnit.MINUTES);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        logger.info("count : {}", count);
    }
    int count = 0;
    class Worker implements Runnable{
        int i;
        public Worker(int i ){
            this.i = i;
        }
        @Override
        public void run() {
            logger.info("i : {}", i);
            count++;
        }
    }
}
{% endhighlight %}



每次的结果都不太一样，相差也就是个两位数。自己以为是多线程里面的问题，也没有仔细的纠结它。现在是工程上需要精确的数据，于是不得不纠结这个问题，经过旁边小伙伴的指点，这个i++是不对的，应该采用AtomicInteger这种东东来，于是代码需要改成如下这样。

{% highlight java %}
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;
import java.util.concurrent.atomic.AtomicInteger;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class TestMultipleThread {
    static Logger logger = LoggerFactory.getLogger(TestMultipleThread.class);
    public static void main(String[] args) {
        TestMultipleThread tmt = new TestMultipleThread();
        tmt.run();
    }

    private void run() {
        int cpu = Runtime.getRuntime().availableProcessors();
        ThreadPoolExecutor service = new ThreadPoolExecutor(cpu, cpu, 1, TimeUnit.HOURS,
                new ArrayBlockingQueue<Runnable>(cpu * 4),
                new ThreadPoolExecutor.CallerRunsPolicy());
        for(int i = 0; i < 1000; i ++){
            service.submit(new Worker(i));
        }
        service.shutdown();
        try {
            service.awaitTermination(1, TimeUnit.MINUTES);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        logger.info("atom : {}", atom.get());
    }
    AtomicInteger atom = new AtomicInteger(0);
    class Worker implements Runnable{
        int i;
        public Worker(int i ){
            this.i = i;
        }
        @Override
        public void run() {
            logger.info("i : {}", i);
            atom.incrementAndGet();
        }
        
    }
}
{% endhighlight %}

为什么呢？因为在执行i++的时候，需要做4步操作：取到i值，+1，然后存回i的值。当这个事情变成多线程的时候呢？假设线程a取到i的值为2，正在执行第2步，而这时候线程b也在用i，它也取到了i=2，然后它在写i=3的时候，其实线程a已经把i变成3了，但是它不知道，于是还是写了个3，这样数字就不对了。

### HashMap

在统计和各种运算的时候，hashmap的使用很频繁，hashmap在多线程上有个深坑，没太明白具体原因，只能是能避免就避免。

结论：

在多线程编程的时候，尽量的少共享变量，如果非要共享某些变量，那么一定要确保它的内部是线程安全的。

可以将ArrayList => Vector, HashMap => ConcurrentHashMap。







