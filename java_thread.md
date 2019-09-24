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