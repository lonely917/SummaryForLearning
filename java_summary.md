<!-- TOC -->

- [面向对象相关知识1](#1)
    - [多态](#)
    - [java静态方法能否覆盖来实现多态](#java)
    - [java重写注意事项(返回值与重载无关，但是重写要注意)](#java)
    - [java中构造函数调用和自身变量赋值顺序](#java)
    - [java静态变量初始化以及静态代码块执行](#java)
    - [普通对象生成时初始化顺序](#)
    - [接口和抽象类](#)
- [面向对象相关知识2](#2)
    - [内部类以及静态内部类](#)
    - [final关键字](#final)
    - [抽象类](#)
    - [方法签名](#)
    - [重写和重载](#)
    - [重写与屏蔽](#)
    - [参数传递](#)
    - [拷贝构造函数](#)
    - [java内存模型(memory model)](#javamemory-model)
    - [不可变类的分析](#)
    - [BigInteger类分析之不可变类](#biginteger)
    - [String类分析之不可变类(immutable特性)](#stringimmutable)
    - [如何设计一个不可变类](#)
    - [包与成员可见性问题](#)
    - [理解protected](#protected)
    - [java interface](#java-interface)
    - [一些混淆场景](#)
    - [关于一些规范的间接](#)
- [Java集合内容1](#java1)
    - [JCF之前的各种容器类](#jcf)
    - [JCF相关接口、抽象类、实现类](#jcf)
        - [接口](#)
        - [抽象类](#)
        - [实现类](#)
    - [容器和集合的扩容](#)
    - [HashMap和HashTable的扩容](#hashmaphashtable)
    - [Properties](#properties)
    - [BitSet](#bitset)
    - [IdentityHashMap(since1.4)](#identityhashmapsince14)
    - [集合遍历和排序](#)
    - [List各种遍历方式效率比较](#list)
    - [Arrays.sort方法解析](#arrayssort)
    - [Collections.sort方法解析](#collectionssort)
- [Java集合内容2](#java2)
    - [Java集合](#java)
        - [常用集合类继承结构](#)
        - [Vector、ArrayList和LinkedList使用场景比较](#vectorarraylistlinkedlist)
        - [HashTable、HashMap、HashSet使用场景比较](#hashtablehashmaphashset)
        - [线程安全](#)
        - [面向接口编程思想](#)
    - [Map小结](#map)
    - [java HashMap关键点分析(1.8版本源码)](#java-hashmap18)
    - [HashMap 和 HashTable 对比](#hashmap--hashtable)
        - [相同点](#)
        - [不同点](#)
    - [ConcurrentModificationException](#concurrentmodificationexception)
    - [java LinkedList 解析](#java-linkedlist)
    - [JCF & Arrays](#jcf-arrays)
    - [比较类集和数组](#)
    - [toarray & aslist方法](#toarray-aslist)
    - [java-树相关数据结构](#java)
        - [BST概念](#bst)
        - [Java-TreeMap](#java-treemap)
        - [红黑树VS二叉排序树](#vs)
        - [红黑树](#)
    - [TreeMap、TreeSet 对比 HashMap、HashSet的优缺点](#treemaptreeset--hashmaphashset)
- [jdk源码中的设计模式](#jdk)
- [其他](#)
    - [Java基本数据类型](#java)
    - [String、StringBuilder、StringBuffer](#stringstringbuilderstringbuffer)
    - [java hashCode函数](#java-hashcode)
    - [hashcode再次研究 搜索 “高效地hashmap”](#hashcode--hashmap)
    - [hash算法散列实现，各种方法](#hash)
    - [十种排序比较](#)
    - [排序动画](#)
    - [C++常见数据结构以及算法](#c)
    - [java强弱引用介绍](#java)
    - [文件操作](#)
    - [内存泄漏场景](#)
    - [transient变量不可序列化](#transient)
    - [序列化和克隆是标志接口](#)
    - [Arraylist的clone是shallow copy](#arraylistcloneshallow-copy)
    - [Linkedlist的clone是shallow copy](#linkedlistcloneshallow-copy)
    - [Arrays.copy 和 数组clone的比较](#arrayscopy--clone)
    - [对象clone和拷贝构造函数性能比较？](#clone)
    - [java线程使用](#java)
    - [thread.join](#threadjoin)
    - [java 文件操作](#java)
    - [java threadlocal](#java-threadlocal)
    - [fast-fail & happens before](#fast-fail-happens-before)
    - [readObject、readResolve以及序列化、不可变类](#readobjectreadresolve)
    - [CountDownLatch-可变类但被限制状态空间](#countdownlatch)
    - [volatile关键字](#volatile)
    - [transient关键字](#transient)
    - [](#)
    - [深入研究技术点](#)
- [Java知识图谱-wenburgyan](#java-wenburgyan)

<!-- /TOC -->
# 面向对象相关知识1

## 多态
多态的表现形式：重载；子类覆盖父类方法；实现类实现接口方法。
第一个静态实现，后两个动态实现。
没有必要非要将多态和动态去关联，了解实现机制以及表现方法即可，命名只是为了统一推广和交流方便。

## java静态方法能否覆盖来实现多态
子类试图覆盖父类静态方法，静态方法的调用不依赖于对象，所以不会表现出多态，类似变量的屏蔽，父类引用和子类引用依然分别调用自己的函数。

## java重写注意事项(返回值与重载无关，但是重写要注意)
java中方法重写需注意事项
1. 发生方法重写的两个方法的方法名、参数列表必须完全一致(子类重写父类的方法) ，返回值必须和父类相同或者是父类方法返回值的子类(协变返回类型)。
2. 子类抛出的异常不能超过父类相应方法抛出的异常(子类异常不能大于父类异常) 
3. 子类方法的访问级别不能低于父类相应方法的访问级别(子类访问级别不能低于父类访问级别)
4. 上述重写都是针对实例方法而不是类方法，静态方法属于类而不是对象，没有多态性。
5. 子类不能使用相同签名的静态方法屏蔽父类实例方法，也不能使用同签名的实例方法屏蔽父类静态方法，会提示错误。
6. 重写要求是同签名的实例方法(可继承的方法不可能是private的)，返回值方面要求相同或者协变返回类型。
7. 静态方法的屏蔽也如6一样对签名和返回值有要求，返回值不合格，屏蔽或者重写就不正确，直接报错。

一些场景测试：父子类分别有同名实例方法和静态方法；方法签名同(方法名和参数列表)但是返回值不同的情况；

注意:我们这里是在总结规律，规律的根源则是虚拟机实现决定的，或者说是由java语言规范/jvm虚拟机规范来决定。

## java中构造函数调用和自身变量赋值顺序
没有this调用的情况下(this调用其他的重载构造函数)：
1. 如果没有super(必须在第一句)，则添加super父类调用(父类过程同样)
2. 进行自己的变量赋值(代码中的声明和初始化语句)
3. 然后执行构造函数代码。

## java静态变量初始化以及静态代码块执行
1. 二者发生在类加载过程中，比如第一次new()一个对象实例或者反射加载类。

(参考http://www.cnblogs.com/ivanfu/archive/2012/02/12/2347817.html)
jvm类运行装载-连接-初始化。

2. 含有"public static void main"的程序运行时，主类也要初始化，然后才调用静态main方法。

## 普通对象生成时初始化顺序
1. 首次生成
```
从加载和调用角度如下：
    父类静态代码块(类加载时首先加载父类，加载时执行静态代码块)
    子类静态代码块
    子类构造函数(第一句没有super又不是this调用重载构造函数则会自动加上super首先执行父类构造函数)
        父类构造函数
            父类非静态代码块以及变量初始化、//按照出现的先后顺序
            父类构造函数体内容
        子类非静态代码块以及变量初始化。
        子类构造函数体内容
简化版：
    父类静态代码块
    子类静态代码块
    父类非静态代码块以及变量初始化、//按照出现的先后顺序
    父类构造函数体内容
    子类非静态代码块以及变量初始化。
    子类构造函数体内容

```
2. 再次生成
```
直接使用简化版描述：
    父类非静态代码块以及变量初始化、//按照出现的先后顺序
    父类构造函数体内容
    子类非静态代码块以及变量初始化。
    子类构造函数体内容

```

3. 构造函数中涉及到this调用其他重载构造函数的，在this调用中进行如是操作，返回后执行其余代码。如果有this也必定是在方法第一句，this和super不能同时出现。

`可以对比c++ 构造函数以及参数化列表相关情况`

## 接口和抽象类
1. 接口之间是可以多继承的。
2. 类单继承但可以实现多个接口。
3. 接口变量会自动加上public static final、接口方法会自动加上 public abstract
4. 接口的方法全是抽象方法，无实现。//注意:java8之后interface推出default implementation
5. 抽象类可以有变量和非抽象方法，当然原则上可以没有抽象方法只要声明abstract class 但是这个没意义。
6. 实现类必须实现全部接口方法，否则算是抽象类（类，含有抽象方法必须是抽象类才可以）。
7. 子类继承抽象类没有实现全部抽象方法，还要声明抽象类。
8. 实现接口是拥有一项功能(也可用于划分类别，比如标志接口的理解：具有某一类功能的类集合，实际上接口里没有任何抽象方法待实现，接口就是用于标记类型、划分类型)，继承抽象类则是是某一类。

# 面向对象相关知识2

## 内部类以及静态内部类
1. 嵌套类是定义在另一个类内部的类。内部类(inner class)是非静态的嵌套类(An inner class is a nested class that is not explicitly or implicitly declared static)
2. 内部类有三种：非静态成员类、匿名类、局部类。静态成员类可以看作普通类，只是在一个类内部声明而已，并可以访问外围类的所有成员。静态成员类不是内部类，虽然我们会使用静态内部类这样的称呼，但是在jsl的规范中，它不是inner class.
3. 内部类定义在外部类中，甚至可以是代码块中(比如某一个方法内部)，即2中所说的局部类。
4. 代码块中的内部类不允许静态内部类，会提示错误，只能有final或者abstract修饰，而且这样的类只作用于代码块中，外部无法调用。
5. 非静态内部类会持有外部类对象的引用，注意使用匿名内部类对象或者lambda表达式可能造成的内存泄漏。
6. 非静态内部类可以直接访问外部类对象的成员。
7. 静态内部类只能访问封装类中的静态成员，很好理解，静态的，不依赖于外部类的某一对象，自然不能访问其非静态成员。
8. 非静态内部类可以使用qualified this来使用外部类的引用，也就是"外部类名.this"的形式。
9. 非静态内部类不能定义static类型变量(类变量)，除非定义成常量，也就是static final类型才可以。但是可以由继承而获得static的成员。(jsl8.1.3)
10. 非晶态内部类可以定义static方法，没有限制，跟普通类一样。(jsl8.1.3)
11. 所有规律或者行为都起源于jsl，java语言规范对所有行为进行了说明，语言设计也遵循这一规范。

## final关键字
1. final类，不能被继承，不会有子类。 //防继承
2. final方法，类中的final方法，表示该方法不能被重写。//防重写
3. final变量，表示该变量生命周期内不可变，只能赋值一次。//防修改
4. final修饰形参，方法内不能修改这个传参拷贝变量，这里指得是这个引用值本身，对应引用对象的成员是可能进行改变的。

## 抽象类
1. 有抽象方法的类一定是抽象类
2. 抽象类语言规范上不要求一定有抽象方法
3. 抽象类可以有实例方法，但是抽象类不可实例化
4. 抽象类子类要想实例化必须实现所有抽象方法，否则只是一个抽象子类

## 方法签名
1. 方法签名包含方法名和参数列表(参数类型、顺序、个数)
2. 返回值不属于方法签名
3. 返回值与重载无关，overload。
4. 返回值与重写有关，重写(override)要求返回类型相同或者是协变返回类型(1.5之后提出的协变返回类型)

## 重写和重载
java多态，一个方法具有不同的表现，有两种实现途径，重写和重载。
1. 重写(子类重写父类方法，以及实现接口的抽象方法)属于动态的，用父类变量引用子类对象，运行时根据具体子类决定实际行为。
2. 重载，静态编译决定行为。具有相同方法但是不同的方法签名。比如参数类型或者个数不同。

## 重写与屏蔽
1. 重写只针对非静态方法(类的实例方法)，具体调用是一种动态行为，运行时决定。
2. 屏蔽针对变量或者静态方法，编译即决定具体调用情况。也就是根据发起调用的这个对象的定义或者声明类型，决定具体使用的变量或者静态方法，而不像重写那样会看这个对象的真实类型(父类引用子类对象的情况)，但是继承在父类和子类之间是存在的，因此当发生这种同名的冲突时，相当于发起调用方的声明类型能够对实际类型进行一定程度的屏蔽。

## 参数传递
1. Java中一些数据类型：值类型(基本数据类型)、引用类型(对象)、数组类(first-class)也是引用类型.
2. Java的引用变量可以看作一个地址，类似指针，引用变量对应引用着一个对象，就像指针指向了一个对象。
3. 对比，c中传值、传地址；c++传值、传地址、引用传递(alias,别名)，c和c++的传值是指基本类型拷贝和对象的拷贝，传地址就是指针的传递，实际是指针值的传递。
4. Java中都是“值传递”：理解下面的话，基本类型传参则在方法内部进行参数的拷贝，引用类型传参，实际也会进行一个引用变量的拷贝，就像拷贝了一个指针值但没有拷贝对象内容，拷贝后的这个方法内引用变量依然引用外部参数引用变量所引用的实际对象。(c++中支持参数对象拷贝，像基本数据类型一样，java中并不会对引用变量这个参数对应的实际对象进行拷贝)
5. 对于数组，java中引用类型，传参就是一个引用变量值的传递，当时c如果是数组作为参数传递是一个完整的拷贝传递。
7. 对于基本类型可以通过包装器或者人为封装，然后就可以当作引用变量进行传递。

## 拷贝构造函数
1. 注意c++中，会有一个默认拷贝构造函数，进行成员拷贝，浅拷贝。java中需要自己去定义这个函数，而且java参数传递的时候或者引用变量赋值的时候只会进行引用变量值的拷贝，不会像c++中那样进行对象内容的拷贝。这里所谓的拷贝构造实际上没有什么意义，不像c++中那种对象拷贝行为会在对象赋值以及对象传参中自动触发，java中都是引用变量值。

2. `保护性拷贝`(defensive copy)，effective java中提出的一个概念，对象能少生成就少生成，但是再需要生成的时候不应吝啬。保护性拷贝就是针对后半句的。需要根据设计和场景决定是返回同一个对象的引用，还是返回对象的一个拷贝，这个拷贝可以通过clone和拷贝构造函数来生成，这两个途径都可以自定义来区分浅拷贝还是深拷贝。

## java内存模型(memory model)
jls17.4 & jls17.1

## 不可变类的分析
Integer类&IntegerCache缓存&Integer中的享元模式&Boolen也是享元模式

## BigInteger类分析之不可变类
1. final类型的类，不等同于不可变类，不可变类要通过一定形式保障相关内容是不可变的。
2. 使用final修饰类是设计不可变类的条件之一，不是唯一条件，也不是必须条件。
3. 除了final之外可以通过私有化构造函数转而使用静态工厂的形式来实现final不可继承的特点。
4. BigInteger和BigDecimal没有使用final修饰，也没使用3中的技术，虽然他是不可变类，但是其子类是不可信的。这个是最初设计问题，但考虑兼容性已经无法修改。类似的java.awt中的Point和Demision类的设技与“共有类不应直接暴露共有域”这一原则相悖(effective java 3rd)，也是历史遗留问题。
pow(int)方法的设计

## String类分析之不可变类(immutable特性)
1. final类，不可继承，使得String不会有子类改变其固有设计，final只是类的不可变，并不代表类对象的不可变。
2. 来看String的内部固有设计：其内容是一个private final char[]，这也就是String的内容。
3. 由于char[]是final类型的变量，因此初始化后不能再修改，保证了String中的char[]引用不可变
4. 查看对char[]的所有引用，发现写操作都在初始化构造过程中，也就是没有外部方法提供改变其数组内容
5. 2、3、4保证String类对象不可变(immutable)，1保证String对象的不可变性不能通过重写子类来打破规则。

## 如何设计一个不可变类
1. 所有的成员应该是private final类型的，保证成员不可修改
2. 不应该有成员相关的setter方法暴露，保证成员不能修改(一定程度上和1中final作用重合)
3. 如果成员非基本类型，则除了保证引用本身不可变之外，还要保证引用对应的对象内容不能改变，即不可对外暴露修改其内容的方法。
4. 类本身使用final修饰，保证不能通过子类重写来修改设计。

## 包与成员可见性问题
1. 对于顶层类(非嵌套)来说有public和默认两种。公有类：对所有代码都可访问；默认类：同一个包中的代码才可以访问。
2. 对于类成员有public、protected、默认成员、以及private私有成员，可见性逐渐降低。其中public总是可见的，private只在类内部可见，protected是包内可见、跨包的子类也可见，可见性为默认的成员只在包内可见，对于包外的类不可见。

## 理解protected
如果我们学过c++会有这样一个认识，protected的属性，子类可继承，访问，但是外部不可访问。在java中略有差异：它允许所在包访问以及其他包的子类访问。也就是说子类可以访问，同一个包内的其他类虽然不是子类，依然可以访问。这里可以对比下c++的规则，c++可见性以及namespace相关概念。

## java interface
    Every interface is implicitly abstract.
    This modifier is obsolete and should not be used in new programs.(取自jsl12)
1. interface可以定义为public或者默认的，隐含是有abstract修饰的，程序中不应在显示地使用abstract修饰interface。
2. 接口中的变量隐式地定义为public static final
3. 接口中的方法被隐式声明为public abstract
4. java8特性，接口提供默认方法，作为接口方法的默认实现；接口中加入static方法(java8之前不允许这样)，但注意抽象方法与static是不兼容的，因为抽象是重写或者实现后呈现出多态，而静态则是类绑定，不具有动态的效果，只可能发生屏蔽的情况。另外java8虽然提出接口中可以有static方法，但是它不能被实现类或者子接口继承。
5. 默认方法又名扩展方法，java8提出，好处是在不破坏现有代码的情况下对接口扩展，比如java8推出了流式操作，为集合类进行了完美的结合。Collection添加了Stream接口并带有默认实现。
6. java8后，接口中可以定义静态方法，但是注意，接口中的静态方法不能被继承，无论是实现类还是子接口。

## 一些混淆场景
1. 静态(接口)方法的继承与屏蔽，类和接口的情况

说明：首先针对的是可继承的方法而言，私有方法自然不再考虑范围。类继承结构中，静态方法相关属于方法屏蔽，子类静态方法会屏蔽父类静态方法(如果父类有的话),运行时根据声明或者定义的类型寻找执行方法；实例方法则是方法重写，多态的表现形式之一，实际运行时根据真正的对象类型而不是定义或者声明的类型(可能是父类型 superclass)去执行对应的方法；另外，静态方法和实例方法之间不能屏蔽，这种场景会直接编译报错。

2. 静态变量、实例变量的屏蔽情况

说明：类继承结构中，静态变量之间，实例变量之间，以及两者结合的场景，都是变量屏蔽，是静态的推断，可以认为编译即决定执行情况，根据对象的定义类型来决定实际调用的是父类还是子类对象，子类对象会出现变量屏蔽的场景。

3. 实例方法的重写情况

说明: 多态性表现之一，最常见的情况。实际关于重写jsl中定义了详细的约束，如果要面面俱到的话还是比较复杂的。我们就注意下述几点：子类可以重写父类中可见的方法，方法签名是一样的，返回值相同或者是协变返回类型，方法可见性不能缩小，抛出的异常不能超出父类异常范围，关于方法签名准确地说是相同或者是子签名subsignature(涉及泛型以及类型擦出，这点了解下即可)。

4. 实现多个接口，方法冲突的情况

说明：。。尽量避免发生这种情况吧。。

5. 方法重写修改访问修饰符的情况

说明：能够被重写的方法，可见性至少是默认类型，可能是protected或者public的，子类重写的时候，不能减少继承来的方法的可见性(can't reduce the visibility of inherited method from super,这一点可以这样理解：为了遵循里氏替换原则，使得可以用父类引用变量来引用子类对象，并调用子类重写的方法)。

## 关于一些规范的间接
从语言使用的角度来看，前面一些易混淆的场景我们并不需要去生硬地记住，甚至不知道也没关系，他们是遵循jsl和jvsl规范的，每个场景都可以从规范中找到对应的说明，至于为何这样设计，有语言严谨性的原因、也有历史发展和兼容性的考虑，设计者的最初的主观想法也会影响规范的设定。如果去研究语言的设计以及jvm的实际实现，那么这些情况在同类型语言的迁移中都应是注意的，如果从构建上层应用的角度来看，了解一些基本的概念即可，比如类继承、实例方法多态、实例变量屏蔽等，一些易混淆的场景我们应该是尽量避免的，这会造成代码理解以及编码协作的障碍，比如试图使用在类中或者继承类中使用同名的变量(无论是类变量还是实例变量)，且不说是否符合规范，即使符合规范也应该尽量避免，又如代码块中定义内部类的情况，不使用这种形式也就没必要去纠结他能否用static修饰了(截至1.8是代码块中不能使用static的内部类)。

当前时间19年8月，java12语言规范
https://docs.oracle.com/javase/specs/jls/se12/html/jls-8.html#jls-8.4.8.2

其中对inherit、overload、override、hiding等进行了详细的定义和约束，对于class和interface以及static method和 instance method都进行了十分详尽的阐述，另外也有Covariant retype(协变返回类型since1.5)以及subsignature等概念相关的场景规范。

`很多细节可能对我们的实际应用关系不大，我们掌握常用的概念即可进行编码工作，我们也应尽量避免易混淆场景，除非真的需要如此。另外我们应结合源码去验证、学习这些概念，去判定哪些知识点是常用的、实用的，而不是孤立地记住他们，更深一步我们可以去了解语言的这些规范是如何实现的，结合jvm的相关知识，最后我们如果能去思考语言设计通用性的一些理念，那就妙不可言了。`


# Java集合内容1

## JCF之前的各种容器类
查看源码会看到Collection和Map接口是在1.2之后加入的。那么由他们引申来的XXList、XXMap在1.2之前是没有的。java2提出JCF-集合框架(java collection framework)。

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

## JCF相关接口、抽象类、实现类

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

## 容器和集合的扩容

1. Hashtable会有一个装载因子，比如容量达到一定界限(比例)会进行扩容，map类型也是如此；

2. Hashtable默认装载因子是0.75，默认的容量大小是11；每次扩容将容量变为“原始容量x2 + 1”。

3. HashMap默认的“加载因子”是0.75, 默认的容量大小是16，每次扩容将容量变为“原始容量x2”。HashMap其中哈希算法是假定表长为2的整数次幂。

4. HashMap和HashMap扩容发生的条件：当前存储数目 >= 阈值按照扩容算法进行扩容，其中阈值 = 总的容量 * 加载因子。扩容后会已有所有元素重新进行散列存储。

5. Vector有一个增加值，满了之后重新申请空间，原空间加上增加值是新的空间的大小(增加值不为0的情况下，直接变为原来的两倍)，得到的新值再和实际所需空间比较，还不够，则用实际所需空间作为新的空间大小去申请。会和一个max比较，是否oom。

6. 对于Collection集合实现类比如ArrayList，则是默认加上自身空间的一半，也就是1.5倍扩容，不够的话使用实际所需空间去申请。会和一个max比较，是否oom。

## HashMap和HashTable的扩容

注意HashMap的key到index的映射方法是假定长度为2的x次幂的情况下，所以默认的初始长度是一个2的次方项(16),而且每次的扩容是double，指定初始容量的构造函数中实际会对这个初始值进行处理，得到一个近似的2的次方项。对比地，HashTable没有这种限制，默认初始长度11，扩容利用2*x+1进行计算。扩容会对已有内容进行重新计算，所以扩容实际上是一个比较耗时的操作，于是合适的初始化长度就比较重要，在内存消耗和扩容时间消耗之间进行折衷处理。

## Properties
Properties是hashtable的一个子类，额外添加一些方法，比如从文件读取以及写入文件。

    Properties is a subclass of Hashtable. It is used to maintain lists of values in which the key is a String and the value is also a String.

## BitSet
BitSet的提出since1.0，后来进行了重新设计。
位运算对应集合运算，and、or、xor ->  求交、求和、求两个差集的和。

    This is a legacy class but it has been completely re-engineered in Java 2, version 1.4.

## IdentityHashMap(since1.4)
区别于hashmap的是其判断相等的方法，判断引用值等而不在乎具体对象内容。

    This class implements AbstractMap. It is similar to HashMap except that it uses reference equality when comparing the elements.

几个例子很清晰
http://blog.csdn.net/stoneok07/article/details/7262676


## 集合遍历和排序

1. 遍历集合 iterator
2. 集合排序 comparator

    Iterator enables you to cycle through a collection, obtaining or removing elements. ListIterator extends Iterator to allow bidirectional traversal of a list and the modification of elements.注意后者还能双向遍历，并且可以修改集合元素。
3. listiterator，除了向后遍历next还能够利用previous向前遍历。

## List各种遍历方式效率比较

Iterable接口是1.5提出的，为了支持foreach循环。
`foreach支持的实现机理`。

## Arrays.sort方法解析

1. primitive->quicksort

2. Object->mergesort(可以传入comparator，不传入则null，必须是可比较对象 comparable)

3. 长度<7使用insertation sort

4. 注意归并的时候src和dest的来回复用，以及归并合并时的优化(提前判断是否已经有序)

这里涉及到很多排序的方法，不同jdk版本的实现也会有差异。

## Collections.sort方法解析

1. 最终调用Arrays的sort（object）方法；

2. 也可以传入比较器；


# Java集合内容2

## Java集合
### 常用集合类继承结构 
```
Collection<--List<--Vector 
Collection<--List<--ArrayList 
Collection<--List<--LinkedList 
Collection<--Set<--HashSet 
Collection<--Set<--HashSet<--LinkedHashSet 
Collection<--Set<--SortedSet<--TreeSet 
Map<--SortedMap<--TreeMap 
Map<--HashMap 
HashTable
```
### Vector、ArrayList和LinkedList使用场景比较
都会对基本数据类型进行包装，大多数情况下，从性能上来说ArrayList最好，但是当集合内的元素需要频繁插入、删除时LinkedList会有比较好的表现，但是它们三个性能都比不上数组，另外Vector是线程同步的。所以： 


1. 如果能用数组的时候(元素类型固定，数组长度固定)，请尽量使用数组来代替List； 
2. 如果没有频繁的删除插入操作，又不用考虑多线程问题，优先选择ArrayList； 
3. 如果在多线程条件下使用，可以考虑Vector； 
4. 如果需要频繁地删除插入，LinkedList就有了用武之地； 
5. 如果你什么都不知道，用ArrayList没错。 

### HashTable、HashMap、HashSet使用场景比较
1. hashtable线程安全，key和value都非空
2. hashmap单线程，可空key和空value

### 线程安全
和Vector不同，ArrayList中的操作不是线程安全的！所以，建议在单线程中才使用ArrayList，而在多线程中可以选择Vector或者CopyOnWriteArrayList。但和HashMap相比，Hashtable是线程安全的，而且它支持通过Enumeration去遍历。

### 面向接口编程思想
利用抽象编程我们返回一个接口，用接口控制，以后根据需要可以改变具体实现类而不需要改变客户端代码。

## Map小结

Java为数据结构中的映射(字典)定义了一个接口java.util.Map;它有四个实现类,分别是HashMap、Hashtable、LinkedHashMap和TreeMap。HashTable在java1.0中就出现，继承Dictionary抽象类，java1.2提出集合框架后进行了整合，实现Map接口。

Map主要用于存储健值对，根据键得到值，因此不允许键重复(重复即覆盖已有键值),但允许值重复(不同的key可以有相同的value，很好理解)。

Hashmap 是一个最常用的Map接口实现类,用于存储很多的键值对儿，它根据键的hashcode相关值进行key的位置定位,因此根据键key可以直接获取键值对儿位置并获取对应的value，具有O(1)访问速度，遍历时，取得数据的顺序是完全随机的,不一定是插入键值对儿的顺序。 HashMap最多只允许一条记录的键为Null;允许多条记录的值为Null;前面两句比较绕口，就是说hashmap允许空值的出现，key不能重复，自然只能有一个空值key，而value本身可能重复，自然可能有多条记录具的value未空。

HashMap不支持线程的同步，没有对写操作加锁，即任一时刻可以有多个线程同时写HashMap，即可能会导致数据的不一致。如果需要同步，可以用 Collections的synchronizedMap方法使HashMap具有同步的能力，或者使用`ConcurrentHashMap`。

Hashtable与 HashMap类似,它继承自Dictionary抽象类，1.2之后也实现了Map接口。与HashMap不同点:它不允许记录的键或者值为空;它支持线程的同步，即任一时刻只会有一个线程能写Hashtable,因此也导致了Hashtable在写入时效率相比HashMap低一些。

LinkedHashMap是HashMap的一个子类，保存了记录的插入顺序，在用Iterator遍历LinkedHashMap时，先得到的记录肯定是先插入的。在遍历的时候会比HashMap慢，不过有种情况例外，当HashMap容量很大，实际数据较少时，遍历起来可能会比 LinkedHashMap慢，因为`LinkedHashMap的遍历速度只和实际数据有关，和容量无关`，而HashMap的遍历速度和他的容量有关。注意实现机理。

TreeMap实现SortedMap接口，能够把它保存的记录根据键排序,默认是按键值的升序排序，也可以指定排序的比较器，当用Iterator 遍历TreeMap时，得到的记录是排过序的。

一般情况下，我们用的最多的是HashMap,在Map中插入、删除和定位元素，HashMap 是最好的选择。但如果您要按自然顺序或自定义顺序遍历键，那么TreeMap会更好。如果需要输出的顺序和输入的相同,那么用LinkedHashMap 可以实现,它还可以按读取顺序来排列.

## java HashMap关键点分析(1.8版本源码)
使用开链法存储，链长超出一定范围会进行`树式优化`。因此这里既有散列表的映射、冲突解决、扩充处理、也有二叉树操作(具体分析红黑树、平衡二叉树还是普通二叉搜索树)。小小HashMap内含乾坤，之前没有注意到其中的树操作，是后来引入的？

## HashMap 和 HashTable 对比
### 相同点
1. 都是用来存储键值对儿的散列表，设计上都是采用拉链法。
2. 源码层面都有一个table数组，数组元素为Entry类型对象(各自有一个实现Map.Entry接口的内部类)，每个Entry保存一个键值对key-value，并且引出一个单向的链表用于解决发生在该位置的冲突。

### 不同点
1. 产生背景和继承结构
Hashtable 1.0就有，JCF提出后进行了重新设计，继承于Dictionary，实现了Map、Cloneable、java.io.Serializable接口。
HashMap 提出于1.2，是JCF的一部分，继承于AbstractMap，实现了Map、Cloneable、java.io.Serializable接口。

2. 实现细节不同
默认初始容量、加载因子、扩容算法、哈希算法等都不同。

冲突处理不同：hashtable比较纯粹，开链法，利用链表存储冲突节点；hashmap会限制链长，链长超限后会进行扩容或者链-树结构优化(Node->TreeNode转化)，链变为红黑树结构。
(静态内部类TreeNode继承自LinkedHashMap.Entry,有红黑树相关的各种旋转调整操作，源码值得一读)

3. 线程安全不同
Hashtable产生于1.0,包括同时期的Vector,几乎所有方法都是同步的，即它是线程安全的，支持多线程。
而HashMap的函数则是非同步的，它不是线程安全的。若要在多线程中使用HashMap，需要我们额外的进行同步处理。 对HashMap的同步处理可以使用Collections类提供的synchronizedMap静态方法，或者直接使用JDK 5.0之后提供的java.util.concurrent包里的ConcurrentHashMap类。

4. 空值处理
Hashtable的key、value都不可以为null。
HashMap的key、value都可以为null。

5. 遍历方式
HashMap只支持Iterator(迭代器)遍历。
Hashtable支持Iterator(迭代器)和Enumeration(枚举器)两种方式遍历。
Enumeration 有两个接口方法：hasMoreElements(), nextElement()，不支持修改元素。
Iterator有hasNext(), next(), remove() 三个API接口。

6. 通过Iterator迭代器遍历时，遍历的顺序不同
HashMap是“从前向后”的遍历数组；再对数组具体某一项对应的链表，从表头开始进行遍历。
Hashtabl是“从后往前”的遍历数组；再对数组具体某一项对应的链表，从表头开始进行遍历。

## ConcurrentModificationException
1. 产生原因：java的fail-fast机制，通过iterator迭代器遍历HashMap的时候，如果通过其他方式修改了容器结构，常见的如调用HashMap的remove函数，然后iterator的next函数就会抛出此异常，这是尽早抛出异常避免非法访问策略，那么遍历的时候如何删除呢，通过iterator提供的remove函数即可。

2. 实现机制：利用modCount和exceptedModCount，通过迭代器操作的时候，加入删除等操作，会同时修改这两个参数，故其保持相等，而通过其他方式操作时，只增加前者数值，使得后者与前者不等，iterator的next等操作会首先检测这两个数值是否相等，不相等就抛出异常。

3. HashMap和HashTable遍历过程删除元素：如前所述，HashMap用iterator的remove可以删除，对于hashtable而言，遍历方式可以通过迭代器或者枚举器，通过迭代器的方式和hashmap分析思路一致，通过枚举的话则不可以，首先使用remove函数的时候会抛出unsupportedException，枚举智能遍历不支持删除，通过HashTable的remove后，继续访问比会引发并发改变异常。

4. 这些异常出现在迭代器遍历过程中集合结果变化时，通过多个迭代器遍历也可能引发此异常，思路一致，有一些改进的集合对这些会进行处理，也势必对性能有一定影响。

## java LinkedList 解析
http://www.cnblogs.com/skywang12345/p/3308807.html

1. 内部实现是双向链表，可以当做队列或者栈，有队列或者栈相关的等效方法addFirst、addLast、getFirst、getLast、removeFirst、removeLast。

2. 随机访问接口get(int index)会调用entry(int index)，根据index和长度决定从后面还是前面开始找(利用双向链表的优势)。

## JCF & Arrays
Arrays提供了对基本数据类型和对象对类型数组的一些操作，比如排序、查找、拷贝等工作。
Collections提供对List的一些操作，底层还是调用了Arrays相关方法。另外Collections提供封装其功能，可以把一个集合(List或者Map类)转换成特殊的集合，使得其具有只读或者同步属性。
 
1. binarySearch：二分查找 
2. sort：排序，这里是一种类似于快速排序的方法，不同版本源码方法有所不同，复杂度O(n * log n)，注意这里的排序是`稳定的`stable。
3. reverse：将线性表进行逆序操作。
4. rotate：以某个元素为轴心将线性表“旋转”。 
5. swap：交换一个线性表中两个元素的位置。 
6. unmodifiableXXX：转换成只读集合，这里XXX代表六种基本集合接口：Collection、List、Map、Set、SortedMap和SortedSet。如果你对只读集合进行插入删除操作，将会抛出UnsupportedOperationException异常。 
7. synchronizedXXX：转换成同步集合。 
8. singleton：创建一个仅有一个元素的集合，这里singleton生成的是单元素Set， 
singletonList和singletonMap分别生成单元素的List和Map。 
8. 空集：由Collections的静态属性EMPTY_SET、EMPTY_LIST和EMPTY_MAP表示

## 比较类集和数组
1. Collection包含了更多的结构，更多的操作，Collections也提供更为丰富的方法。
2. LinkedList和ArrayList 与数组(链表)比较，容器动态增长，数组固定大小。

## toarray & aslist方法

1. Arrays.asList(T...)方法对于传入的数组生成一个对应的固定长度的ArrayList，原数组和这个新的list共同引用对象，而且这个ArrayList是固定长度不可改变的，是Arrays$ArrayList,一个静态内部类。

2. Collections.toArray最终调用的是Collection.toArray,而Collection的toArray要看实现类具体实现的情况。
Object[] toArray()和Object[] toArray(T[]a)接口定义进行了实现细节的约束说明，应当进行深度拷贝，不传参的调用直接生成新的数组返回，传参的调用根据参数数组大小决定是否生成新数组。具体以ArrayList为例，其toArray实现的时候使用了Arrays.copyOf方法，最终会使用System.arrayCopy方法进行一组对象的拷贝。


## java-树相关数据结构
树 二叉树 完全二叉树 满二叉树 二叉搜索树 红黑树 字典树

### BST概念
Binary search trees are a fundamental data structure used to construct more abstract data structures such as sets, multisets, and associative arrays。

### Java-TreeMap
1. TreeMap基于红黑树（Red-Black tree）实现。该映射根据其键的自然顺序进行排序，或者根据创建映射时提供的 Comparator 进行排序，具体取决于使用的构造方法。
2. TreeMap的基本操作 containsKey、get、put 和 remove 的时间复杂度是 log(n) 。
另外，TreeMap是非同步的。 它的iterator 方法返回的迭代器是fail-fast的。

### 红黑树VS二叉排序树
1. 首先红黑树是改进的二叉排序树
2. 避免不平衡导致退化成链表的情况

### 红黑树
1. 红黑树的应用比较广泛，主要是用它来存储有序的数据，它的时间复杂度是O(lgn)，效率非常之高。
例如，Java集合中的TreeSet和TreeMap，C++ STL中的set、map，以及Linux虚拟内存的管理，都是通过红黑树去实现的。

2. 对于Java-TreeMap 而言，由于它底层采用一棵“红黑树”来保存集合中的 Entry，这意味这 TreeMap 添加元素、取出元素的性能都比 HashMap 低（红黑树和Hash数据结构上的区别）：当 TreeMap 添加元素时，需要通过循环找到新增 Entry 的插入位置，因此比较耗性能；当从 TreeMap 中取出元素时，需要通过循环才能找到合适的 Entry，也比较耗性能。但 TreeMap、TreeSet 比 HashMap、HashSet 的优势在于：TreeMap 中的所有 Entry 总是按 key 根据指定排序规则保持有序状态，TreeSet 中所有元素总是根据指定排序规则保持有序状态。

3. 为什么TreeMap采用红黑树而不是二叉查找树？
最坏的情况下：如果插入的节点集本身就是有序的，要么是由小到大排列，要么是由大到小排列，那么最后得到的排序二叉树将变成链表：所有节点只有左节点（如果插入节点集本身是大到小排列）；或所有节点只有右节点（如果插入节点集本身是小到大排列）。在这种情况下，排序二叉树就变成了普通链表，其检索效率就会很差。为了改变排序二叉树存在的不足，Rudolf Bayer 与 1972 年发明了另一种改进后的排序二叉树：红黑树，他将这种排序二叉树称为“对称二叉 B 树”，而红黑树这个名字则由 Leo J. Guibas 和 Robert Sedgewick 于 1978 年首次提出。红黑树是一个更高效的检索二叉树，因此常常用来实现关联数组。典型地，JDK 提供的集合类 TreeMap 本身就是一个红黑树的实现。

4. 红黑树在原有的排序二叉树增加了如下几个要求：
```
性质 1：每个节点要么是红色，要么是黑色。
性质 2：根节点永远是黑色的。
性质 3：所有的叶节点都是空节点（即 null），并且是黑色的。
性质 4：每个红色节点的两个子节点都是黑色。（从每个叶子到根的路径上不会有两个连续的红色节点）
性质 5：从任一节点到其子树中每个叶子节点的路径都包含相同数量的黑色节点。
{
Java 实现的红黑树
上面的性质 3 中指定红黑树的每个叶子节点都是空节点，而且并叶子节点都是黑色。但 Java 实现的红黑树将使用 null 来代表空节点，因此遍历红黑树时将看不到黑色的叶子节点，反而看到每个叶子节点都是红色的。
}
```
红黑树通过上面这种限制来保证它大致是平衡的——因为红黑树的高度不会无限增高，这样保证红黑树在最坏情况下都是高效的，不会出现普通排序二叉树的情况。
由于红黑树只是一个特殊的排序二叉树，因此对红黑树上的只读操作与普通排序二叉树上的只读操作完全相同，只是红黑树保持了大致平衡，因此检索性能比排序二叉树要好很多。
但在红黑树上进行插入操作和删除操作会导致树不再符合红黑树的特征，因此插入操作和删除操作都需要进行一定的维护，以保证插入节点、删除节点后的树依然是红黑树。二叉查找树(二叉排序树)中我们对元素删除和插入花费时间为O(h)，而在红黑树中却为O(lgn)，这是因为在经过二叉查找树的那些操作后，红黑树为了保证其特有性质必须进行树的旋转。

## TreeMap、TreeSet 对比 HashMap、HashSet的优缺点
缺点：
    对于 TreeMap 而言，由于它底层采用一棵“红黑树”来保存集合中的 Entry，这意味这 TreeMap 添加元素、取出元素的性能都比 HashMap （O(1)）低；当 TreeMap 添加元素时，需要通过循环找到新增 Entry 的插入位置，因此比较耗性能（O(logN)）。当从 TreeMap 中取出元素时，需要通过循环才能找到合适的 Entry，也比较耗性能（O(logN)）
优点：
    TreeMap 中的所有 Entry 总是按 key 根据指定排序规则保持有序状态，TreeSet 中所有元素总是根据指定排序规则保持有序状态。


# jdk源码中的设计模式

# 其他

## Java基本数据类型
1. 整数都是有符号数
2. char是2字节的unicode值，属于字符型数据(也可被当作unsigned short int型数据进行运算，但是直接输出是字符)

```
    byte, 8 bit
    short, 16 bit
    int, 32 bit
    long, 64 bit
    float,32bit
    double,64bit
    char, 16bit
    boolean，不明确，跟实现有关。
```
3. 每种基本类型都有一个对应的封装类型，注意char对应的是Character，其余都是对应首字母大写的类(byte-Byte，short-Short....)。

## String、StringBuilder、StringBuffer
1. String，final类型，是字符串常量(常量池相关 intern用法需要了解)，后两者变量。
2. StringBuffer-线程安全-长度可变 append和insert操作 
3. StringBuilder-非线程安全-长度可变
4. `那么String的操作线程安全吗？`安全的，属于内容不可变类。
5. 编译器会对代码进行优化、有些拼接String并不会降低那么多效率(看上去会)，因为编译器对代码进行了优化。
6. String的hashCode利用了延迟初始化(lazy initialization)，又由于不可变的，利用了缓存技术，避免了重复的复杂计算。

## java hashCode函数
http://en.wikipedia.org/wiki/Java_hashCode()

http://my.oschina.net/chihz/blog/56256

## hashcode再次研究 搜索 “高效地hashmap”
http://blog.23lab.com/blog/2013/10/31/cong-hashcodetan-qi/

1. eclipse中有自动生成hashCode和equals方法的功能 ，其方法是effective java中提出的方法。
2. Object的hashCode()方法其实就是根据对象的物理地址生成的hash值，Object的equals方法默认是判断物理地址是否相等(this==obj)。Object的hashCode方法是native的。

## hash算法散列实现，各种方法

    http://blog.csdn.net/jnu_simba/article/details/9631445
    31*m+n  31 乘法可以转换成 左移5次减1
    
1. 冲突处理链地址(拉链法)http://blog.csdn.net/jnu_simba/article/details/9632675
2. 冲突处理开地址法(线性探测再散列) http://blog.csdn.net/jnu_simba/article/details/9664053  H0=Hash(x) Hi= (H0+di)%m
3. 冲突处理开地址法(二次探测再散列)http://blog.csdn.net/jnu_simba/article/details/9668369   H0=Hash(x) Hi= (H0+/- i^2)%m    m表大小 4k+3质数

## 十种排序比较
http://blog.csdn.net/jnu_simba/article/details/9705111

## 排序动画
https://blog.csdn.net/rocling/article/details/82832998
https://blog.csdn.net/rocling/article/details/82832998#1%E3%80%81%E5%86%92%E6%B3%A1%E6%8E%92%E5%BA%8F%EF%BC%88Bubble%20Sort%EF%BC%89


## C++常见数据结构以及算法
http://blog.csdn.net/jnu_simba

## java强弱引用介绍
http://blog.csdn.net/mazhimazh/article/details/19752475

强>软>弱>虚

## 文件操作

## 内存泄漏场景

## transient变量不可序列化

## 序列化和克隆是标志接口
接口没有实际的方法，只是用于标识这个类是可序列化的或者是可克隆的?

1. object有一个clone方法，Object中这个clone方法是protected访问级别，因此每个类都会继承到一个clone方法，对于没有实现cloneable这个接口的类即使调用clone方法也会报错，在native的clone方法中会判断是否实现克隆接口。实现这个接口意味着我们要重写clone方法，重点是要改为public访问权限，可能会定制特殊的clone策略，super.clone(shallow copy)应该被调用，当然也可以彻底自己实现所有过程(比如可以使用拷贝构造函数)。数组被认为默认实现了cloneable接口，Object自身没有实现这个接口。

2. serialization接口没有任何方法和字段，只是一个语义标识。(注意涉及到序列化id、writeObject、readObject等相关内容)


## Arraylist的clone是shallow copy

源码中有说明，数组中的对象本身并没有复制，被两个数组引用着。
首先调用super.clone()，然后利用Arrays.copyOf方法，最终利用system.arrayCopy进行复制，注意这里元素本身是共享的。

## Linkedlist的clone是shallow copy

源码中有说明，数组中的对象本身并没有复制，被两个数组引用着。
首先迪奥用super.clone()，然后遍历链表元素对clone后的链表进行元素添加，这里元素也是共享的。

## Arrays.copy 和 数组clone的比较

## 对象clone和拷贝构造函数性能比较？

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

## fast-fail & happens before
## readObject、readResolve以及序列化、不可变类
## CountDownLatch-可变类但被限制状态空间
## volatile关键字
## transient关键字
##
1. 锁等待时线程的状态
2. wait notify
3. Executor
4. java线程的状态{new running blocked waiting timed-waiting terminated }(https://docs.oracle.com/javase/10/docs/api/java/lang/Thread.State.html)
5. synchronized {lock and monitor}
6. java实现生产者消费者模型(wait notify? 其他类型的高级锁？)
7. wait和sleep
8. fail-fast&fail-safe  / collection in java.util. & java.util.concurrent
9. ConcurrentHashMap源码分析
10. comparable & comparator 两个接口
11. TreeSet
12. System.gc() & Runtime.gc()
13. java heap & permernant generation space
14. garbage collector
15. Exception & Error  / Checked & Unchecked Exception
16. final/finally/finalize

## 深入研究技术点
1. 源码中sort实现，双基准快排、tim-sort等
2. 集合迭代器的实现，iterator和foreach比较
3. java中树结构

# Java知识图谱-wenburgyan
1. 基本概念(基本数据类型、操作符)
2. 面向对象(继承、封装、抽象、接口)
3. JCF-集合类源码分析
4. 设计模式(jdk源码实例)
5. 泛型
6. 序列化
7. 注解
8. 多线程、同步、并发、锁
9. JMM-java memory model 内存模型、见JLS17.4详细讲解(重排序、happens-before)
10. JVM相关(垃圾回收机制、类加载过程、内存结构)


- 算法类
- 数据结构类
- 语言语法类
- 面向对象相关
- java内存模型&垃圾回收机制
- java序列化
- java反射
- java线程相关
- java并发编程
- java源码探究
- 设计模式相关
- java网络相关
- java8学习