<!-- TOC -->

- [jvm model & GC(jvm model不同于jmm)](#jvm-model--gcjvm-model不同于jmm)
    - [概览](#概览)
    - [类加载过程](#类加载过程)
    - [Run-Time Data Areas](#run-time-data-areas)
    - [执行引擎](#执行引擎)
    - [JVM性能调优监控工具](#jvm性能调优监控工具)
    - [GC 算法](#gc-算法)
    - [GC 垃圾回收器](#gc-垃圾回收器)
    - [Full GC 触发条件](#full-gc-触发条件)
    - [](#)
    - [talks](#talks)
    - [参考](#参考)

<!-- /TOC -->
# jvm model & GC(jvm model不同于jmm)

## 概览
>Run-Time Data Areas(运行时数据区)
1. 程序计数器；虚拟机栈；本地方法栈；|| 方法区(含运行时常量池)；堆；
2. 虚拟机栈(jvm stack) & 栈帧(frame)
3. 栈帧：局部变量表；操作数栈；常量池引用(`做什么?`)
4. 方法区在逻辑上可以视为堆的一部分，但是简单的实现可能会选择不对其进行垃圾回收或者空间压缩。
5. StackOverflowError & OutOfMemoryError

>Hotspot Heap Structure(hotspot 虚拟机堆实现策略)
1. 新生代、老生代、永久区
2. young: eden、survivor0、survivor1
3. old: tunured
4. permanent：方法区、常量池引用

>1.8之后hotspot模型变动
1. 删除永久区
2. 增加元数据区(对应到本地内存)
3. 方法区在元数据区中
4. 运行时常量池放在在堆中(1.7 hotspot已经采用这种策略，`具体堆中应该是在老生代中吧?`)

>Hotspot GC(hotspot虚拟机GC策略)
1. 新生代young GC - minor gc(eden->survivor to/ survivor from->survivor to)
2. 老生代old GC - ~~major gc的称呼并不严谨~~
3. full gc - 整个heap收集算法


>对象内存布局
1. markword-对象头-类型指针-实例数据-对齐填充

>对象访问定位
1. handle(句柄方案，本地变量表->堆句柄池->堆对象实例数据&方法区对象类型数据)
2. pointer(指针方案-hotspot采用这种策略，本地变量表->堆对象实例(含有对象类型指针)->方法区对象类型数据)

>JVM 模块
1. class-类文件
2. classloader-各种加载器
3. RuntimeDataAreas-运行时数据区
4. 执行引擎
5. 本地方法接口
6. 本地方法库

>类加载器
1. 启动类加载器
2. 扩展类加载器
3. 应用程序类加载器
4. 用户自定义类加载器

>对象存活判断
1. 引用计数法
2. 可达性分析(hotspot采用这种策略)

>gc roots
1. 虚拟机栈(栈帧中的本地变量表)中引用的对象  
1. 方法区中类静态属性引用的对象
1. 方法区中常量引用的对象  
1. 本地方法栈中JNI(即一般说的Native方法)引用的对象
1. 已启动且未停止的java线程

>引用类型
1. 强引用(正常的引用，new一个对象)
2. 软引用(SoftReference，内存不足的时候会清除，可结合引用队列使用)
3. 弱引用(WeakReference，只能存活到下一次GC之前，可结合引用队列使用)
4. 虚引用(PhantomReference，无法通过虚引用获取对象，虚引用设置的目的就是在对象被回收时收到一个通知)

## 类加载过程
>类生命周期

java类生命周期：加载-链接-初始化-使用-卸载；其中链接包括：验证-准备-解析三个过程。可以通过设置JVM参数打印类加载过程：-verbose:class(另外设置参数-verbose:gc可以查看垃圾回收相关日志)。

>类加载/卸载时机

加载就相当于找到class文件以流的形式读入内存生成对应c++数据结构(kclass)并执行相关操作，类信息都在方法区，方法区在1.7之前位于永久代，1.8之后移到元数据区(本地内存)，类加载时机没有明确规定，总结起来分为显示加载和隐式加载，前者比如调用Class.forName、ClassLoader.loadClass、反射、JNI_FindClass都会触发类加载，后者比如初次new一个类对象，则会加载该类。

卸载就是从方法区把class信息移除。`类卸载的场景?`方法区内存不够用时，会发生gc吗?gc会进行类卸载吗?类卸载的条件?

>类加载器 classloader

java9之前有三种：启动类加载器，扩展类加载器，应用程序类加载器；
java9之后两种：启动类加载器，平台类加载器。

>链接过程

解析环节主要是处理class文件中的符号引用，class在加载到虚拟机之前，并不知道成员变量和成员方法的具体地址，编译器会生成一个符号引用来表明所在类、相关方法、参数类型以及返回类型，链接的时候就是将符号引用换成实际的地址。jvm规范没有要求连接过程完成解析，在字节码执行之前完成解析即可。

## Run-Time Data Areas
1. jvm进程-虚拟机实例
2. jvm线程-虚拟机栈
3. 方法调用-虚拟机栈中的栈帧(本地变量表-操作数栈-常量池引用-入口-出口)
4. 局部变量-栈帧中的本地变量表
5. new出的对象-堆
6. 线程私有：虚拟机栈、本地方法栈、程序计数器
7. 共享区域：堆、方法区
8. 堆：eden/s0/s1;old
9. 通过参数设置各区域大小和变化规则，可以提高程序性能

## 执行引擎
## JVM性能调优监控工具
1. jps
2. jstack
3. jmap(memory map) & jhat(heap analysis tool)
4. jstat
5. hprof(heap/cpu profiling tool)

## GC 算法
1. 标记-清除/内存碎片问题
2. 标记-整理(老年代)/解决内存碎片，但是每次都得移动对象，成本较高，标记->清除->整理
3. 复制-清除(新生代)/解决内存碎片，提高效率，但是内存利用率受影响
4. 分代收集(hotspot-gc策略)

## GC 垃圾回收器
1. Serial
2. ParNew
3. Parallel Scavage
4. Serial Old
5. Pararrel Old
6. CMS(并发标记清除，The CMS collector is deprecated as of JDK 9)
7. G1(Garbage First)
8. ZGC(Z Garbage Collector is available as an experimental feature, starting with JDK 11.)


## Full GC 触发条件
1. System.gc方法的调用(不保证触发)
2. 老年代空间不足(minor gc后老对象可能移入老年代，大对象生成可能直接进入老年代)
3. 方法区空间不足(`1.8后hotspot方法区在元数据空间对应本地内存`是否还有gc?)
4. ~~空间分配担保失败(和2原理相似)~~

## 

## talks
1. 进程和虚拟机实例
2. 线程通信和进程通信

## 参考
>JVM 规范(具体的实现是从规范出发，各种灵活多变的实现策略)

https://docs.oracle.com/javase/specs/jvms/se8/html/jvms-2.html#jvms-2.5.4


>JVM相关技术
https://docs.oracle.com/en/java/javase/13/vm/java-virtual-machine-technology-overview.html

>各种垃圾回收

https://docs.oracle.com/en/java/javase/13/vm/garbage-collection-enhancements.html

>一个CS知识总结

https://cyc2018.github.io/CS-Notes/#/notes/Java%20%E8%99%9A%E6%8B%9F%E6%9C%BA?id=%e4%b8%80%e3%80%81%e8%bf%90%e8%a1%8c%e6%97%b6%e6%95%b0%e6%8d%ae%e5%8c%ba%e5%9f%9f

>一份书籍

https://lingcoder.github.io/OnJava8/#/sidebar