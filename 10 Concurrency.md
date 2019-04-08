# Effective Java笔记九 并发
## 第66条 同步访问共享的可变数据
关键字`synchronized`可以保证同一时刻只有一个线程可以执行某一个方法或者某一个代码块.

如果把同步的概念仅仅理解为一种互斥的方式, 虽然正确, 但并没有说明同步的全部意义.

如果没有同步, 一个线程的变化就不能被其他线程看到. 同步不仅可以阻止一个线程看到对象处于不一致的状态中, 它还可以保证进入同步方法或者同步代码块的每个线程, 都看到由同一个锁保护的之前所有的修改效果.

虽然语言规范保证了线程在读取原子数据的时候, 不会看到任意的数值(读写变量是原子的), 但是它并不保证一个线程写入的值对于另一个线程将是可见的. 为了在线程之间进行可靠的通信, 也为了互斥访问, 同步是必要的. -> 归因于内存模型, 规定线程所做的变化何时以及如何对其他线程可见.

如果读和写操作没有都被同步, 同步就不会起作用.

`volatile`修饰符不执行互斥访问, 但它可以保证任何一个线程在读取该域的时候都将看到最近刚刚被写入的值. -> 用在只需要通信而不需要互斥的场合.

增量操作符`++`不是原子的: 先读, 后写.
多个线程可能会看到同一个值.

避免本条目中所讨论到的问题的最佳办法是: 不共享可变的数据. -> 要么不可变, 要么不共享. -> 将可变数据限制在单个线程中.

让一个线程在短时间内修改一个数据对象, 然后与其他线程共享, 这是可以接受的, 只同步共享对象引用的动作. 这种对象称为**事实上不可变的(effectively immutable)**. 将这种对象引用从一个线程传递到其他线程被称作**安全发布(safe publication)**.

## 第67条 避免过度同步
不要在同步代码块中调用外来方法, 可能会造成并发修改异常或者死锁.

将外来的方法调用移出同步的代码块:
* 拷贝数据结构.
* 使用concurrent collection, `CopyOnWriteArrayList`.

通常, 应该在同步区域内做尽可能少的工作.

## 第68条 executor和task优先于线程
Java 1.5中加入了`java.util.concurrent`, 很好用:
```
ExecutorService executorService = Executors.newSingleThreadExecutor();

// 提交runnable
executorService.execute(new Runnable() {
@Override
public void run() {

}
});

// 终止
executorService.shutdown();
```

很多有用的方法, 比如等待优雅地完成终止
```
executorService.awaitTermination(30, TimeUnit.SECONDS);
```

还可以创建固定或可变数目的线程池.
```
Executors.newFixedThreadPool(3);
```
小程序或者轻载的服务器, `Executors.newCachedThreadPool()`是个不错的选择, 但是对于大负载的服务器来说, 最好使用固定数目的线程池.

还可以直接使用`ThreadPoolExecutor`类, 控制线程池操作.

`ScheduledThreadPoolExecutor`可以代替`java.util.Timer`, 优势: 时间更准确, 可以从异常恢复.

## 第69条 并发工具优先于wait和notify
concurrent中工具分为三类:
* Executor Framework.
* 并发集合. -> 为标准集合接口提供了高性能的并发实现.
* 同步器(Synchronizer). -> 常用: `CountDownLatch`, `Semaphore`.

应该优先使用并发集合, 而不是使用外部同步的集合.

大多数`ExecutorService`实现都使用了`BlockingQueue`.

线程饥饿死锁(thread starvation deadlock).

`System.nanoTime()`比`System.currentTimeMillis()`更加准确和精确.


没有理由在新代码中使用`wait`和`notify`, 即使有也是极少的. 如果你在维护使用`wait`和`notify`的代码, 务必确保始终是利用标准的模式从while循环内部调用`wait`.

使用`wait`的标准模式:
```
// The standard idiom for using the wait method
synchronized (obj) {
while (<condition does not hold>) 
obj.wait();  // (Releases lock, and reacquires on wakeup) 

... // Perform action appropriate to condition
}
```

一般情况下, 优先使用`notifyAll`而不是`notify`, 如果使用`notify`请一定要小心, 以确保程序的活性.

## 第70条 线程安全性的文档化
线程安全性的常见级别:
* 不可变的(immutable). -> `String`, `Long`, `BigInteger`.
* 无条件的线程安全(unconditionally thread-safe). -> 这个类的实例是可变的, 但是这个类有着足够的内部同步. 所以它的实例可以被并发使用, 无需任何外部同步. -> `Random`, `ConcurrentHashMap`.
* 有条件的线程安全(conditionally thread-safe). -> 有些方法需要外部同步. -> `Collections.synchronized`包装返回的集合.
* 非线程安全(not thread-safe). -> 并发使用需要客户自己在外部处理同步. -> 通用的集合比如`ArrayList`, `HashMap`.
* 线程对立的(thread-hostile). -> 这个类不能安全地被多个线程并发使用, 即使所有的方法调用都被外部同步包围.

每个类都应该在文档中说明它的线程安全属性. 有条件的线程安全必须在文档中指明"哪个方法调用序列需要外部同步, 以及在执行这些序列的时候要获得哪把锁".

无条件的线程安全类, 应该考虑使用私有锁对象来代替同步的方法 -> 防止客户端程序和子类的不同步干扰.

## 第71条 慎用延迟初始化
延迟初始化(lazy initialization): 需要域的值时才将它初始化.

延迟初始化降低了初始化类或者创建实例的开销, 却增加了访问被延迟初始化的域的开销.

当有多个线程共享一个延迟初始化的域, 采用某种形式的同步是很重要的.

大多数的域应该正常地进行初始化, 而不是延迟初始化. 如果为了达到性能目标, 或者为了破坏有害的初始化循环, 而必须延迟初始化一个域, 就可以使用相应的延迟初始化方法:
* 对于实例域, 使用双重检查模式.
* 对于静态域, 使用lazy initialization holder class模式.
* 对于可以接受重复初始化的实例域, 也可以考虑使用单重检查模式.


## 第72条 不要依赖于线程调度器
线程调度器(thread scheduler)决定哪些线程将会运行, 以及运行多长时间. 编写良好的程序不应该依赖于这种策略的细节.

要编写健壮的, 响应良好的, 可移植的多线程应用程序, 最好的办法是确保可运行线程的平均数量不明显多于处理器的数量.

不要依赖`Thread.yield`或者线程优先级, 这些设施仅仅对调度器作些暗示. 

线程优先级可以用来提高一个已经能够正常工作的程序的服务质量, 但永远不应该用来"修正"一个原本不能工作的程序.

## 第73条 避免使用线程组
线程组(thread group)的初衷是作为一种隔离小程序(applet)的机制, 出于安全的考虑, 但是它们从来没有真正履行这个承诺.

总之, 线程组并没有提供太多有用的功能, 而且它们提供的许多功能还都是有缺陷的. 最好把线程组看作是一个不成功的试验, 可以忽略掉它们.
