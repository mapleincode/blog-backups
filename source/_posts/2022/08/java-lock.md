---
title: Java 的 21 种锁
date: 2022-08-04 11:37:58
tags: java
---



1. 乐观锁

   - **并发控制概念**

   - OCC
   - 适合写入少，回滚成本低于锁的场景
   - 本质没有锁，靠自身校验来实现”锁的效果“
   - 事务写入数据后，提供时间戳或者类似的凭证
     - 通过凭证检查数据是否被修改
       - 被修改，回滚事务
       - 无修改，提交事务
   - 不足
     - 读取独占的资源可能造成死锁
2. 悲观锁

   - **并发控制概念**
   - PCC
   - 适合写入多，回滚成本高于锁的场景
   - 先竞争获取锁，获得锁只能才能写入数据，写完数据释放锁
   - 不足
     - 容易造成死锁
     - 加锁会有效率消耗
3. 自旋锁

   - **技术方案**
   - 现成执行忙循环
   - 占用时间长会导致资源消耗
   - CAS
     - 默认 10 次
     - -XX:PreBlockSpin 修改
4. 可重入锁
   - 递归锁
   - 也是技术方案
   - 一个线程获得对象锁之后，他可以再次获得本对象上的其他锁
   - 避免死锁，因为其他线程无法获取任意的锁
   - ReentrantLock， synchronized
   - 面试
     - 必须释放所有锁才能结束线程
     - 一个锁释放两次
       - java.lang.IllegalMonitorStateException
5. 读写锁
   - 技术方案
   - ReentrantReadWriteLock
   - 读锁
     - 允许多个线程同时
   - 写锁
     - 仅允许一个线程
6. 公平锁
   - 概念
   - 通过队列的方式，先到先得
   - ReentrantLock 可以配置为公平锁
7. 非公平锁
   - 概念
   - 不通过队列
   - 性能高
   - 可能造成线程饥饿
   - ReentrantLock 默认为非公平锁
   - synchronized 非公平锁
8. 共享锁
   - 概念
   - 多个线程持有同个锁，多个线程可以获得锁
   - 区别于一般的锁，一般的锁只能由一个线程获得。
   - 只能限制读取
   - ReentrantReadWriteLock
9. 独占锁
   - 概念
   - 和悲观锁、互斥锁本质一样，角度不一样
   - synchronized，ReentrantLock
10. 重量级锁
  - synchronized 通过监视器锁 (monitor) 实现
  - monitor 依赖操作系统的 Mutex Lock
  - 成本高
  - 区别于**轻量级锁**
11. 轻量级锁
    - 区别于重量级锁
    - CAS
    - JDK 6 加入
12. 偏向锁
    - java 6
      - 提高 jvm 性能
      - 早期的代码里同步代码比较多，使用偏向锁能提高不少性能
    - java 15 废弃
      - 现在引入了 Hashmap，ArrayList 等非同步集合，性能又饿不少提升
      - 偏向锁的代码对 Hotspot 性能产生了影响
    - HotSpot 虚拟机使用的优化技术
    - 如果一个 monitor 一直由某个特定的线程持有，除非其他线程访问，否则取消执行 CAS 的原子操作。
13. 分段锁
    - ConcurrentHashMap
      - Segment 数组
      - 继承 ReentrantLock
      - Segment 是线程安全
    - 对需要修改的数据的 key 进行加锁，而不是整个对象
    - 实现并发的对同个 Map 的不同的 key-value 进行修改
14. 互斥锁
    - 与悲观锁、独占锁同义
15. 同步锁
    - 与互斥锁同意
    - 同事只允许一个线程访问数据
16. 死锁
    - A B 相互锁了一个资源之后，依然需要对方的资源
    - 两者无法释放锁也无法获取新的资源，造成死锁
17. 锁粗化
    -  线程需要操作一个组的资源，在操作不是频繁的前提下，可以考虑直接对整租资源上锁，而不是在操作资源的时候单独上锁
    - 开发思路
18. 锁消除
    - 线程逃逸
    - 虚拟机发现对象的作用域有且被一个线程访问，不会被其他线程访问
    - 直接把锁消除以提高性能
    - jvm 优化技术





## **synchronized**

- 示例 1

```java
// 著作权归https://pdai.tech所有。
// 链接：https://pdai.tech/md/java/thread/java-thread-x-key-synchronized.html

public class SynchronizedObjectLock implements Runnable {
    static SynchronizedObjectLock instence = new SynchronizedObjectLock();

    @Override
    public void run() {
        // 同步代码块形式——锁为this,两个线程使用的锁是一样的,线程1必须要等到线程0释放了该锁后，才能执行
        synchronized (this) {
            System.out.println("我是线程" + Thread.currentThread().getName());
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + "结束");
        }
    }

    public static void main(String[] args) {
        Thread t1 = new Thread(instence);
        Thread t2 = new Thread(instence);
        t1.start();
        t2.start();
    }
}

// 我是线程Thread-0
// Thread-0结束
// 我是线程Thread-1
// Thread-1结束
```



- 示例 2

