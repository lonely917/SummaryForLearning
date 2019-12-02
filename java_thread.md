## 一个问题清单
1. 线程和进程
2. 并行和并发
3. 线程或者进程被调度的规则-占用和放弃CPU的规则
4. 尝试比较不同语言所支持的线程、进程模型
5. java锁的概念与jvm的支持原理、进一步和宿主操作系统的关系。
6. java thread.sleep会暂时放弃cpu，那么sleep的实现以及精度的控制？
7. 各种高级锁的封装和实现原理
8. java thread到future/futuretask的演变
9. Thread.stop为何被弃用?Thread.interrupt后线程的状态
10. Java线程的状态
11. 父子线程与thread.join实现原理
12. 守护线程thread.setDeamon的作用
13. 针对线程未捕获异常的默认异常处理机制(类方法setDefaultUncaughtExceptionHandler & 实例方法setUncaughtExceptionHandler)
14. ShutdownHook-Runtime.getRuntime().addShutdownHook
15. synchronized机理和模型
16. volatile原理
17. Java中的原子操作(long double的读和写不是原子操作，可以结合volatile来实现)
18. 线程安全的单例模式(n种写法比较)
19. 双重检测机制+volatile变量的单例模式(volatile的必要性，否则单纯的双重检测未必线程安全，由于指令重排的缘故可能得到一个没有构建完全的单例对象)
20. 同步涉及的一些锁概念：deadlock、livelock、fairlock/nonfairlock、reenterlock、optimisticlock、readwritelock、Object(wait、notify、notifyAll)、condition(await、signal
signalAll)
21. ThreadLocal实现原理(threadlocal、threadlocalmap、弱引用、垃圾回收)
22. 获取锁失败的线程的行为?(直接放弃cpu or busywaiting后再放弃cpu。可重入锁概念)
23. 锁竞争(lock contention)的优化和避免。(缩小加锁范围reduce lock cope;减小锁的粒度 lock splitting)
24. Lock Stripping(增加并发性)
25. ConcurrentHashMap(作为切人点研究并发集合)
26. CAS操作 & AtomicXX类
27. 对象池的代价评估-(节省创建对象开销与增加的同步控制开销权衡)
28. Executor、ExecutorService、Callable
29. 线程同步操作(串行等待)与异步操作(回调触发)
30. Runnable & Callable(后者线程执行完毕后带有返回值)
31. Collections.SynchronizedXX方法获得一个集合的同步封装。
32. Semaphore(accqurie & release)
33. producer & consumer模型的实现(只用synchronized和waitnotify;1.5之后java.util.concurrent包中高级结同步类实现)
1. 尝试使用synchronized以及wait-notify实现基本的生产者消费者问题。
1. SynchronousQueue implements BlockingQueue，一种阻塞队列的高级实现，可以帮助更容易地实现生产者消费者问题。缓存队列大小为0。注意阻塞和非阻塞的操作。
34. ForkJoin框架：ForkJoinPool-ForJoinTask...
35. CountDownLatch & CyclicBarrier
36. RecusiveTask & RecusiveAction
37. 语言的发展几个维度：代码能够更简洁(新的实现和封装、新的框架)；代码更规范更安全(类型检查、错误提前发现)

## Java Tutorial之concurrency

https://docs.oracle.com/javase/tutorial/essential/concurrency/index.html

### Thread和Process

1. 计算机系统中会有很多的进程和线程，对于单一处理器架构，同一时间只能有一个进程或者只能有一个线程占据cpu。时间片的概念(time slicing)。

2. Process-进程，是一个自包含的执行环境(self-contained)，通常会有一个独立私有的运行时资源，有独立的内存空间。一般我们所说的程序、应用就是类似的概念。但是一个应用或者程序可能会含有多个进程。进程间通信即IPC有不同的途径，比如管道(pipe)和套接字(socket)，IPC也可能发生在不同的系统之间。

3. 多数jvm的实现都是单进程的。 ProcessBuilder可以被用来创建新的java进程。

4. Thread-线程，也叫轻量级的进程(LWP-light weight process)。每个进程至少有一个线程，创建一个线程需要的开销更小，因为线程会共享进程的部分资源，比如内存和打开的文件。这种设计是为了更高效，但是也带来了一定的复杂性。

5. 多线程是java平台的一个很重要的特性，每个java应用至少有一个线程，或者说至少有若干个线程(因为有一些必要的系统线程来进行内存管理以及信号处理)。从编程者角度，至少要有一个线程，也就是主线程，主线程可以开启另外的自定义的线程。注意，c和c++的早期自身不支持多线程操作，需要通过第三方库进行系统调用创建线程(比如linux下posix线程库的pthread_create操作)，c++11添加了Thread类，支持线程操作。

