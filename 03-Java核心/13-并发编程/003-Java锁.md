---
createTime: 2023-10-30
---

## 1、乐观与悲观锁

悲观锁：写场景多、先加锁，保证写时数据正确
乐观锁：更新数据时进行判断，没有被修改写入，如果修改了放弃修改、重试强锁，例如 CAS

## 2、锁问题？

阿里开发手册：高并发时，同步调用应考量锁的性能损耗，能用无锁结构，就不要用锁，能锁区块就不要锁整体方法，能锁对象就不锁类。

固定部分：一个对象有 F1、F2两个方法，创建了 T1、T2 两个线程，线程之间间隔了 200ms，保证 T1 先执行，T2 再执行。

### 2.1、F1、F2 两个方法都有 synchronize 修饰，先打印 F1 还是 F2？

F1

### 2.2、F1 方法块内输出前睡眠3秒，都有 Synchronize 修饰，先打印 F1 还是 F2？

F1，现象是 等待3秒，先打印 F1，再打印 F2

一个对象里面如果有多个Synchronize 方法，某一时刻内，只要有一个线程去访问其中一个 Synchronize 方法，其他线程只能等待，换句话说，某一时刻内，只有唯一一个线程去访问这些 Synchronize 方法，锁的是当前对象 this，被锁定后，其他线程都不能进入当前对象的其他 Synchronize 方法。

### 2.3、新增普通 F3 方法，只有 F1、F2 是 Synchronize 修饰，先打印 F1 还是打印 F3？

F3
加了普通方法后，发现同步锁无关
### 2.4、F1 方法内输出前睡眠3秒，两个对象 O1、O2，O1 执行 F1、O2 执行 F2，先打印 F1还是F2？

F2，没有争抢行为？
换成两个对象后，不是同一把锁，此时不发生竞争关系

### 2.5、F1 延迟3秒，F1、F2 都是 static sync，先打印 F1 还是 F2

F1

### 2.6、F1 延迟3秒，F1、F2 都是 static sync，但是是两个对象 O1、O2，O1执行F1、O2执行F2，先打印F1、F2？

F1
静态方法，是类锁，无论几个对象，都是一样的
+ 普通同步方法，锁的是当前实例对象，通常是 this
+ 静态同步方法，当前 Class 类，无论 new 多少个，只有1个锁
+ 对于同步方法块，锁的锁 sync 括号内的对象

### 2.7、F1 延迟3秒，F1 是 static sync，F2 是 sync，先打印 F1、F2？

F2

### 2.8、F1延迟3秒，F1 是 static sync，F2 是 sync，两个对象 O1、O2，O1执行F1、O2执行F2，先打印 F1、F2？

F2
静态锁，也叫类锁，锁的方法区中有且只有一份，对象锁，则是通过一个类多个实例得到的对象，存在堆中，可以有多份。


## 3、源码分析 Synchronized

使用 monitorenter、monitorexit 指令，
一般情况 1个 enter、2个 exit，极限情况，直接抛异常的方法，1个enter、1个 exit
锁的是对象头

## 4、公平与非公平

```java
ReentrantLock lock = new ReentrantLock(true);
```

+ true 公平：先到先得，后来者列队，需要频繁切换线程，线程切换开销大
+ false （默认）非公平：CPU根据优先度分配，容易引发饥饿线程
	+ 更充分利用CPU时间片，减少CPU时间，线程切换少，开销小，所以默认非公平


### 5、可重入锁（递归锁）

定义：同一个线程，外层获取锁的时候，在进入内层方法会自动获取锁，前提是锁是同一个对象，不会因为已经获取没有释放而阻塞

ReentrantLock 、 Synchronized 都是可重入锁

## 6、死锁及排查

死锁 [GitHub案例](https://github.com/YuncenLiu/code-example/blob/master/thread/src/main/java/com/liuyuncen/juc/locks/MyDeadlockDemo.java)
jps 找到Java进程， jstack 进程ID
1. 结尾出现 Found 1 deallock 
2. waiting to lock 等待lock

jconsole 在线程菜单有检测死锁按钮

