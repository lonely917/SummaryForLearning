<!-- TOC -->

- [Java知识手册](#java知识手册)
    - [String、StringBuilder、StringBuffer](#stringstringbuilderstringbuffer)
    - [多态](#多态)
    - [java静态方法能否覆盖来实现多态](#java静态方法能否覆盖来实现多态)
    - [java重写注意事项(返回值与重载无关，但是重写要注意)](#java重写注意事项返回值与重载无关但是重写要注意)
    - [java静态变量初始化以及静态代码块执行](#java静态变量初始化以及静态代码块执行)
    - [java中构造函数调用和自身变量赋值顺序](#java中构造函数调用和自身变量赋值顺序)
    - [接口和抽象类](#接口和抽象类)
    - [transient变量不可序列化](#transient变量不可序列化)
    - [序列化和克隆是标志接口](#序列化和克隆是标志接口)
    - [java线程使用](#java线程使用)
    - [thread.join](#threadjoin)
    - [java 文件操作](#java-文件操作)
    - [java threadlocal](#java-threadlocal)
    - [Arrays.sort](#arrayssort)
    - [Collections.sort](#collectionssort)
    - [一些标志接口](#一些标志接口)
    - [Arraylist的clone是shallow copy](#arraylist的clone是shallow-copy)
    - [iterator和listiterator](#iterator和listiterator)
    - [identityhashmap](#identityhashmap)
    - [collection之前的各种容器](#collection之前的各种容器)
    - [容器和集合是如何扩容的](#容器和集合是如何扩容的)
    - [Properties](#properties)
    - [BitSet](#bitset)
    - [集合遍历和排序](#集合遍历和排序)
    - [Collection&Map相关接口、抽象类、实现类](#collectionmap相关接口抽象类实现类)
        - [接口](#接口)
        - [抽象类](#抽象类)
        - [实现类](#实现类)
    - [JCF & Arrays](#jcf--arrays)
    - [内存泄漏场景](#内存泄漏场景)
    - [内部类、静态内部类、匿名类、静态匿名类对象](#内部类静态内部类匿名类静态匿名类对象)
    - [Android Handler与内存泄漏](#android-handler与内存泄漏)
    - [深入研究技术点](#深入研究技术点)

<!-- /TOC -->
# Java知识手册

## String、StringBuilder、StringBuffer
- String final类型，是字符串常量(常量池相关 intern用法需要了解)，后两者变量。
- StringBuffer线程安全效率也就会低一些。 
- 编译器会对代码进行优化、有些拼接String并不会降低效率(看上去会)，因为编译器对代码进行了优化。


## 多态
多态的表现形式：重载；子类覆盖父类方法；实现类实现接口方法。
第一个静态实现，后两个动态实现。
没有必要非要将多态和动态去关联，了解实现机制以及表现方法即可，命名只是为了统一推广和交流方便。

## java静态方法能否覆盖来实现多态
子类试图覆盖父类静态方法，静态方法的调用不依赖于对象，所以不会表现出多态，类似变量的屏蔽，父类子类依然分别调用自己的函数

## java重写注意事项(返回值与重载无关，但是重写要注意)
java中方法重写需注意事项
1. 发生方法重写的两个方法的方法名、参数列表必须完全一致(子类重写父类的方法) ，返回值必须是父类方法返回值的子类、协变返回类型。
2. 子类抛出的异常不能超过父类相应方法抛出的异常(子类异常不能大于父类异常) 
3. 子类方法的访问级别不能低于父类相应方法的访问级别(子类访问级别不能低于父类访问级别)
4. 上述重写都是针对实例方法而不是类方法，静态方法属于类而不是对象，没有多态性。

一些场景测试：父子类分别有同名实例方法和静态方法；方法签名同(方法名和参数列表)当时返回值不同的情况；

注意:我们这里是在总结规律，规律的定则是虚拟机实现决定的，或者说是由java语言规范/jvm虚拟机规范来决定。

## java静态变量初始化以及静态代码块执行
二者发生在类加载过程中，比如第一次new()一个对象实例或者反射加载类
(参考http://www.cnblogs.com/ivanfu/archive/2012/02/12/2347817.html)
jvm类运行装载-连接-初始化。
含有public static void main的 执行时，主类也要初始化，然后调用静态main方法。

普通对象第一次生成时：

        父类静态代码块、
        子类静态代码块、
        父类非静态代码块以及变量初始化、//按照出现的先后顺序
        父类构造函数、
        子类非静态代码块以及变量初始化。

再次new的时候，静态部分不再执行。

## java中构造函数调用和自身变量赋值顺序
没有this调用的情况下(this调用其他的重载构造函数)：
- 如果没有super(必须在第一句)，则添加super父类调用(父类过程同样)
- 进行自己的变量赋值(代码中的声明和初始化语句)
- 然后执行构造函数代码。

有this调用的情况下在this调用中进行如是操作，返回后执行其余代码。

`可以对比c++ 构造函数以及参数化列表相关情况`

## 接口和抽象类
1. 接口之间是可以多继承的。
2. 类单继承但可以实现多个接口。
3. 接口变量、方法会自动加上public static final 和 public abstract
4. 接口的方法全是抽象方法，无实现。//注意:java8之后interface推出default implementation
5. 抽象类可以有变量和非抽象方法，当然原则上可以没有抽象方法只要声明abstract class 但是这个没意义。
6. 实现类必须实现全部接口方法，否则算是抽象类（类，含有抽象方法必须是抽象类才可以）。
7. 子类继承抽象类没有实现全部抽象方法，还要声明抽象类。
8. 实现接口是拥有一项功能(也可用于划分类别，比如标志接口的理解：具有某一类功能的类集合，实际上接口里没有任何抽象方法待实现，接口就是用于标记类型、划分类型)，继承抽象类则是是某一类。

## transient变量不可序列化

## 序列化和克隆是标志接口

## java线程使用
1. 启动一个线程可以去写一个runnable的实现类然后构造一个thread，对thread传入一个runnable接口类。
2. 也可以new一个thread，覆盖它的run方法。

二者效果是一样的，源代码级别来看，thread的默认的run方法(不被覆写的话)是调用target(target不为空的话)的run方法，target就是我们传入的runnable接口类。

## thread.join
thread的join通过native的wait帮助检测线程是否结束

## java 文件操作
## java threadlocal
关于并发同步，我们想到synchronized关键字，同样threadlocal可用于创建线程变量副本，但我觉得似乎与同步不太相关，毕竟各自有一个副本。

threadlocal的get和set方法是使用的关键，如果没有重写 initialValue的方法，则get之前必须set，否则报null异常。
内部实现原理就是每个Thread实例本身有一个局部变量threadlocals(threadlocalmap类对象)，通过这个map可以根据thread找到对应的threadlocal。
那么这个线程变量实际就是我们和线程绑定的一个局部变量，那他和我们自己定义的局部变量又有什么不同的，除了这个作用更加专一，线程相关，但是副本的功能局部变量或者实例变量也能做到。


## Arrays.sort

1. primitive->quicksort

2. Object->mergesort(可以传入comparator，不传入则null，必须是可比较对象 comparable)

3. 长度<7使用insertation sort

4. 注意归并的时候src和dest的来回复用，以及归并合并时的优化(提前判断是否已经有序)

这里涉及到很多排序的方法，不同jdk版本的实现也会有差异。


## Collections.sort

最终调用Arrays的sort（object）方法；

也可以传入比较器；



## 一些标志接口 

Clonable Serializable等接口



## Arraylist的clone是shallow copy

源码中有说明，数组中的对象本身并没有复制，被两个数组引用着。



## iterator和listiterator

后者除了向后遍历next还能够previous

## identityhashmap
区别于一般的hashmap是其判断相等的方法，判断引用值等而不在乎具体对象内容。

    This class implements AbstractMap. It is similar to HashMap except that it uses reference equality when comparing the elements.

几个例子很清晰
http://blog.csdn.net/stoneok07/article/details/7262676

## collection之前的各种容器
java2提出JCF-集合框架(java collection framework)，查看源码会看到Collection和Map接口是在1.2之后加入的。那么由他们引申来的XXList、XXMap在1.2之前是没有的。

1.0中相关的集合或者列表功能的类(即对数据结构中的线性表、栈、树、图、哈希表等的一些实现或者基础支持)

    Vector
    Stack
    HashTable->Dictionary

1. Vector实现一个动态的数组，和Arraylist类似，不同之处在于vector是线程安全的，方法都是同步的，synchronized；其次vector有很多旧方法legacy methods，而这些旧接口不属于collections framework(arraylist是集合框架下的一个具体实类)。

2. Stack是vector的子类，一个后进先出的vector，只提供一个默认构造函数，除了vector的方法外，添加了一些特有方法，pop、push、peek等。peek是查看栈顶元素但不弹出，pop则是返回栈顶元素，并弹出。

3. Dictionary是一个抽象类，类似于map接口的功能。目前被标记为过时的。

    The Dictionary class is obsolete. You should implement the Map interface to obtain key/value storage functionality.

4. Hashtable是Dictionary的具体实现类，早期java.util的一部分。java2提出集合框架后，进行整合，1.2源码可以看到它继承Dictionary并实现了map接口。

    Hashtable was part of the original java.util and is a concrete implementation of a Dictionary.

总结：有时候接口或者抽象类去实现或者继承，不一定真的需要进行关联，而是为了进行概念和逻辑上的一个统一，比如后期的延伸需要与前期版本进行兼容，实际上两者可能并不需要继承或实现关系。

    However, Java 2 re-engineered Hashtable so that it also implements the Map interface. Thus, Hashtable is now integrated into the collections framework. It is similar to HashMap, but is synchronized.

早期的容器都是同步的，集合框架中很多不是线程同步的，这样设计的一个原因是多数情况下不需要去进行线程同步，因此一定程度上提高集合效率，另一方面集合也提供了对应的同步版本或者说支持并发的集合版本。


## 容器和集合是如何扩容的

1. hashtable会有一个装载因子，比如容量达到一定界限(比例)会进行扩容，map类型也是如此；

2. vector有一个增加值，满了之后重新申请空间，原空间加上增加值是新的空间的大小(增加值不为0的情况下，直接变为原来的两倍)，得到的新值再和实际所需空间比较，还不够，则用实际所需空间作为新的空间大小去申请。会和一个max比较，是否oom。

3. 对于collection集合实现类比如arraylist，则是默认加上自身空间的一半，也就是1.5倍扩容，不够的话使用实际所需空间去申请。会和一个max比较，是否oom。


## Properties
Properties是hashtable的一个子类，额外添加一些方法，比如从文件读取以及写入文件。

    Properties is a subclass of Hashtable. It is used to maintain lists of values in which the key is a String and the value is also a String.

## BitSet
BitSet的提出since1.0,后来进行了重新设计。
位运算对应集合运算，and、or、xor ->  求交、求和、求两个差集的和。

    This is a legacy class but it has been completely re-engineered in Java 2, version 1.4.

## 集合遍历和排序

1. 遍历集合 iterator
2. 集合排序 comparator

    Iterator enables you to cycle through a collection, obtaining or removing elements. ListIterator extends Iterator to allow bidirectional traversal of a list and the modification of elements.注意后者还能双向遍历，并且可以修改集合元素。

## Collection&Map相关接口、抽象类、实现类

### 接口
```
1. collection 
	list //有序的collection
	set  //不重复的collection
		sortedset//有序的set
2. Map //(unique keys to values)[ map.entry]
	sortedmap//keys 升序的map

3. Iterator //遍历Collection。注意Map本身不提供Iterator遍历，但是可以获取集合视图进而使用Iterator
```
`早期使用Enumeration对Vector、Hashtable进行遍历，1.2之后被Iterator取代`

### 抽象类
```java
AbstractCollection  实现大多数的collection接口方法
	AbstractList 继承自abstractcollection并实现多数List接口
		AbstractSequentialList 继承自abstractlist提供顺序访问而非随机访问
	AbstractSet 继承自AbstractCollection  并实现多数set接口
AbstractMap 实现多数map接口
```
### 实现类
```
linkedlist是AbstractSequentialList 的实现类，抽象类的实现类。

Arraylist 是 AbstractList.的实现类(Implements a dynamic array by extending AbstractList.)

HashSet是AbstractSet 的实现类，使用的hashmap，组合。
	linkedhashset是有序的hashset，插入顺序。
TreeSet 是 AbstractSet 的实现类，使用树结构存储。

HashMap是AbstractMap的实现类，使用哈希存储。
	linkedhashmap，保持插入顺序的HashMap子类。

Treemap是AbstractMap实现类，使用树存储(java中使用的是红黑树)

IdentityHashMap 是AbstractMap实现类，不同于hashmap的是判等方式，使用引用值是否相等进行判断。
```

## JCF & Arrays

## 内存泄漏场景

## 内部类、静态内部类、匿名类、静态匿名类对象

## Android Handler与内存泄漏
涉及静态内部类以及弱引用相关操作

## 深入研究技术点
1. 源码中sort实现，双基准快排、tim-sort等
2. 集合迭代器的实现，iterator和foreach比较
3. java中树结构