6. java中操作线程的基本方法使用Thread类，更高级的操作比如Executors(high-level concurrency objects)。 

7. Thread的实现以及使用。法1通过自定义runnable对象构造Thread对象；法2-自定义Thread类重写run方法，使用自定义类构造线程对象。

8. 关于线程的interrupt方法的说明。Thread.interrupted()方法会检测标志位，返回线程是否被已经被中断的结果。interrupted方法会清空标志位。对比地，另一个实例方法，thread.isInterrupted则只是返回检测结果，不会清空标志位。后者可以从其他线程发起检测(实例方法)，前者(静态方法)只能在当前线程对自身发起检测。线程的许多操作比如sleep、join等都可能抛出InterruptedException异常，即发起调用的线程在sleep或者join等待的过程中被另外的线程中断的话(比如通过thread.interrupt实例方法发起中断调用)，sleep或者join操作后处于等待状态的线程会抛出的异常。根据`默认的约定`，抛出异常之前会清空标志位，也就是说sleep/join等已经对中断异常进行了处理，遇到中断则会清空标志位并进行操作的退出和异常的抛出，交由外部调用者进行异常处理(sleep以及join的底层实现应该是遵循前文`默认的约定`)。关于Thread的类方法isInterrupted和thread对象的实例方法interrupted对标志位清空的情况，可以看源码，最终都是调用当前线程实例的isInterrupted(true/false)本地方法，通过传参来标识是否清空标志位。

9. 对于没有调用可能抛出中断异常方法的线程，如果存在一些耗时或者循环性的操作，则需要进行定期主动判断线程是否被中断，进行相应的处理(即发现标志位被设置后也就是进行Thread.interrupted()操作后发现返回true，应进行退出线程或者抛出中断异常的操作，sleep和join的底层实现应该就是这样的)。

10. Thread的一些实例方法来判断线程的状态(是否存活或者被中断，存活的意思是没有挂掉)：thread.isAlive，thread.isInterrupted。

11. java线程状态：new生成线程，调用start方法后线程进行runnable状态(实际上start之后应该是有个ready的状态，然后当线程获取时间片被cpu调度后才真正开始执行，这里将就绪和运行两个状态合二为一)，如果有sleep或者join操作，则线程会放弃cpu进入timed-waiting状态或者waiting状态，如果是遇到加锁操作(指synchronized或者lock对象上锁)暂时不能获得锁，则会处于blocked状态，等待加锁成功进入runnable状态开始执行临界区代码，如果有object.wait操作(一定结合synchronized关键字使用)，则会放弃锁，并进入waiting或者timed-waiting状态，等待其他线程notify操作的唤醒，如果其他线程进行了相关object的notify操作，则wait操作会被唤醒，并尝试获取之前放弃的锁并恢复状态，成功上锁并恢复状态之后开始执行位于wait操作后续的代码。注意yield操作是尝试放弃cpu控制权，但线程可能继续被调度，sleep会使线程放弃cpu控制权，进入timed-waiting但是不会放弃持有的任何锁，wait会放弃线程持有的锁对象。`blocked状态的线程和cpu控制权的关系，blocked的线程可以是忙等类似自旋操作或者阻塞等待唤醒，或者先自旋尝试一段时间不成功的话再阻塞等待唤醒即从轻量级锁切换为重量级锁，具体跟底层实现机制有关`。除此外还有一个终结状态terminated，线程对应方法执行完，线程终结。(建议参考thread类源码，有对状态的详细说明)

12. 通过join实现父子线程的同步，关于join的实现，java层会调用wait等待，wait外的循环会使用isAlive来判断子线程的状态，如果子线程结束，则循环判断条件为false就结束循环，join方法也就走到头返回了。首先join方法是一个同步方法，会对子线程对象本身加锁，然后wait会等待其他线程发来的对该线程对象的notify操作，这个notify实际是子线程发来的，`当子线程结束的时候，会调用notifyAll操作`，这个调用发生在c++层，线程start0本地方法中最后会有一个ensureJoin的方法，在线程结束前发出notifyAll。于是子线程结束，join内部的wait就会被唤醒，同时外部的while循环条件也被打破，join方法就顺利执行完毕，然后返回。

### Synchronization

1. 线程通信主要通过共享变量(字段或者对象的引用)，但也会带来问题：线程干扰和内存一致性错误，使用同步(synchronization)来解决这个问题。

