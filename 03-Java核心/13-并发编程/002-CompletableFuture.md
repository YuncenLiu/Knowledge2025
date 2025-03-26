---
createTime: 2023-10-30
resource: https://github.com/YuncenLiu/code-example/tree/master/thread/src/main/java/com/liuyuncen/juc/cf1
File: OneDrive-个人/附件/KnowledgeFile/03-Java核心/13-并发编程/拉钩-并发编程-2021-09-30.pdf
File2: OneDrive-个人/附件/KnowledgeFile/03-Java核心/13-并发编程/B站阳神-JUC-2024-11-12.xmind
---
[toc]

Future接口定义了操作异步任务的一些方法，如==获取异步任务执行结果==、==取消任务的执行==、==判断任务是否被取消==、==判断任务执行是否完毕==等

## 1、Futrue 接口能干什么？

JDK1.5 开始，提供异步并行计算功能
三个特点：多线程、异步任务、有返回值
+ `get()` 阻塞获取子线程结果
+ `get(3, Time)` 只等待固定时间，超时异常
+ `isDone()` 判断任务是否执行完成

优点：future + 线程池异步多线程配合，显著提高执行i效率
缺点：get 阻塞、isDone 轮询
场景：对简单业务 Future 完全OK，回调通知

## 2、CompletableFuture


