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

## DroidPlugin对四大组件的支持
1. Activity，通过在manifest.xml中进行占位，躲过安全检查，然后对AMN和ActivityThread中的H.Callback进行hook，调用方在安全检查前替换插件Activity名为占位名，实际处理启动的地方将占位名复原为实际的插件Activity名。不同技术hook点的选取会有所不同，不同的版本hook点的选取也会有差别，都是对私有api的调用，sdk版本的升级可能导致hook不可用。这里还涉及到对Resources的替换，对Instrumentation的callActivityOnCreate进行hook。
2. Service，一样占位。类似Activity。
3. BroadCastReceiver，通过解析将静态广播都转换为动态广播后通过代码Register上。
4. ContentProvider，ActivityThread的installContentProviders方法是一个安装插件provider到宿主的实际。