2. 并发(concurrency),可以是多处理器同时执行多个任务，也可以是单处理器一段时间内执行多个任务，java支持并发编程，1.5之后提供了高层次的API接口(在java.util.concurrent包中)。另一个概念，并行，则是绝对意义上的同时执行，类似前文所述的并发中的第一种情况。

3. ThreadInterference(线程干扰概念)，多个线程同时对一个对象的某个字段操作(多处理器可能是同时，单处理器则是微小时间段内操作序列重叠交错执行)，可能会造成与预计不相符合的结果，主要是因为操作的非原子性，多个操作的原子序列会交叠，进而产生干扰错误。可以通过锁机制来解决。

4. Memory consistency errors(内存一致性问题)，不同的线程对本应一致的数据却具有不同的视角导致数据差异。可以理解为线程局部缓存和共享的内存区域数据不一致的问题，或者说线程工作内存和主内存不一致问题。主要是操作可见性的问题。通过`HappenBefore`规则来约束。

5. `同步方法`，会对所属对象进行加锁(如果是静态方法则会对对应的class对象加锁)。有两个作用：其一是临界区的概念，只能有一个线程调用被加锁的方法(或者是该对象被加锁的其他方法)，其二是退出临界区时锁释放对后续加锁操作的HappenBefore规则，也就是`加锁过程中的操作，对后续其他线程再加锁后操作是可见的`。实际上是通过HB规则对编译器或者虚拟机行为进行了约束，来保证并发的正确性。

6. 构造函数不应使用同步，因为构造过程中该对象只能对创建它的那个线程是可见的，因此同步是无意义的。注意在构造函数中应避免泄露对象，使得存在一种可能，即外部可以获得没有构造完全的对象(构造函数泄露)，因为这会引发线程安全问题。

7. 同步可以解决`线程干扰`和`内存不一致`错误。如果一个对象对多个线程可见，多个线程对其的读写操作应该进行同步,即要使用synchronized的读写方法。一种例外，如果操作字段是final类型，则只要在对象创建完毕后，就可以进行非同步地安全地读。

8. 除了同步方法外还有`同步语句或同步代码块`，同步语句除了使用this还可以进行自定义的加锁对象，因此提供了更丰富地粒度控制。

9. synchronized加锁是一种`可重入锁`(reentrant lock)，这使得同步方法可以调用同一个锁对象关联的其他的同步方法，而不需要进行额外的控制。

10. 原子操作(Atomic Access),对于基本类型(除了long和double)以及对象的引用类型的读写操作是原子操作；对于volatile类型的变量的`读写操作`是原子操作(包含volatile的long和double)。对volatile变量的原子操作会比通过同步方法对变量操作更为高效，但是并不能完全避免内存不一致问题,需要一些额外的工作量,主要是因为变量的操作一般会涉及多个连续的原子操作，整个操作序列的原子性不能通过简单的volatile来保证。

11. `CAS的概念`(线程安全策略除了上锁之外，增加并发性的另一种思路，涉及ABA问题以及相应解决方案)

12. 线程内存相关概念(同步实现需要考虑内存模型)：主内存和工作内存；原子性、可见性、有序性。


### Liveness
1. Liveness线程活跃度(Deadlock, Starvation,Livelock等概念)，其中死锁是多个线程彼此持有对方需要的资源，都在等待获取到对应的锁，均处于blocked的状态；饥饿是部分线程一直在等待获取对应锁，但是锁不断地被其他线程持有，造成一种长时间等待加锁的状态，或者长时间得不到cpu使用权的一种状态；活锁是线程彼此都进行退让，但却形成双方都无法进一步执行的场景(`走廊让道的例子`)，此时双方都处于waiting(忙等?)而非blocked的状态。`活锁死锁以及线程阻塞的问题?是否都有block的状态以及相互停滞的时候CPU的占用情况`

关于livelock一段解释
```
A thread often acts in response to the action of another thread. If the other thread's action is also a response to the action of another thread, then livelock may result. As with deadlock, livelocked threads are unable to make further progress. However, the threads are not blocked — they are simply too busy responding to each other to resume work. This is comparable to two people attempting to pass each other in a corridor: Alphonse moves to his left to let Gaston pass, while Gaston moves to his right to let Alphonse pass. Seeing that they are still blocking each other, Alphone moves to his right, while Gaston moves to his left. They're still blocking each other, so...
```

### Guarded Blocks
简而言之，就是程序等待条件合适再通过，否则一直放弃cpu处于等待状态，被唤醒也要再次判断是否条件被满足，避免误唤醒。具体场景类似我们生产者消费者中wait使用的场景，“while(条件) wait”这种形式。

