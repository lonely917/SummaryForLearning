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