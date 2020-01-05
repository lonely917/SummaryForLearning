<!-- TOC -->

- [jvm model & GC](#jvm-model--gc)
    - [概览](#概览)
    - [Run-Time Data Areas](#run-time-data-areas)
    - [GC 算法](#gc-算法)
    - [GC 垃圾回收器](#gc-垃圾回收器)
    - [Full GC 触发条件](#full-gc-触发条件)
    - [talks](#talks)
    - [参考](#参考)

<!-- /TOC -->
# jvm model & GC

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

>GC类型
1. 

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


## Run-Time Data Areas

## GC 算法
1. 标记-清除
2. 标记-整理(老年代)
3. 复制-清除(新生代)
4. 分代收集(hotspot-gc策略)

## GC 垃圾回收器
1. Serial
2. ParNew
3. Parallel Scavage
4. Serial Old
5. Pararrel Old
6. CMS(并发标记清除)
7. G1

## Full GC 触发条件
1. System.gc方法的调用(不保证触发)
2. 老年代空间不足(minor gc后老对象可能移入老年代，大对象生成可能直接进入老年代)
3. 方法区空间不足(`1.8后hotspot方法区在元数据空间对应本地内存`是否还有gc?)
4. ~~空间分配担保失败(和2原理相似)~~

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