关于Guarded block的一段解释
```
Threads often have to coordinate their actions. The most common coordination idiom is the guarded block. Such a block begins by polling a condition that must be true before the block can proceed. There are a number of steps to follow in order to do this correctly.
```
1. wait调用的完善写法应该在一个循环检测中，也就是wait结束被唤醒后仍要对判断条件进行判断以确认是否满足预期条件，因为并不能确定中断退出是否表示对应标志改变以及即使被唤醒了但不能保证判断条件没有被修改过。同时注意有虚假唤醒的可能。

2. wait需要在同步方法或者同步块中执行，因为wait要求已经获取到对象的monitor，否则会抛出异常，因此在同步方法中使用wait比较容易符合这个条件，wait会释放锁然后挂起执行(releases the lock and suspends execution)，线程处于waiting状态，等另外某个线程加锁后并调用notify系列方法的时候，wait的线程收到通知有状态变化，会重新尝试加锁，等nofity的线程退出同步块即释放锁之后，wait的线程重新获得锁，从wait结束，开始后续执行。


### Immutable Objects
1. 不可变对象可以使用在多线程并发中，避免产生数据操作的不一致现象。

2. 不可变类设计规则(https://docs.oracle.com/javase/tutorial/essential/concurrency/imstrat.html)


### High Level Concurrency Objects
1. Lock(java.util.concurrent.locks)。synchronized同步依赖于简单的可重入锁，简单但有一些限制，jdk1.5之后提供了更高级的控制，Lock是一种更高级别的锁接口，提供更灵活的操作，也支持wait/notify机制(通过Condition信号量接口来支持)，内置两个实现类ReentrantLock和ReentrantReadWriteLock。(Doug Lea，java并发相关类的主要作者)

2. Executors，更高级别的线程操作管理,可以看作一个Executor的工厂类，提供一些简单的方法可以生成不同类型的线程池。

3. Executors工具类/newSingleThreadExecutor/newFixedThreadPool/newCachedThreadPool/newScheduledThreadPool最终都会用到new ThreadPoolExecutor的构造，传入的参数有核心线程数、线程池尺寸、非核心线程闲置时间、阻塞队列、线程构造工厂、异常处理等参数。

4. Executor/ExecutorService/ScheduledExecutorService/FinalizableDelegatedExecutorService

5. ThreadPoolExecutor/ScheduledThreadPoolExecutor

6. ExecutorService的shutdown操作。非同步方法。
7. ExecutorService的shutdownNow操作。非同步方法。(使用awaitTermination会阻塞等待直到shutdown调用后所有线程终结,注意不仅仅是等待工作线程结束，整个线程池都要结束。)

8. ExecutorService的submit/execute以及Future的get操作。(Executor接口到ExecutorService接口丰富了接口方法)

9. Fork/Join框架：这个是多核编程相关的，有点类似windows MPI编程，利用多处理器进行划分和归约操作。框架核心是ForkJoinPool(AbstractExecutorService的子类)帮助我们利用多处理器，并有一个work-stealing机制，可以理解为完成任务的线程可以去帮助其他线程解决未完成的任务。`思考一下线程在多核架构下的实现原理。`

10. JDK中F/J框架的应用：java8中Arrays中提供了并行排序算法parallelSort；java8开始java.util.streams包也利用了F/J框架。

11. Concurrent Collections:BlockingQueue\ConcurrentMap\ConcurrentNavigableMap\ConcurrentSkipListMap。注意`All of these collections help avoid Memory Consistency Errors by defining a happens-before relationship between an operation that adds an object to the collection with subsequent operations that access or remove that object.`

12. java.util.concurrent.atomic，相关变量封装是完全线程安全的，而volatile的线程安全是有使用场景的。volatile变量本身的写对读是可见的，但是在多线程中volatile变量的相关操作可能不是原子的，这就导致并发的不安全。volatile只是保证变量的可见以及有序性(指令重排控制)。`atmoic的实现原理?以及CAS在java中的应用`

13. Concurrent Random Numbers

14. concurrent further reading  (https://docs.oracle.com/javase/tutorial/essential/concurrency/further.html)

15. concurrent exercise(https://docs.oracle.com/javase/tutorial/essential/concurrency/QandE/questions.html),其中exercise2 关于happen-before的仔细分析。

16. 一个并发API相关的动画展示（很棒，一个可执行`jar程序`）。 https://sourceforge.net/projects/javaconcurrenta/

17. jdk并发包相关的一个uml模型 https://www.uml-diagrams.org/java-7-concurrent-uml-class-diagram-example.html

### 高级锁相关概念

1. 悲观锁和乐观锁。
2. 加锁机制以及线程切换代价-阻塞or自旋(spin)-自选超时+阻塞。
3. java对象之markword：未锁定、可偏向、轻量、重量、
4. 重量级锁、轻量级锁、可偏向锁；自旋锁。
5. CAS以及ABA问题的探讨。`ABA不一定导致最终结果错误，但是过程可能会有逻辑错误，或者说过程被隐蔽了`
6. 悲观锁实例：synchronized、reentrantlock.(synchronized 也在逐步优化)
7. 缓存行的伪共享问题产生以及性能优化。(主要影响多核场景下处理器之间的缓存同步问题，解决方法都是缓存行填充的原理，具体实现方式可能会有所不同)
8. ABA问题的处理：CAS添加对象标志用来进行操作计数。比如java中AtomicStampedReference以及AtomicMarkableReference是对AtomicReference的ABA完善版。

一些参考
http://ifeve.com/falsesharing/


### concurrent并发包中的锁、信号量、条件变量等

1. Object类最基础的wait、notify结合synchronized关键字
2. Condition和Lock结合使用，lock的lock和unlock以及condition的await和signal/signalAll
3. Semaphore信号量的acquire和release
4. Lock子类ReentrantLock以及ReadWriteLock
5. CountDownLatch的await和countdown，控制一定数量的线程集合。
6. CyclicBarrier循环屏障

### AQS框架(AbstractQueueSynchronizer)

>Provides a framework for implementing blocking locks and related
synchronizers (semaphores, events, etc) that rely on
first-in-first-out (FIFO) wait queues. 

1.5之后加入的类，位于java.util.concurrent.locks包，是条件变量、信号量的实现基础。此前我们通过synchronized和wait/notify来实现同步控制，或者附加数组检查来实现带有条件的等待与同步(比如生产者消费者、读写锁等模型)，1.5后直接实现了一个新的框架，提供了Lock、Condition、Semaphore等高级类，Condition的await和signal以及Semaphore的acquire和release的实现最终都和AQS有关，新框架中类似wait和notify的等待和唤醒操作是由LockSupport类的park和unpark操作来实现，进一步是通过sun.misc.Unsafe类实现。

### 并发集合的实现(非阻塞操作)

1. ConcurrentLinkedQueue (since 1.5)

```
论文https://www.cs.rochester.edu/research/synchronization/pseudocode/queues.html

96年Non-Blocking and Blocking Concurrent Queue Algorithms 论文的一些实验结论：

1.两个锁(head lock 和 tail lock)相对于single lock的情况，在并发较多的情况下(5个processors以上)效果更好。(很容易理解，并发量高才能体现出分锁的意义)

2.非阻塞算法的实现利用CAS需要硬件指令支持(当前多数硬件都支持)，论文中的非阻塞算法相较于传统单锁、文中的双锁、以及此前一些非阻塞算法，在多处理器情况下都表现得更为出色。(`jdk中ConcurrentLinkedQueue的实现也是基于此论文所阐述的原理`)
```

2. ConcurrentHashMap 1.7 vs 1.8实现不同

1.7使用分段锁，1.8使用CAS操作，CAS失败的话使用Synchronized进行加锁，另外1.8后，存储结果发生变化，开链法的链不在是简单的线性结构，链表过长会转成红黑树进行存储。
   
### 并发集合列表(分段锁以及CAS操作)
1. ConcurrentLinkedQueque(since 1.5)
1. ConcurrentHashMap(since 1.5)
1. ConcurrentSkipListSet(since 1.6)
1. ConcurrentSkipListMap(since 1.6)
1. ConcurrentLinkedDeque(since 1.7)

### 同步集合列表(支持阻塞操作)
1. BlockingQueue接口(since 1.5)
2. LinkedBlockingQueue(since 1.5)
3. ArrayBlockingQueue(since 1.5)
3. LinkedBlockingDeque(since 1.6)

### 同步基础相关
1. 同步集合会用到锁(ReentrantLock)和信号量(Condition接口)
2. ReentrantLock中有两个静态内部类FairLock和NonFairLock，都是静态抽象内部类Sync的具体实现类
3. ReentrantLock.Sync继承自抽象类AbstractQueuedSynchronizer，AQS是1.5并发同步的基础
4. ReentrantLock的lock和unLock通过Sync的lock和unLock最终调用AQS的acquire和release方法
5. Condition接口有两个接口实现类，其中一个是AQS的内部类ConditionalObject，Condition的wait和signal方法通过调用ConditionalObject的wait和signal最终调用LockSupport的park和unark方法，最终通过sun.misc.Unsafe类的native方法来实现。

### Executor相关
1. 接口Executor，execute方法接受runnable对象，没有返回值。
2. 接口ExecutorService extends Executor，继承自Executor添加submit方法，可以通过submit传递runnable或者callable对象，返回Future实例，提供了线程执行完返回一个结果的途径(future.get)。
3. 接口ScheduledExecutorService接口，提供了时间调度的方式，延时执行，循环执行等。
4. 接口Callable，不同于Runnable接口中有一个无返回值的run方法，Callable接口中的抽象方法call带有返回值。
5. 抽象类AbstractExecutorService implements ExecutorService，是一个ExecutorService的预实现。
6. 类ThreadPoolExecutor extends AbstractExecutorService，相当于Executor的一个具体实现类。
7. Executors是一个工厂类，提供Executor实例的简单构造，最终都是通过不同的参数构造类ThreadPoolExecutor。
8. ThreadPoolExecutor相关配置：核心线数量，线程池最大线程数量，非核心线程闲置时间，时间单位，待执行任务的阻塞队列。
9. Executors若干方法的解析，提供简单构造，不一定适合项目，具体可以使用ThreadPoolExecutor进行定制化构造。
10. Executors.newSingleThreadExecutor和Executors.newFixedThreadPool(1)类似，最终都调用new ThreadPoolExecutor(1, 1,0L, TimeUnit.MILLISECONDS,new LinkedBlockingQueue<Runnable>())这个方法，创建的线程池情况：核心线程数量1，线程池大小也为1，非核心线程超时时间为0，任务队列长度无限，由于任务队列长度没有限制(Integer.Max)，故不会创建非核心线程，线程池大小和非核心线程超时时间这两个参数就没什么作用了。简单概括就是提交任务后，只会有一个核心线程工作，同时新任务到来就会放入队列中，待核心线程工作完继续从任务队列里取任务执行。由于队列没有边界，可以一直添加任务，可能会造成OOM异常。两种形式创建的线程池还是有差别的，前者会进行一个FinalizableDelegatedExecutorService的包装，在垃圾回收方面进行一些特殊操作，对象被清理前会调用shutdown操作,具体实现是对象的finalize方法中调用了super.shutdown方法。

11. Executors.newFixedThreadPool(n)，创建的线程池情况如下：核心线程数为n，任务队列无边界，线程池大小也为n，非核心线程超时时间0min，类似前面的分析，由于任务队列无界，线程池大小和非核心超时时间就没有太大意义。简单概括表现行为：提交任务，如果核心线程数量没有到n(即使已经有核心线程目前处于闲置状态)，则会开启新线程成为核心线程，如果核心线程已满，并且都处于工作状态，则将任务添加到队列中。

12. Executors.newCachedThreadPool()，创建的线程池形况如下：核心线程数0，线程池大小Integer.Max，非核心线程超时时间1min，任务队列为SynchronousQueue，该种类型队列尺寸为0，意味着队列不能缓存任务，工作线程不足就会创建新的线程来执行任务。简单概括表现行为：提交任务，如果没有闲置的线程，则会开启新的非核心线程执行任务，执行完毕后，非核心线程可以保持1min，如果在此期间没有被重复利用，该线程会关闭，如果新任务提交的时候有非核心线程闲置，则会利用闲置线程执行任务，整个过程不会有核心线程出现。注意由于线程池大小没有限制，有可能发生OOM。

### 内存可见性以及共享变量
1. 内存可见性
>关于concurrent包描述-内存可见性部分
https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/package-summary.html#MemoryVisibility

Chapter 17 of the Java Language Specification defines the happens-before relation on memory operations such as reads and writes of shared variables. The results of a write by one thread are guaranteed to be visible to a read by another thread only if the write operation happens-before the read operation. The synchronized and volatile constructs, as well as the Thread.start() and Thread.join() methods, can form happens-before relationships. In particular:
Each action in a thread happens-before every action in that thread that comes later in the program's order.

An unlock (synchronized block or method exit) of a monitor happens-before every subsequent lock (synchronized block or method entry) of that same monitor. And because the happens-before relation is transitive, all actions of a thread prior to unlocking happen-before all actions subsequent to any thread locking that monitor.

A write to a volatile field happens-before every subsequent read of that same field. Writes and reads of volatile fields have similar memory consistency effects as entering and exiting monitors, but do not entail mutual exclusion locking.

A call to start on a thread happens-before any action in the started thread.
All actions in a thread happen-before any other thread successfully returns from a join on that thread.

The methods of all classes in java.util.concurrent and its subpackages extend these guarantees to higher-level synchronization. In particular:
Actions in a thread prior to placing an object into any concurrent collection happen-before actions subsequent to the access or removal of that element from the collection in another thread.
Actions in a thread prior to the submission of a Runnable to an Executor happen-before its execution begins. Similarly for Callables submitted to an ExecutorService.
Actions taken by the asynchronous computation represented by a Future happen-before actions subsequent to the retrieval of the result via Future.get() in another thread.
Actions prior to "releasing" synchronizer methods such as Lock.unlock, Semaphore.release, and CountDownLatch.countDown happen-before actions subsequent to a successful "acquiring" method such as Lock.lock, Semaphore.acquire, Condition.await, and CountDownLatch.await on the same synchronizer object in another thread.
For each pair of threads that successfully exchange objects via an Exchanger, actions prior to the exchange() in each thread happen-before those subsequent to the corresponding exchange() in another thread.
Actions prior to calling CyclicBarrier.await and Phaser.awaitAdvance (as well as its variants) happen-before actions performed by the barrier action, and actions performed by the barrier action happen-before actions subsequent to a successful return from the corresponding await in other threads.


2. 共享变量

>关于共享变量描述 https://docs.oracle.com/javase/specs/jls/se12/html/jls-17.html#jls-17.4.1

Memory that can be shared between threads is called shared memory or heap memory.

All instance fields, static fields, and array elements are stored in heap memory. In this chapter, we use the term variable to refer to both fields and array elements.

Local variables (§14.4), formal method parameters (§8.4.1), and exception handler parameters (§14.20) are never shared between threads and are unaffected by the memory model.

Two accesses to (reads of or writes to) the same variable are said to be conflicting if at least one of the accesses is a write.


## JLS之Threads and Locks
https://docs.oracle.com/javase/specs/jls/se12/html/jls-17.html

### synchronized
对于语句，synchronized对一个对象进行加锁和解锁，加锁成功后才执行具体行为，行为结束(包括异常退出)后则自动解锁。

对于函数，synchronized对被调用对象(同步的实例方法)或者被调用类class(同步的静态方法)进行加锁，函数结束(包括退出)后自动解锁。

其他的一些同步相关策略：volatile变量读写、java.util.concurrent包的使用。

加锁指的是加锁对象对应的monitor，java中每个object都有一个对应的monitor,实际是对象的markword中的对应标志位。

### wait
object.wait肯定发生在对object相关的monitor加锁的情况下(比如对其进行了synchronized操作)。wait行为如下：
1. 如果thread没有进行过object相关的lock操作，则wait操作会发生异常
2. 如果带参wait参数不合法，也会报异常

不报异常的情况下发生如下事情：
1. thread会被加入到object的wait-set中(一个阻塞在object上的线程集)，然后进行锁释放(lock了多少次，就unlock多少次)。
2. thread等待(挂起)直到其从object的wait-set中移除。
    2中所述的移除会发生在下述情况下：
    - notify被执行，然后该thread被选中从wait-set中移除
    - notifyAll被执行
    - thread被interrupt
    - time-wait超时
3. thread重新进行加锁(之前释放了多少次，就要重新加锁多少次)
4. 如果2中等待的时候，thread被interrupt的话，不会走3重新加锁，会在wait方法抛出异常。

### notification
object.nofity肯定发生在对object相关的monitor加锁的情况下，比如常见的notify之前应该有synchronized相关的操作语句。notify行为如下：
1. 如果当前thread没有对object进行加锁，notify抛出异常
2. 如果当前阻塞等待在object上的线程数>0(object的wait-set列表)，则nofity会在object的wait-set中选择一个thread移除。（注意这里选择的策略并不明确，可能是任意一个，对应的thread会从wait返回，然后尝试进行加锁操作，但是加锁操作要想成功得等到notify的发起方完成解锁操作，即nofity后至到执行完退出synchronized语句时锁才得以释放，被唤醒的thread才可能拿到锁。）
3. 如果当前阻塞等待在object上的线程数>0，notifyAll会唤起所有的等待方thread，(即wait-set中所有thread都被移除)，但是一次只可能有一个thread重新获得锁。

### sleep and yield
Thread.sleep会使得当前线程进行休眠一段时间(具体时间长度和系统定时器精度有关)，休眠不会使得线程放弃对monitor的拥有权，也就是说sleep不会解锁。

Thread.yield会让出cpu时间片，使得cpu重新调度选择执行线程，因此存在这样一种可能，yield后依然马上又获得cpu时间片。注意，yield和sleep一样不会放弃对锁的控制。

很重要的一点，`yield和sleep并没有同步语义`，即在这两个方法调用的时候编译器不会强制刷新缓存到内存或者从内存重新加载数据到缓存。理解下面的例子：

```java
//done是一个非volatile的bollean变量
while (!this.done)
    Thread.sleep(1000);
//就编译器的实现而言，可以只读取一次done到缓存
//即使其他线程改变了done的值，但是这个线程由于一直使用的缓存值
//依然无限循环着不退出。
```
上述例子说明使用这种形式的方法来控制线程结束是不确定的，因为没有确定规则来使得编译器对变量同步进行保证。但如果`使用volatile修饰done就有可以达到目的`。

### memory model
- reordering
- shared variables(共享变量)
- synchronization order
- program order
- happen-before order
- final field semantics


总结
1. jmm三个关键点：atomicity、visibility、ordering。
2. 给编程者的同步工具：通过synchronized、volatile、final来向编译器表明同步控制的语义。
3. jmm主要是描述同步的框架和策略，一方面编译器的实现应遵循这个模型，另一方面编程者根据这个模型实现同步策略。
4. 编译器指令重排是一种程序优化，jmm允许指令重排，但设定了一些规则保持局部的顺序。
5. volatile关键字解决DCL(double check locking)问题，是基于JVM1.5及以后才有效的。新的JMM保证volatile应用对象可见一定是在对应构造函数完全执行完毕，而在1.5之前，可能由于指令重排的缘故得到一个部分初始化的对象。`思考下DCL中volatile的真正作用，不应是什么原子操作以及读写可见，而是确保对象构造完全。`
6. 一个对象的final成员在该对象构造返回后(由于指令重排，外部已经获取对象的引用，但是其普通成员变量可能还是默认值，没有完成构造函数中的赋值操作)，其final值一定是完成赋值的，外界所见的就是最终值。而在1.5之前可能观察到未完成的默认值以及最终赋值的初始值，会观察到final值的变更。
7. volatile有几点注意：首先volatile类型变量读写的原子性，其次是写对读的happens-before(可见性规则)，最后是指令重排方面的限制(比如5中的描述)。前者保证不同线程每次都取最新值而不使用缓存，后者避免指令重排带来的执行顺序以及初始化问题，但是对volatile变量的原子操作仅限读过程和写过程(如a++这样一个操作实际上涉及一些列原子操作，即使volatile修饰，也不能将整个过程视为原子操作)，并非完全线程安全，volatile的变量的线程安全是有条件的，一是运算结果不依赖变量本身的数值或者确保只有一个线程修改数值，二是变量不需要与其他状态变量共同参与不变约束。(原子性只是线程安全的一方面，不能完全保证线程安全。)
8. synchronized同步含义：除了我们临界区的概念，一次只能有一个线程持有锁并操作，还有一个很重要的概念，同一个锁的unlock对lock的happens-before原则(可见性规则)，意味着，相关缓存会被刷新(缓存写入主存以及变量重新加载等)。
9. happens-before是一种可见性的规则，如果A先于B发生，则A的操作对B可见，这样就叫做A hb B。JMM关于HB有8条规则，如果两个操作之间的关系不能从这8条规则推导出来，则这两个操作没有顺序保证，虚拟机(还是编译器?)可以随意重排。对于符合HB的，只要重排是合理的，也是允许的。
10. 鉴于编译器的优化策略，比如`指令重排`以及`前向替代`，对于并发访问应使用合适的同步策略。(jls17.4开篇就列出了编译器优化带来的一些奇特现象)

### word tearing
略

### long double操作
1. 对于long、double基本类型操作，不是原子操作，最好使用volatile或者同步策略。
2. 对于引用变量本身，无论是32位还是64位虚拟机，都是原子操作。


### 相关资料
1. jsr列表：https://www.jcp.org/en/jsr/all
2. java memory model（jsr133） 
3. The Java Memory Model http://www.cs.umd.edu/~pugh/java/memoryModel/
4. JMM FAQ (好文 http://www.cs.umd.edu/~pugh/java/memoryModel/jsr-133-faq.html)
5. 旧资料http://gee.cs.oswego.edu/dl/cpj/index.html
6. java并发相关的一个uml图 https://www.uml-diagrams.org/java-7-concurrent-uml-class-diagram-example.html
7. java线程状态的一个uml图
https://www.uml-diagrams.org/java-thread-uml-state-machine-diagram-example.html