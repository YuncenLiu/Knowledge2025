---
createTime: 2023-10-30
---
[toc]

LockSupport 是 concurrent 包下的工具，support 翻译支持的意思，意为锁的扩展

中断方式

+ `void interrupt()`   设置线程未中断状态
+ `static boolean interrupted()`  判断线程是否中断，清零线程的中断状态
+ `boolean isInterrupted()`  判断线程是否中断（不清空标志）、

```java
Object objectLock = new Object();  
new Thread(()->{  
    try { TimeUnit.SECONDS.sleep(1); } catch (InterruptedException e) { e.printStackTrace(); }  
    synchronized (objectLock){  
        System.out.println(Thread.currentThread().getName() + "\t --- come in");  
        try {  
            objectLock.wait();  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
        System.out.println(Thread.currentThread().getName() + "\t --- 被唤醒");  
    }  
},"t1").start();  
new Thread(() ->{  
    synchronized (objectLock){  
        objectLock.notify();  
        System.out.println(Thread.currentThread().getName() + "\t --- 发出通知");  
    }  
},"t2").start();
```

## 1、什么是线程中断机制？

一个线程不应该由其他线程强制中断或者停止，而是由自己停止，所以 `thrad.stop`、`thread.suspend`、`thread.resume` 这些方法都废弃了。
这一种协商机制，手动调用 interrupt 方法，底层给线程打上 Ture 标识，线程发现后，自行了断。

如何判断呢？
+ 通过 volatile 修饰变量进行判断
+ 通过对 atomic 对象进行判断
+ 使用 interrupt 等方式

当中断标识为 true 时，是否立刻停止？

否！协商形式，如果从 Flase 修改为 True，线程运行了 join、sleep 方法，将会抛出异常 InterruptedException，并且中断标识将会重新返回 False，不会中断处理，方案是在 Sleep 的 catch 中，再次中断。

## 2、线程等待唤醒机制

三种等待线程唤醒的方法

1. Object.wait()  让线程等待，Synchronized、await、notify [GitHub案例](https://github.com/YuncenLiu/code-example/blob/master/thread/src/main/java/com/liuyuncen/juc/LockSupport/LockSupportDemo.java)
2. JUC中 Condition 的 await 让线程等待，Lock、await、Signal [GitHub案例](https://github.com/YuncenLiu/code-example/blob/master/thread/src/main/java/com/liuyuncen/juc/LockSupport/LockSupportDemo2.java)
3. LockSupport 类可以阻塞当前线程以及唤醒指定被阻塞的线程
	1. park 阻塞当前线程
	2. unpark 唤醒阻塞线程

重点
1. LockSupport 是一个线程阻塞工具，所有方法都是静态
2. 提供 park 和 unpark 方法实现阻塞和接触的过程
3. 每个线程都有一个相关的 permit，最多只有1个，重复调用 unpark 不会积累凭证

## 3、如何中断运行中线程？

挂起线程：`LockSupprort.park()` 
唤醒线程：`LockSupprort.unpark()` 

## 4、如何停止运行中线程？

- `LockSupport.park()` 会让线程挂起，直到被唤醒或中断。
- `thread.interrupt()` 可用中断线程，并设置中断标志，线程在执行过程中自行发现，自行了断。

## 5、当前线程终端表示为 Ture，是否立刻停止？

否！协商形式，如果从 Flase 修改为 True，线程运行了 join、sleep 方法，将会抛出异常 InterruptedException，并且中断标识将会重新返回 False，不会中断处理，方案是在 Sleep 的 catch 中，再次中断。

## 6、静态方法 Thread.interrupted() 谈谈理解

`Thread.interrupted()` 是一个静态方法，用于检测当前线程是否已经被中断。它的作用和使用场景在多线程编程中非常重要，尤其是在处理中断信号时。理解 `Thread.interrupted()` 的工作机制有助于更好地掌握线程的中断操作。

+ Thread.interrupted() 是一个静态方法，用于检查当前线程的中断状态并清除该状态。它通常与 Thread.sleep()、Object.wait() 等阻塞方法一起使用，以响应中断请求。
+ Thread.isInterrupted() 是实例方法，不会清除中断标志，只是返回当前线程的中断状态。
+ 中断机制对于线程的优雅停止非常重要，尤其是在多线程编程中需要响应外部中断信号时。

为什么要清除中断状态？

调用 `Thread.interrupted()` 会清除当前线程的中断状态，这是为了防止中断标志被意外“传播”到后续的操作中。在一个线程执行过程中，可能会遇到多个可能的中断点。如果不清除中断标志，可能会导致其他部分的代码错误地认为线程已经中断，因此有时需要清除中断标志来明确中断的处理。