```java
// 著作权归https://pdai.tech所有。
// 链接：https://pdai.tech/md/java/thread/java-thread-x-key-synchronized.html

public class SynchronizedObjectLock implements Runnable {
    static SynchronizedObjectLock instence = new SynchronizedObjectLock();
    // 创建2把锁
    Object block1 = new Object();
    Object block2 = new Object();

    @Override
    public void run() {
        // 这个代码块使用的是第一把锁，当他释放后，后面的代码块由于使用的是第二把锁，因此可以马上执行
        synchronized (block1) {
            System.out.println("block1锁,我是线程" + Thread.currentThread().getName());
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("block1锁,"+Thread.currentThread().getName() + "结束");
        }

        synchronized (block2) {
            System.out.println("block2锁,我是线程" + Thread.currentThread().getName());
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("block2锁,"+Thread.currentThread().getName() + "结束");
        }
    }

    public static void main(String[] args) {
        Thread t1 = new Thread(instence);
        Thread t2 = new Thread(instence);
        t1.start();
        t2.start();
    }
}

// block1锁,我是线程Thread-0
// block1锁,Thread-0结束

// block2锁,我是线程Thread-0　　// 可以看到当第一个线程在执行完第一段同步代码块之后，第二个同步代码块可以马上得到执行，因为他们使用的锁不是同一把
// block1锁,我是线程Thread-1
// block2锁,Thread-0结束 //
// block1锁,Thread-1结束 // Thread-0 的 block2 锁 和它谁先结束应该会随机

// block2锁,我是线程Thread-1
// block2锁,Thread-1结束
```





## Lock 和 synchronized的区别

- Lock 可以中断 (unlock)
- synchronized 是 Java 内置的关键，Lock 仅仅是方法
- Lock 可以读写锁、共享锁等非独占锁



- 接口声明

  ```java
  public interface Lock {
      void lock();
      void lockInterruptibly() throws InterruptedException;
      boolean tryLock();
      boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
      void unlock();
      Condition newCondition();
  }
  ```

  

## ReentrantLock 和 synchronized ，Lock 的区别

ReentrantLock  是 Lock 的实现之一。



```java
public class Test {
    private ArrayList<Integer> arrayList = new ArrayList<Integer>();
    private Lock lock = new ReentrantLock();
    public static void main(String[] args)  {
        final Test test = new Test();

        new Thread(){
            public void run() {
                test.insert(Thread.currentThread());
            };
        }.start();

        new Thread(){
            public void run() {
                test.insert(Thread.currentThread());
            };
        }.start();
    }

    public void insert(Thread thread) {
        lock.lock();
        try {
            System.out.println(thread.getName()+"得到了锁");
            for(int i=0;i<5;i++) {
                arrayList.add(i);
            }
        } catch (Exception e) {
            // TODO: handle exception
        }finally {
            System.out.println(thread.getName()+"释放了锁");
            lock.unlock();
        }
    }
}
```



## Mutex Lock 是否真的很消耗性能？

Mutex Lock 这个词组本身就已经是错误的，Mutex  就是互斥锁，也就是我们常说的锁基本都是互斥锁（共享锁除外），Mutex  本身就包含了 Lock 的概念，而且被 Lock 更准确。

Mutex 性能低下？

但是 Mutex  只是一个概念，就好比说火车速度一定不如汽车这样奇怪，火车有内燃机车，但也包含高铁甚至磁悬浮。而汽车也包含了普通的汽车、超跑。

每个系统的 Mutex  实现方式本身就不同。拿 windows 而言：

> 一个 Mutex 仅仅从 Lock 到 Unlock 具体开销是多少,是不是占用很多时间,从 [Always Use a Lightweight Mutex](http://preshing.com/20111124/always-use-a-lightweight-mutex/) 从可以看到在 windows 中有两种 Mutex:[Muetx](http://msdn.microsoft.com/en-us/library/windows/desktop/ms684266(v=vs.85).aspx) 和 [Critical Section](http://msdn.microsoft.com/en-us/library/windows/desktop/ms682530(v=vs.85).aspx), 重量级和轻量级的区别,两者的时间开销相差 25 倍多,所以一直使用轻量级的 Mutex.

拿 Linux 下的 POSIX thread 作为对比，POSIX thread 本身就属于轻量级锁。

在 2.4GHz i5 的单核计算速度中，通过 10 万次的 lock/unlock ，实际上平均每次操作占用的时间是 34 ns。对于分布式的网络相对比，几乎说可以忽略不计。

> 在 Always Use a Lightweight Mutex 文章中，作者用来测试的 2.66 GHz 的 Xeon 处理器，Windows Mutex 达到了 608 ns / 次，和 POSIX thread 对比的确差异很大。

但是实际上，在现在 2022 年，绝大多数企业逐步抛弃了 windows server 作为服务器，而选用了性价比更高的 linux。那么 Mutex 真的需要像以前一样避免使用么？我觉得 lock 本身占用的资源其实完全是可控的，可怕的不是工具，而是用工具的人。

参考:

1. http://dreamrunner.org/blog/2014/06/29/qian-tan-mutex-lock/
1. https://cloud.tencent.com/developer/news/688367