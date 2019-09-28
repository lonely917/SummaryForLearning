## 一个问题清单
1. 线程和进程
2. 并行和并发
3. 线程或者进程被调度的规则-占用和放弃CPU的规则
4. 尝试比较不同语言所支持的线程、进程模型
5. java锁的概念与jvm的支持原理、进一步和宿主操作系统的关系。
6. java thread.sleep会暂时放弃cpu，那么sleep的实现以及精度的控制？
7. 各种高级锁的封装和实现原理
8. java thread到future的演变
9. Thread.stop为何被弃用?Thread.interrupt后线程的状态
10. Java线程的状态
11. 父子线程与thread.join实现原理
12. 守护线程thread.setDeamon
13. 针对线程未捕获异常的默认异常处理机制(类方法setDefaultUncaughtExceptionHandler & 实例方法setUncaughtExceptionHandler)
14. ShutdownHook-Runtime.getRuntime().addShutdownHook
15. synchronized机理和模型
16. volatile原理
17. Java中的原子操作(long double的读和写不是)
18. 线程安全的单例模式(n中写法比较)
19. 双重检测机制+volatile变量的单例模式(volatile的必要性，否则单纯的双重检测未必线程安全，由于指令重排的缘故可能得到一个没有构建完全的实单例对象)
20. 同步涉及的一些概念：deadlock、livelock、fairlock、reenterlock、optimisticlock、readwritelock、wait、notify、notifyAll
21. ThreadLocal实现原理(threadlocal、threadlocalmap、弱引用、垃圾回收)
22. 获取锁失败的线程的行为?直接放弃cpu or busywaiting后再放弃cpu。再次被调用后又会尝试加锁？
23. 锁竞争(lock contention)的优化和避免。(缩小加锁范围reduce lock cope;减小锁的粒度 lock splitting)
24. lock Striping(增加并发性)
25. ConcurrentHashMap
26. CAS操作-AtomicXX类
27. 对象池的代价评估-(节省创建对象开销与增加的同步控制开销权衡)
28. Executor、ExecutorService、Call
29. 线程同步操作(串行等待)与异步操作(回调触发)
30. Runnable & Callable(后者线程执行完毕后带有返回值)
31. Collections.SynchronizedXX方法获得一个集合的同步封装。
32. Semaphore
33. producer & consumer的实现(只用synchronized和waitnotify/1.5之后java.util.concurrent)
34. 语言的发展几个维度：代码能够更简洁(新的实现和封装、新的框架)；代码更规范更安全(类型检查、错误提前发现)；
35. ForkJoin框架：ForkJoinPool-ForJoinTask...
36. CountDownLatch & CyclicBarrier
37. RecusiveTask & RecusiveAction

## JLS之java process and threads

https://docs.oracle.com/javase/tutorial/essential/concurrency/procthread.html

### synchronized
对于语句，synchronized对一个对象进行加锁和解锁，加锁成功后才执行具体行为，行为结束(包括异常退出)后则自动解锁。

对于函数，synchronized对被调用对象(同步的实例方法)或者被调用类class(同步的静态方法)进行加锁，函数结束(包括退出)后自动解锁。

其他的一些同步相关策略：volatile变量读写、java.util.concurrent包的使用。

加锁指的是加锁对象对应的monitor，java中每个object都有一个对应的monitor。

### wait
object.wait肯定发生在对object相关的monitor加锁的情况下(比如对其进行了synchronized操作)。wait行为如下：
1. 如果thread没有进行过object相关的lock操作，则wait操作会发生异常
2. 如果带参wait参数不合法，也会报异常

不报异常的情况下发生如下事情：
1. thread会被加入到object的wait-set中(一个阻塞在object上的线程集)，然后进行锁释放(lock了多少次，就unlock多少次)。
2. thread等待至到其从object的wait-set中移除。
    2中所属的移除会发生在下述情况下：
    - notify被执行，然后该thread被选中从wait-set中移除
    - notifyAll被执行
    - thread被interrupt
    - time-wait超时
3. thread重新进行加锁(之前释放了多少次，就要重新加锁多少次)
4. 如果2中等待的时候，thread被interrupt的话，不会走3重新加锁，会在wait方法抛出异常。

### notification
object.nofity肯定发生在对object相关的monitor加锁的情况下，比如常见的notify之前应该有synchronized相关的操作语句。notify行为如下：
1. 如果当前thread没有对object进行加锁，notify抛出异常
2. 如果当前thread对object加锁次数大于0，则nofity会在object的wait-set中选择一个thread移除。（注意这里选择的策略并不明确，可能是任意一个，对应的thread会从wait返回，然后进行加锁操作，但是加锁操作要想成功得等到notify的发起方完成解锁操作，即nofity后至到执行完退出synchronized语句时锁才得以释放，被唤醒的thread才可能拿到锁。）
3. 如果当前thread对object加锁次数大于0，notifyAll会唤起所有的等待方thread，(即wait-set中所有thread都被移除)，但是一次只能有一个thread重新获得锁。

### sleep and yield
Thread.sleep会使得当前线程进行休眠一段时间(具体时间长度和系统定时器精度有关)，休眠不会使得线程放弃对monitor的拥有权，也就是说sleep不会解锁。

Thread.yield会让出cpu时间片，使得cpu重新调度选择执行线程，因此存在这样一种可能，yield后依然马上又获得cpu时间片。注意，yield和sleep一样不会放弃对锁的控制。

很重要的一点，yield和sleep并没有同步语义，即在这两个方法调用的时候编译器不会强制刷新缓存到内存或者从内存重新加载数据到缓存。理解下面的例子：

```java
//done是一个非volatile的bollean变量
while (!this.done)
    Thread.sleep(1000);
//就编译器的实现而言，可以只读取一次done到缓存，即使其他线程改变了done的值，但是这个线程由于一直使用的缓存值，依然无限循环着不退出。
```
上述例子说明使用这种形式的方法来控制线程结束是不确定的，因为没有确定规则来使得编译器对变量同步进行保证。但如果使用volatile修饰done就有可以达到目的。

### memory model
- reordering
- shared variables(共享变量)
- synchronization order
- program order
- happen-before order
- final field semantics

### word tearing

### long double操作
对于long、double基本类型操作，不是原子操作，最好使用volatile或者同步策略。

对于引用变量本身，无论是32位还是64位虚拟机，都是原子操作。
