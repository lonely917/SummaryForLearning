<!-- TOC -->

- [Android插件化](#android插件化)
    - [进程间通信](#进程间通信)
    - [Binder通信](#binder通信)
    - [ClassLoader](#classloader)
    - [资源处理](#资源处理)
    - [apk生成](#apk生成)
    - [插件化技术概览](#插件化技术概览)
    - [插件化关键技术](#插件化关键技术)
    - [开源插件动态化技术追踪](#开源插件动态化技术追踪)
    - [插件化发展进程中的一些资料](#插件化发展进程中的一些资料)
    - [DroidPlugin对四大组件的支持](#droidplugin对四大组件的支持)
    - [代理技术的一些模式](#代理技术的一些模式)

<!-- /TOC -->
# Android插件化

## 进程间通信
1. 文件共享
2. Bundle
3. AIDL
4. Messenger
5. ContentProvider
6. Socket

其中AIDL,Messenger,ContentProvider都是基于Binder实现。Binder基于mmap操作，只需要一次内存拷贝即可实现通信。微信开源MMKV存储方案也是基于mmap操作。

## Binder通信
ServiceManager对应0号binder，相当于编号固定。其他的服务通过SM进行注册，注册后可以被客户端通过SM查询到地址，进而使用地址获取对应的服务调用。服务注册的过程SM是服务端，注册服务的调用方是客户端；查询服务的时候，查询服务方是客户端，SM是服务端；使用服务的时候，使用服务方是客户端，对应的service所在进程/线程是服务端。Binder驱动是内核相关binder交互的基础。Android每个进程也都会初始化一些binder线程，都是服务于binder通信这个整体框架的。

## ClassLoader
1. Java中的classLoader:Bootstrap ClassLoader；Extention ClassLoader;App ClassLoader;(java9之后只有启动类加载器和模块类加载器)
2. Android中的classLoader(5.0之后art虚拟机):BootClassLoader;PathClassLoader;DexClassLoader;其中DexClassLoader可用于动态加载。

## 资源处理
1. 资源文件对应的R.java中R.xx.xx生成规则，资源id的编号含义“空间-类型-编号”。
2. 自适应打包，buildVariant/buildType/productFlavor/sourceSet概念。
3. module引用、jar包引用、aar引用。
4. 0x7f开头和0x01开头的资源
5. 不同gradle处理流程可能会有差异

## apk生成
1. aapt对资源处理->R.java和编译后的资源(resource.arsc)
2. java编译器对R.java、源代码处理->class文件
3. dex对class files还有三方class文件处理生成dex files
4. apk builder对编译后资源、dex files、三方资源处理->apk
5. jarsigner处理apk生成signed-apk

其中2中的源代码不仅包含应用手写的源代码，还有由aidl文件生成的java类或接口文件(生成过程在2之前一ing该就已完成)。其中3对class文件集合进行聚合以及压缩，可能出现65536问题，通过multidex进行解决，生成的dex就不止一个了。其中4生成的apk最后会根据场景进行签名以及对齐等操作。其中5签名可以通过两种形式一种是jarsigner，另一种比较新的方式Signature Scheme V2。

`混淆发生的环节?`

涉及到的一些技术点：
1. 指令重排，优化启动速度。
2. apk混淆，安全性以及包体积优化。标识换短名以及无用资源删除。Proguard vs AndResGuard(微信推出，可以处理资源文件的混淆)
3. zipalign，执行优化。
4. 插件化技术-模块资源id的定制化修改。
5. ButterKnife等框架的使用、 双向绑定技术的使用。
6. Transform技术(android gradle plugin提供的api)，可在dex过程之前对class进行字节码处理。 AspectJ/Javasist/ASM等框架。
7. 多渠道打包的优化。productflavor途径改为v2签名写入额外信息。(美团Walle框架)

## 插件化技术概览
1. AndroidDynamicLoader，fragment动态替换的思想。
2. DLA(任玉刚)/DroidPlugin(张勇)，前者继承ProxyActivity思想，后者Hook系统框架的思想。
3. VirtualApk(hook点选取，manifest文件四大组件预埋)
4. VirtualApp/Atlas(提高兼容性和稳定性，容器化框架的思想)

## 插件化关键技术
1. 插件调用。(apply plugin:com.android.library 与 apply plugin:com.android.application的不同)。动态加载dex，classloader的管理。
2. 资源管理。Resources和AssetManager的处理。关于id冲突可以修改aapt对插件资源id进行修改或者根据插件的使用调用对应的Resources和AssetManager。
3. AMS和PMS的hook

## 开源插件动态化技术追踪
1. AndroidDynamicLoader，github最早提交记录在2012年，github账号mmin18，有很多热门项目。插件通过Fragment代替Activity。
2. DLA，github最早提交记录在2014年初，csdn也有博客介绍，账号singwhatiwanna，任玉刚。利用Activity占位以及代理的思想。
3. Android PluginManager，github最早提交记录在2014年11月，账号houkx，csdn也有一篇原理介绍对应dev分支，利用dexmaker动态生成dex字节码的相关技术，为插件Activity动态生成代理类；master分支舍弃了dexmaker的使用，对ActivityThread、Instrumentation等进行了hook。
4. DroidPlugin，360出品，作者账号cmzy(Andy Zhang)，张勇，最早提交追溯到2015年。目前依然在更新活跃的项目。
5. RePlugin，360出品的另外一款产品，号称功能更强大，更稳定，只使用唯一hook点，不同团队的产品， 作者张炅轩，主分支最后一次提交2019年7月，目前依然在活跃中，最早开源提交记录时间为2017年6月，据宣传称比DroidPlugin研发时间更早，2013年开始做这块儿。
5. CtripMobile/DynamicAPK，携程技术，2015年10月份提交，最后一次提交2015年11月份。有对此前技术的一些分析对比。
6. VirtualApk，滴滴出品，singwhatiwanna任玉刚主笔，最早追溯到2017年6月，最后一次提交18年12月。Hook和预埋的思想。
7. Atlas，阿里巴巴开源项目，容器化框架/动态组件化框架，最早提交记录2017年3月，主分支最后一次提交与2019年7月。目前仍在活跃中。
8. VirtualApp，APP沙箱运行，可实现APP多开，免安装运行等。作者lody，最早提交于2016年7月，一直在维护更新，但是目前商业化，开源的是早期的版本。
9. Shadow，腾讯零反射全动态android插件化技术。最早提交记录2018年4月，目前依然活跃中。

## 插件化发展进程中的一些资料
1. dla系列文章，github以及csdn
2. droidplugin实现思路,github由详细说明以及分析资料(其中就有tianweishu和hejunlin的博客分析)
3. 携程技术，https://www.infoq.cn/article/ctrip-android-dynamic-loading/
4. 手淘组件化之路，https://mp.weixin.qq.com/s?__biz=MzAxNDEwNjk5OQ==&mid=2650400348&idx=1&sn=99bc1bce932c5b9000d5b54afa2de70e
5. 支付宝mpaas系列

## DroidPlugin对四大组件的支持
1. Activity，通过在manifest.xml中进行占位，躲过安全检查，然后对AMN和ActivityThread中的H.Callback进行hook，调用方在安全检查前替换插件Activity名为占位名，实际处理启动的地方将占位名复原为实际的插件Activity名。不同技术hook点的选取会有所不同，不同的版本hook点的选取也会有差别，都是对私有api的调用，sdk版本的升级可能导致hook不可用。这里还涉及到对Resources的替换，对Instrumentation的callActivityOnCreate进行hook。
2. Service，一样占位。类似Activity。
3. BroadCastReceiver，通过解析将静态广播都转换为动态广播后通过代码Register上。
4. ContentProvider，ActivityThread的installContentProviders方法是一个安装插件provider到宿主的实际地方。

## 代理技术的一些模式
1. 代理模式的运用，偷梁换柱亦或瞒天过海
2. 静态代理与动态代理，前者自定义代理类，后者是基于接口来生成代理类，利用Proxy.newProxyInstance方法生成代理类，进一步可以研究下newProxyInstance的实现(相关虚拟机指令)以及InvocationHandler方法拦截的使用。(retrofit中就是利用了代理的思想)
3. cglib生成代理类利用继承的思想覆盖父类方法，不要求类实现某一接口，应用场景更更广泛，底层利用asm字节码操作技术。但也有限制，由于基于继承，不能实现对final方法的修改。
4. 编译器生成代理类或者字节码操作技术
5. 运行时生成代理类或者字节码操作技术