关系依赖 Future、CompletionStage 一个异步计算过程中的某一个阶段，一个阶段完成后可能触发另一个阶段
静态方法：
+ runAsync 无返回值 [GitHub案例](https://github.com/YuncenLiu/code-example/blob/master/thread/src/main/java/com/liuyuncen/juc/cf2/CompletableFutureBuildDemo.java)
	+ `public static CompletableFuture<Void> runAsync(Runnable runnable)`
	+ `public static CompletableFuture<Void> runAsync(Runnable runnable, Executor executor)`
+ supplyAsync 有返回值 [GitHub案例](https://github.com/YuncenLiu/code-example/blob/master/thread/src/main/java/com/liuyuncen/juc/cf2/CompletableFutureBuildDemo.java)
	+ `public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier)`
	+ `public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier, Executor executor)`
+ whenComplete 当子线程完成，主动回调 [GitHub案例](https://github.com/YuncenLiu/code-example/blob/master/thread/src/main/java/com/liuyuncen/juc/cf1/CompletableFutureUseDemo.java)
	+ `.whenComplete((v,e) -> {})` 
		+ v 上一步返回值
		+ e 上一步异常
+ exceptionally 异常时主动调用 [GitHub案例](https://github.com/YuncenLiu/code-example/blob/master/thread/src/main/java/com/liuyuncen/juc/cf1/CompletableFutureUseDemo.java)
	+ `.exceptionally (e->{})`
+ getNow("xxx")

优点：
1. 异步任务结束时，会自动回调某个对象方法
2. 主线程设置好回调，不用关心异步任务的执行，异步任务之间可以顺序执行
3. 异步任务出错时，会主动回调某个对象的方法

### 2.1、常用方法

#### 2.1.1、获取结果和触发计算

获取结果
+ get() 阻断获取结果
```java
CompletableFuture<String> returnFuture = CompletableFuture.supplyAsync(() -> {  
	System.out.println(Thread.currentThread().getName());  
	try { TimeUnit.MILLISECONDS.sleep(1000); } catch (InterruptedException e) {e.printStackTrace();}  
	return "hello supplyAsync";  
});  
System.out.println(returnFuture.get());
```
+ get(long timeout,TimeUnit unit) 指定时间，超时抛异常
+ join() 和get一样，但是编译不会警告，报错时，该抛就抛
+ getNow(T valuefAbsent) 获取子现场结果，如果还拿不到，直接返回参数值

主动触发计算
+ complete(T value)

#### 2.1.2、对计算结果进行处理

+ thenApply(f->{}) 一个参数往后传递
+ handle(f,e->{}) 可以带着异常往后走
+ excptionally 相当于 try catch
+ whenComplete、handle 相当于 try finally

```java
ExecutorService threadPool = Executors.newFixedThreadPool(3);  
CompletableFuture<Integer> completableFuture = CompletableFuture.supplyAsync(() -> {  
    try {  
        TimeUnit.SECONDS.sleep(1);  
    } catch (InterruptedException e) {  
        throw new RuntimeException(e);  
    }  
    System.out.println("----- 111");  
    return 1;  
}, threadPool).handle((v, e) -> {  
    System.out.println("----- 222");  
    // int i = 10 / 0;  
    return v + 2;  
}).handle((v, e) -> {  
    System.out.println("----- 333");  
    return v + 3;  
}).thenApply(v -> {  
    System.out.println("----- 444");  
    return v + 4;  
}).whenComplete((v, e) -> {  
    if (null == e) {  
        System.out.println("--- 计算完成, " + v);  
    }  
}).exceptionally(e -> {  
    e.printStackTrace();  
    System.out.println("--- 异常情况: " + e.getCause() + " ---- " + e.getMessage());  
    return null;  
});  
System.out.println(Thread.currentThread().getName() + " ---- 主线程忙其他的事情去了 ");  
TimeUnit.SECONDS.sleep(5);  
threadPool.shutdown();
```


#### 2.1.3、对计算结果进行消费

接收任务的处理结果，并消费处理，无返回结果

+ thenAccept(r-> sout) 
	+ .thenAccept(System.out::println)
+ thenRun(Runnable) A执行完再执行B，B不需要A的结果
+ thenAccept(Consumer) A执行完再执行B，B需要A的结果，但是B无返回值
+ thenApply(Function) A执行完再执行B，B需要A的结果，同时B有返回值


1.没有传入自定义线程池，默认使用 ForkJoinPool
2.传入自定义线程池，执行第一个任务传入了
	1.调用 thenRun执行第二个时，第二个和第一个共同使用同一
	2.调用thenRunAsync执行第二个，第一个使用自己线程池，第
3.也有可能因为处理的太快了，系统优化切换原则，直接使用main

```java
// 各玩各的，没有任何关系
System.out.println(CompletableFuture.supplyAsync(() -> "resultA").thenRun(() -> {System.out.println("没有任何关系");}).join());
System.out.println(CompletableFuture.supplyAsync(() -> "resultA").thenAccept(r -> System.out.println(r)).join());
System.out.println(CompletableFuture.supplyAsync(() -> "resultA").thenApply(r -> r + "===").join());
System.out.println(CompletableFuture.supplyAsync(() -> "resultA").thenRun(() -> {
    System.out.println("没有任何关系");
}).join());
```


#### 2.1.4、对计算速度进行选用

[GitHub 案例](https://github.com/YuncenLiu/code-example/blob/master/thread/src/main/java/com/liuyuncen/juc/cf1/CompletableFutureFastDemo.java)

+ A.applyToEither(B ,f->{})

```java
CompletableFuture<String> playA = CompletableFuture.supplyAsync(() -> {  
    System.out.println("A Come in");  
    try {TimeUnit.SECONDS.sleep(2);} catch (InterruptedException e) {e.printStackTrace();}  
    return "playA";  
});  
  
CompletableFuture<String> playB = CompletableFuture.supplyAsync(() -> {  
    System.out.println("B Come in");  
    try {TimeUnit.SECONDS.sleep(3);} catch (InterruptedException e) {e.printStackTrace();}  
    return "playB";  
});  
  
CompletableFuture<String> result = playA.applyToEither(playB, f -> {  
    return f + " is winer";  
});  
System.out.println(Thread.currentThread().getName() + "\n ------- " + result.join());
```

A/B 谁快完成，f 获取谁的返回值

#### 2.1.5、对计算结果进行合并

两个 CompletionStage任务都完成了，一起交给 thenCombine处理

先完成的，先等着，等待其他分支任务完成

```java
CompletableFuture<Integer> task1 = CompletableFuture.supplyAsync(() -> {  
    System.out.println(Thread.currentThread().getName() + "\t ---- 启动");  
    try {  
        TimeUnit.SECONDS.sleep(1);  
    } catch (InterruptedException e) {  
        e.printStackTrace();  
    }  
    System.out.println("task1 over");  
    return 10;  
});  
CompletableFuture<Integer> task2 = CompletableFuture.supplyAsync(() -> {  
    System.out.println(Thread.currentThread().getName() + "\t ---- 启动");  
    try {  
        TimeUnit.SECONDS.sleep(3);  
    } catch (InterruptedException e) {  
        e.printStackTrace();  
    }  
    System.out.println("task2 over");  
    return 20;  
});  
Integer result = task1.thenCombine(task2, (r1, r2) -> {  
    return r1 + r2;  
}).join();  
System.out.println("result = " + result);  
System.out.println("-----------------------------------------------");  
CompletableFuture<Integer> task3 = CompletableFuture.supplyAsync(() -> {  
    System.out.println(Thread.currentThread().getName() + "\t ---- 1启动");  
    return 10;  
}).thenCombine(CompletableFuture.supplyAsync(() -> {  
    System.out.println(Thread.currentThread().getName() + "\t ---- 2启动");  
    return 20;  
}), (x, y) -> {  
    System.out.println(Thread.currentThread().getName() + "\t ---- 3启动");  
    return x + y;  
}).thenCombine(CompletableFuture.supplyAsync(() -> {  
    System.out.println(Thread.currentThread().getName() + "\t ---- 4启动");  
    return 30;  
}), (x, y) -> {  
    System.out.println(Thread.currentThread().getName() + "\t ---- 5启动");  
    return x + y;  
});  
System.out.println("task3 = " + task3.join());  
System.out.println("-----------------------------------------------");
```


### 2.2、内部原理，基于 ForkJoinPool

简单解释：在成员变量里，发现线程池是使用 ForkJoinPool 构建的

java.util.concurrent.CompletableFuture.class

```java
public static <U> CompletableFuture<U> supplyAsync(Supplier<U> supplier) {  
    return asyncSupplyStage(asyncPool, supplier);  
}

static <U> CompletableFuture<U> asyncSupplyStage(Executor e,  
                                                 Supplier<U> f) {  
    if (f == null) throw new NullPointerException();  
    CompletableFuture<U> d = new CompletableFuture<U>();  
    e.execute(new AsyncSupply<U>(d, f));  
    return d;  
}
```

通过上⾯的代码可以看到，asyncPool是⼀个static类型，supplierAsync、asyncSupplyStage也都是static⽅法。
Static⽅法会返回⼀个CompletableFuture类型对象，之后就可以链式调⽤，CompletionStage⾥⾯的各个⽅法。
ForkJoinPool接受的任务是ForkJoinTask 类型，⽽我们向CompletableFuture提交的任务
Runnable/Supplier/Consumer/Function 。因此，肯定需要⼀个适配机制，把这四种类型的任务转换成 ForkJoinTask，然后提交给ForkJoinPool，如下图所示：

![](images/Pasted%20image%2020250227141259.png)

为了完成这种转换，在CompletableFuture内部定义了⼀系列的内部类，下图是CompletableFuture的各种内部类的继承体系。
在 supplyAsync(...)⽅法内部，会把⼀个 Supplier 转换成⼀个 AsyncSupply，然后提交给ForkJoinPool执⾏；
在runAsync(...)⽅法内部，会把⼀个Runnable转换成⼀个AsyncRun，然后提交给ForkJoinPool执⾏；
在 thenRun/thenAccept/thenApply 内部，会分别把 Runnable/Consumer/Function 转换成 UniRun/UniAccept/UniApply对象，然后提交给ForkJoinPool执⾏；
除此之外，还有两种 CompletableFuture 组合的情况，分为“与”和“或”，所以有对应的Bi和Or类型的Completion类型。
![](images/Pasted%20image%2020250227141427.png)

![](images/Pasted%20image%2020250227141634.png)

