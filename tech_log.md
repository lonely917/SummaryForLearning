#dexoption 增大编译过程jvm大小
android studio编译项目过程出错：Uncaught translation error: java.util.concurrent.ExecutionException....

#js与webview
http://blog.csdn.net/carson_ho/article/details/64904691
1.代码调用js
2.js调用本地函数
webview内执行网页跳转，重写webviewclient的shouldoverrideurl方法
webviewclient和webchromeclient
重写返回，webview能够回退的时候优先进行回退而不是关闭页面。

#从中英文混合中找到倒数第一个中文位置
unicode utf-8 编码格式 java的String中的每个char是两字结

#activity和fragment的startactivityforresult
fragmentactivity对fragment的startactivityforresult进行了重新设计，对requestcode进行一层处理，通过requestcode来区分是由fragment还是activity发起的，
在activity的onActivityResult监听中通过这个字段判断，并复原，路由到activity或者fragment的对应处理中。fragment的startactivityforresult最终
也是activity的start方法，但是会对requestcode进行转换。

但是可以重写activity的结果处理方法，指定调用某个fragment的结果处理方法。

要想在fragment中启动新的acticity，并且收到结果返回。两种方式：1 调用fragment的startactivity，并在自己的fragment中进行监听，其他默认。
2.通过 getActivity进行activity级别的intent调用，然后在activity的onactivityresult进行主动处理事件结果，路由给指定的fragment。

#apk打包签名，重新签名
指定密钥，输出名称，被处理apk 别名
jarsigner -verbose -keystore aisinoxfb.jks -signedjar test.apk TestSign.apk aisino
然后输入密码后对资源进行处理生成结果。

查看密钥信息
keytool -list -keystore xx.keystore 
keytool -list -v -keystore xx.keystore 

查看安装包里的证书信息，解压文件
keytool -printcert -file META-INF/CERT.RSA

生成密钥
keytool -genkey 。。。。。。

默认debug.keystore相关信息

    Keystore name: "debug.keystore"
    Keystore password: "android"
    Key alias: "androiddebugkey"
    Key password: "android"
    CN: "CN=Android Debug,O=Android,C=US"


#gradle新增指令
http://blog.csdn.net/marvinhq/article/details/73477128
传统comlipe，改为implement和api

#android屏幕适配
wrapcontent尺寸计算原理
dp在设备上的计算原理
http://www.jb51.net/article/112794.htm
http://blog.csdn.net/twilight041132/article/details/43029015


#proguard 可能导致第三方库使用失效，合理的混淆设置
如果release版本有问题，debug版本正常，很有可能就是混淆导致了问题。
build文件开启代码混淆，自定义混淆规则
//代码混淆
minifyEnabled true
proguardFiles 'proguard.cfg'

自定义的proguard.cfg
//#fastjson
-dontwarn com.alibaba.fastjson.**
-keep class com.alibaba.fastjson.** { *; }

#huiyi
#-libraryjars libs/huiyiusdkinterface.jar
#-libraryjars libs/yposPayServiceInterface.jar
#-dontwarn    com.huiyi.nypos.usdk.*

#对根据包名，避免进行混淆
-keep class  com.huiyi.nypos.usdk.** { *;}
-keep class com.huiyi.nypos.pay.thirdpay.**{*;}

#proguard的原理，为什么包的体积变小了？
手册
https://stuff.mit.edu/afs/sipb/project/android/sdk/android-sdk-linux/tools/proguard/docs/index.html#manual/introduction.html

一些混淆示例
http://blog.csdn.net/doris_d/article/details/52609703

http://www.cnblogs.com/jymblog/p/6531955.html



#android 签名机制
签名过程和原理以及作用


#onActivityResult和onResume

http://blog.csdn.net/barryhappy/article/details/53229238     
fragmentactivity startactivity
onactivityresult

官方文档
You will receive this call immediately before onResume() when your activity is re-starting.
so onResume then onActivityResult

fragment类似去看说明

#leakCanary
https://www.liaohuqiu.net/cn/posts/leak-canary-read-me/

#工具类
http
dialog
image
Log

#EditText焦点问题
1.自动弹出输入法问题。利用父布局抢夺焦点，使得输入框点击的时候再弹出键盘
2.弹出键盘遮蔽输入框的问题。可以利用scrollview包裹整体activity的layout，或者利用window的softinput等属性设置方法，也可以在manifest文件设置。

#程序的完全退出，后台线程，定时任务等都应该关闭。

#bigdecimal  setscale
各种进位方式

#fragment和activity通信，方法，变量访问。

#AppCompatActivity

#jackson fastjson gson org.gson
性能和原理，序列化和反序列化。

#xml xstream

#混淆后可能导致的问题
fastjson对实体类进行处理，混淆时注意把实体类排除，否则fastjson相关toJsonString会出问题

#sqlite android 数据库降级默认会导致程序运行出错
默认方法如下，抛出异常。一定要逆向降级，需要重写此方法，不能调用super。
    public void onDowngrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        throw new SQLiteException("Can't downgrade database from version " +
                oldVersion + " to " + newVersion);
    }

数据库初始化部分的逻辑都在SQLiteOpenHelper的getDatabaseLocked，这个方法是私有的，会通过getReadableDatabase对外展示。
这个方法里面有数据库建立以及升级降级等处理流程框架。相关模块在子类中实现具体行为。比如升级降级的处理，数据库的建立等。


#app代码安全问题
>在使用Intent.parseUri解析uri之后，获取到的intent必须严格过滤，intent至少包含addCategory(“android.intent.category.BROWSABLE”)，setComponent(null)，setSelector(null)3个策略
https://www.2cto.com/kf/201604/501880.html
上述是一个缺陷场景，网页调起一个activityA，这个activity获取参数并调用一个带有webview的activityB同时传递获取的参数，网页可以传一个钓鱼网页。当然这在于缺乏对参数的控制，以及activityA、B的设计结构。
浏览器intent scheme url可以利用漏洞Intent.parseURI 对component进行设置。
在Webview加载load_url时，结合APP的自身业务采用白名单机制过滤网页端传过来的数据，黑名单容易被绕过。
为了安全起见，使用网页传过来的intent时，还是要进行过滤和检查。
Intent.parseURI()

>so文件存在被调试的风险，攻击者可以利用此风险对应用进行动态调试，造成核心逻辑和敏感数据等信息泄漏。

>webview js交互漏洞
http://blog.csdn.net/zhangcanyan/article/details/51930775
相关设置
addJavascriptInterface方法在api17之前和之后
api17之下不应使用此方法，官方说明https://labs.mwrinfosecurity.com/blog/webview-addjavascriptinterface-remote-code-execution/
也有提出在17之前可以利用webchromeclient的onJsPrompt来进行安全交互


#md5 
撞库 生日攻击 md5+salt bcrypt
密码明文存储->密码md5后进行存储->密码+一定长度随机salt后md5进行存储，每个用户有对应的salt数值，重置密码的时候才可能变更。
salt的引入有两点：1.扩展密文，使其对应值域偏差，不落在常见密码库对应的md5库中 2.随机的salt使得不同密码扩展不一样，这样增大了撞库的复杂度，增大了样本空间。
salt固定的话缺点之一：假设对用户A进行撞库，运用常用密码库，得到saltA对应的md5库，如果salt是固定的，那么这个库就可以去比对其他用户了，很有可能获得很多可破解密码，
但是如果每个用户的salt是不一样的，那么上述结果得到的库对于其他用户基本不会适用。

生日原理：如果一个房间里有23个或23个以上的人，那么至少有两个人的生日相同的概率要大于50%。对于60人以上，这种概率要大于99%。
注意这是发生一次碰撞，不是给定一个值去找一个碰撞。

md5 通常所说的16位 32位是指多少位16进制字符串，16位对应32位的中间16位，substring(8,24). 16位实际是8字节，32位实际是16字节，即128比特，可以理解为数据到128bit的一个hash
java的md5以及aes源码

#http android 基础框架多种实现

#apk生成的详细过程，签名，apk安装的详细过程，签名验证，app启动运行过程，android 系统运行过程

#java 和 android的异常处理，vm运行分析，何处设置的异常处理句柄，线程和主线程异常处理，android源码，以及watchdog机制

#webview
设置webviewclient，给webview添加一个新的webviewclient，shouldOverrideUrlLoading方法返回值的问题，true表示程序处理，不再给webview默认处理，false表示程序没处理，信号会传到此webview，交由其默认处理。定义自定义的行为可以和true进行结合。如果没有添加webviewclient，多数会掉起浏览器处理，activitymanager寻找合适的对象处理事件。

注意此方法有两个版本，参数不一样，一个方法带有request参数，另一个只有url。在跳转的时候，最好交由系统处理，自定义处理比如调用view.loadurl（）不能合适的处理referrer头参数，可能导致页面取不到refer，实践中遇到过这种情况。


----------purezhihuD----------
https://github.com/laucherish/PureZhihuD

#Application，相关周期

#ctrl +Q  quickdoc
配置文件
C:\Users\ywb\.AndroidStudioPreview3.0\config\options\jdk.table.xml
编辑对应项目

#ConnectivityManager.java  
    private final IConnectivityManager mService;
android系统启动过程，其中的aidl调用，android client和server

#ThreadLocal源码分析，多线程问题

#logger的同步问题，内置系统log多线程情况下的处理？
每次log操作调用了本地方法，本地方法的操作的同步性和原子性问题？

#java获取当前方法名的途径
http://blog.csdn.net/supercyclone/article/details/18599577
this.getClass().getName();
new   Exception().getStackTrace()[i].getMethodName(); 0是当前代码所处方法名
Thread.currentThread().getStackTrace()[1].getMethodName(); 具体索引可能跟jdk版本有关

#Serializable parcelabel

#使用到的开源库
一个log库，对android.util.log的封装，文中又进行了一定得修改
https://github.com/orhanobut/logger
另一个国产库，https://github.com/pengwei1024/LogUtils

#AppCompatActivity & activity

#activity和fragment周期，详细，以及如何被系统执行进入不同阶段

#在activity内部添加一个静态的方法，启动此activity，这种设计的意义何在？

#工具类
xml，json实体解析生成
http
db
sp
pic
toast
dialog
progress dialog or bar

#glide retrofit gson okhttp rx recyclerView 
snackbar
listview源码和recycleview对比

#fragment的意义，减少了大部分的activity又如何，仅仅是一种轻量级的实现？

#提示找不到native函数以及多个zipentry的问题
多个zipentry是由于多个jar包冲突，删除或者整合。
找不到native是因为so文件缺失。
还有abifilter的设置，一般一个就可以兼容，so也要注意对应版本的兼容性，32位64位。

#lib包和libs包，eclipse对dependency引用的包添加源码关联

#插件化和组件化，热更新和增量更新

#app托管平台
蒲公英，frm，pre

#区块链和智能合约

#shadowsocks/shadowsocks-android
kotlin方法

#APPcompat和fragmentActivity

#edittext焦点问题，自动弹出键盘的解决
一种方式是在其parent布局中设置foucusable和focusableintouchmode为true
另一种方式是设置activity的windowsoftinputmethod 为 statehiden
注意stateresize会使得软键盘的弹出附带布局向上移动的效果。

#window notitle的设置
方式1
oncreate中setcontentview之前执行设置requestFeature(Window.FEATURE_NO_TITLE);
方式1在程序启动的时候还是看到标题栏，闪一下。
方式2
设置theme的属性，添加 <item name="android:windowNoTitle">true</item>

#splash的实现
启动activity的制作，初始化行为。
一般初始化会有黑屏或者白屏(也可以设置透明)，这里自动会有一段延时，而且会跟布局的差异时间持续有差别。
要想这个地方也有背景，可以在activity的theme主题中修改背景属性
        <item name="android:windowNoTitle">true</item>
        <item name="android:windowBackground">@drawable/splash</item>
这样就控制了启动初始时间段的显示图片。
然后在oncreate结束地方调用跳转到主activity中去，可以设定延时跳转。

##intent的bundle
data.getExtras().toString()
注意，这里面的bundle的map，在读写的时候会触发一次序列化数据填充的过程，因此在onActivityResult的data里面，调试的时候可能发现里面bundle的map是空的，在调用hasExtra或getStringExtra的时候确可以得到数据，相当于首次调用出发map的填充过程。


#adb shell
关闭指定活动
adb shell am force-stop "包名"
启动指定活动
adb shell am start -n "包名/启动类的名称"
列出安装程序
adb shell pm list package 列出
列出进程
adb shell ps

# Edittext软键盘输入弹出的问题，以及键盘是否影响布局上移的问题
一、代码中调用 setSoftInputMode()方法进行设置：

OnCreate方法里面加下面这句代码 ，很管用，而且再点EditBox也能让输入法正常弹出。。 
getWindow().setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_STATE_ALWAYS_HIDDEN);


二、 在AndroidManifest.xml文件中的android:windowSoftInputMode属性使用：

在 AndroidManifest.xml文件中的activity节点下添加：android:windowSoftInputMode="stateHidden|adjustPan" 

stateHidden：是隐藏软键盘的 

adjustPan：是保证控件不会因为输入法的弹出而发生形变的。 

# surfaceview 周期以及游戏相关

# handler

# Android 跨进程调用途径和原理
intent、广播、aidl。
intent 的action和uri跳转。
网页调用app。


# 自定义控件
组合，继承，绘制

# activity启动模式

# adb shell 启动关闭应用
adb install XX.apk
adb install -r XX.apk
adb uninstall com.spdb.apiservice.test
unstall -k 包名

adb shell am force-stop
adb shell am start -n 包名/启动类名称

# 退出adb shell
如果ctrl+c不能退出adb shell则使用exit命令。

# action 和 category

android.intent.action.MAIN
android.intent.category.LAUNCHER
android.intent.category.HOME

taskAffinity和process属性

adb shell dumpsys activity查看当前任务栈情况

各种启动模式
https://blog.csdn.net/ljz2009y/article/details/26621815

<action android:name="android.intent.action.BOOT_COMPLETED"/>

# Context & Activity

# 打印驱动的几种模式
1. 有deviceServiceApi服务层，利用aidl调用，aidl相关类以及上层方法可以打成jar包，一般不涉及so文件，服务层apk中会用到so库，底层硬件调用。
2. 一个jar包封装接口和底层操作，so库提供支持，所以一般一个jar包一个so库。
上述结论待核实

# settext null的处理
EditText 的 settext以及Toast的toast都会到TextView的setText(CharSequence text, BufferType type,boolean notifyBefore, int oldlen)方法，会对传入数值判断，null则变为""。

# gradle相关目录
1. 下载第三方依赖库
.gradle\caches\modules-2\files-2.1

2. gradle不同版本的目录
.gradle\wrapper\dists

# 打包aar以及依赖本地aar
allprojects {
    repositories {
        jcenter()
        maven { url "https://jitpack.io" }
        flatDir{dirs 'libs'}
    }
}

compile (name:'printerutils-debug',ext:'aar')

# 引入多个jar包带来的jar包冲突，或引入aar和项目其他jar包冲突，aar的生成是否包含其依赖包也是一种考量。
https://blog.csdn.net/jiujiedexiaoming/article/details/76520376

# 构建app最小系统

# FragmentActivity

# so库和abi架构
https://www.cnblogs.com/Bugtags2015/p/5578541.html


# Context源码分析
```
Activity-ContextThemeWrapper-ContextWrapper-Context
                                    |            |__abstract getApplicationContext()
                                    |__Context mBase 
                                    |__getBaseContext()
                                    |__getApplicationContext()
                                    |__attachBaseContext(Context base)
```
`Activity Service Application 与 ContextWrapper Context ContextImpl这样层次设计的目的?代理，包装，设计缘由?`

attachBaseContext的引用，这是一个关键操作，涉及到真实getapplicationcontext的具体实现。Activity,Service,Application实例创建后，最终都会通过attachBaseContext关联一个contextImpl对象。可以从ActivityThread出发进行分析：
- performLaunchActivity -> 
ContextImpl.createActivityContext 创建ContextImpl的实例对象，通过activity的attach方法最终调用attachBaseContext，将activity关联上contextImpl.->
activity.onCreate会被调用


- handleCreateService ->
 ContextImpl.createAppContext -> 
 service.attach 将service关联上contextImpl.->
 service的onCreate会被调用


- handleBindApplication -> 
loadedapk.makeApplication -> 
ContextImpl.createAppContext创建ContextImpl的实例对象->
mActivityThread.mInstrumentation.newApplication(cl, appClass, appContext)创建Application对象->
newApplication方法中通过app.attach 进行application和ContextImpl的关联->
instrumentation.callApplicationOnCreate(app);最终调用application的onCreate.


Activity、Application、Service都用到这个操作。
ContextImpl和ActivityThread两个类不在android sdk系统中，未提示这里的引用，但这两个文件很关键。
`context的实际实现是ContextImpl`。

context的使用，可能是一层一层的包装。注意几点：
1. Activity、Service、Application何时初始化context的 => 和2、3、4关联
2. ActivityThread的各种handle和perform，比如handleBindApplication，performLaunchActivity.
3. LoadedApk的makeApplication
4. ContextImpl的createSystemContext、createSystemUiContext、createAppContext、createActivityContext，被调用的创建ContextImpl的方法。
5. Activity的getApplication 和 getApplicationContext最终返回应该是一样的，都是唯一的Application的对象。(?`Application对象只能有一个吗？`)

context.getSystemService源码分析；
LayoutInflater提供了cloneInContext()函数的用处；

context简单解析:
https://blog.csdn.net/singwhatiwanna/article/details/21829971
https://blog.csdn.net/chunqiuwei/article/details/50068141
https://my.oschina.net/youranhongcha/blog/1807189


```java
    attachBaseContext(Context)
    Found usages
    Activity.java
    Application.java
    BackupAgent.java
    ContextThemeWrapper.java
    EphemeralResolverService.java
    MediaBrowserServiceCompatApi21.java
    MediaBrowserServiceCompatApi21.java
    MultiDexApplication.java
    NotificationListenerService.java
    NotificationRankerService.java
    PrintService.java
    RecommendationService.java
    RuntimePermissionPresenterService.java
    Service.java
```

关注ContextImpl和ActivityThread的流程，探索`apk安装以及启动全过程`。

app启动-注意launcher process - system process - app process结合思考。

Binder与AMS流程。AMS负责四大组件生命周期。
https://blog.csdn.net/bfboys/article/details/52564531
https://www.jianshu.com/p/e69d22ec0582

## ServiceManager、AMS、XXMS、Binder相关
这是一些很好的文章，综合起来看，多看几遍：
binder系列文章开篇,内有一个提纲，从系统启动到服务注册，从c++到java。
http://gityuan.com/2015/10/31/binder-prepare/

侯亮系列，binder开篇，binder以及servicemanager
https://my.oschina.net/youranhongcha/blog/149575

renyugang,这篇文章介绍了另外两篇，比较有深度的两篇。
https://blog.csdn.net/singwhatiwanna/article/details/19756201

1. http://www.cnblogs.com/innost/archive/2011/01/09/1931456.html(邓平凡?，阿拉神农)

2. http://blog.csdn.net/universus/article/details/6211589

其余的比较初级
http://liuwangshu.cn/framework/component/1-activity-start-2.html

## Android binder机制
从c++到java
https://blog.csdn.net/luoshengyang/article/details/6618363
System Server进程启动众多服务进程并addservice，这些服务进程会在ServiceManager中进行注册。
许多java层类方法同过jni调用转到c++层对应的类中进行相关操作。
通过ServiceManager的getService我们可以获取服务的binderProxy，然后通过binder驱动和远程的服务进行跨进程通信。
jni是可以实现双向操作的，java访问navtive,navtive访问java。

## binder机制原理
java层
https://blog.csdn.net/luoshengyang/article/details/6642463(内含一个binder学习路线，若干篇老罗出品，依次学习)
c++层若干篇
https://blog.csdn.net/luoshengyang/article/details/6618363

## binder概述


## 系统启动 概述
长摁电源键后，系统启动(这里不涉及上电后引导区加载等过程，直接从系统的第一个进程开始分析)

1. init进程启动，用户空间的第一个进程。具体文件system\core\init\init.c,main函数启动后会解析init.rc脚本文件。
2. 解析init.rc脚本，init_parse_config_file("/init.rc")生成service_list和action_list。
3. 构建action_queue，可以理解为init进程要执行的一些动作。
4. 逐步执行各种行为，可以理解为开启各种进程。(首先是core类型服务，然后启动main类型服务)
5. 无限循环，等待其他进程发来的信号，响应的执行行为。
6. 服务对应的进程挂掉后会给init进程发送信号，init进程的回调行为是尝试restart服务。

4中涉及的core服务代表举例:console(/system/bin/sh), servicemanager(/system/bin/servicemanager) ;
4中涉及的main服务代表举例:zygote(/system/bin/app_process),media(/system/bin/mediaserver),netd(/system/bin/netd);

7. ServiceManager服务进程启动，启动后最终进入loop状态(binder_loop),涉及内核交互,可以理解为从任务队列里取任务并执行，没有任务会进入阻塞状态等待唤醒。

8. Zygote进程启动，这是Android应用层进程的母体，其中会调用start_system_server开启SystemServer进程，而后进程进入loop状态，通过socket监听信号，执行动作。
(zygote进程创建时会初始化binder相关结构，开启binder监听线程等工作)

9. SystemServer进程启动，会加载各种服务，比如AMS实例化,并将AMS注册到SM中，开启AM线程，systemUI线程(有待考究?有一个system.ui的服务是一个service进程)等，启动webview进程，最后开会开启桌面launcher。

10. 开启桌面会启动新的Activity,首先会创建对应的进程，启动ActivityThread，然后scheduleLaunchActivity开启Activity。
(开启新进程时通过socket和Zygote通信然后创建的新进程,新进程加载的ActivityThread的main方法，启动Activity是利用跨进程binder通信来实现的，进程内部调用scheduleLaunchActivity使用handler发消息最终handleLaunchActivity方法被调用，Activity的attach方法触发，最终onCreate被调用)


## 系统启动 init过程分析
Android系统底层基于Linux Kernel，内核启动过程会建立用户空间的第一个进程即init进程，pid为1。文件位置 /system/core/init/Init.cpp，main方法开启。
1.一些初始化设置(如文件属性、内核log、属性服务，初始化epoll功能、初始化子进程退出处理函数、启动属性服务器);
2.解析"init.rc"文件,init_parse_config_file("/init.rc");
3.构建action_queue，可以理解为init进程要执行的一些动作。这里利用action_for_each_trigger、queue_builtin_action多次处理，生成合适的执行顺序。
4.while循环，执行队列中的指令来开启服务、检测是否需要重启某些服务、监听子进程的退出信号等。
    4.1 execute_one_command 取指令执行服务对应的方法
    4.2 service_start 遍历2中得到的服务列表，根据需要进行重启服务

其中4中会按照一定顺序执行开启各种服务进程，有core类型、main类型，比如servicemanager属于前者，zygote属于后者。

ps查看进程系统情况如下：

```
shell@CB03:/ $ ps | grep init
root      1     0     1000   640   ffffffff 00000000 S /init

shell@CB03:/ $ ps | grep zygote
root      300   1     938404 11380 ffffffff 00000000 S zygote
shell@CB03:/ $ ps | grep servicemanager
system    261   1     1596   384   ffffffff 00000000 S /system/bin/servicemanager

1|shell@CB03:/ $ ps | grep system_server
system    881   300   1140772 66924 ffffffff 00000000 S system_server
u0_a99    19200 300   1133264 108280 ffffffff 00000000 S com.aisino.xfb.pay
```
分析结论：
init为pid为1 ppid为0，servicemanager父进程为init(ppid=1),zygote父进程为init(ppid=1);
servicemanager进程先于zygote进程创建(pid更小)

系统进程system_server由zygote孵化
普通应用进程com.aisino.xfb.pay由zygote孵化(是通过socket给zygote发送信号创建的进程)

## 系统启动 Zygote进程分析(trace dump监测应用变化?)
init.rc脚本文件中描述的服务之一，init进程通过service_start启动的服务，顾名思义，zygote主要功能就是孵化新的进程。
zygote工作流程(frameworks\base\cmds\app_process\App_main.cpp)：
1 构造appruntime,设置进程名字zygote.
2 runtime.start("com.android.internal.os.ZygoteInit",
                startSystemServer ? "start-system-server" : "");
对2的过程描述：
1 初始化jni环境(其中dlopen会加载libdvm.so或libart.so)
2 启动虚拟机startVm
3 注册JNI函数startReg
4 转移到java层ZygoteInit的main方法 

对4的描述：
1 如果传参有"start-system-server"、判定startSystemServer=true
2 registerZygoteSocket(socketName); socketName为"zygote",对应会去从环境里解析出"ANDROID_SOCKET_zygote"文件描述符，生成LocalServerSocket对象sServerSocket，用于后续接收指令。
3 preload,预加载类、资源等等
```java
        preloadClasses();
        preloadResources();
        preloadOpenGL();
        preloadSharedLibraries();
        preloadTextResources();
```
4 如果startSystemServer为true则调用startSystemServer(abiList, socketName);
5 runSelectLoop开启循环
    5.1 针对指令最终会调用peers.get(i).runOnce()->Zygote.forkAndSpecialize->nativeForkAndSpecialize->底层fork
    5.2 pid=0表示子进程，则执行handleSystemServerProcess->RuntimeInit.zygoteInit执行各种初始化最后抛出异常(MethodAndArgsCaller类型)
6 捕获异常执行caller.run()

注意：其中4为true，则开启system_server,父进程也就是zygote进入循环，子进程system_server进程执行中实际会抛出异常，到6，然后caller.run开启了systemserver的main方法。
同样地，循环监听过程收到信号后，fork的新进程也是这个流程，caller.run调用main方法。

其中5.2环节中RuntimeInit.zygoteInit ->nativeZygoteInit 通过本地方法最终执行c++层 gCurRuntime->onZygoteInit()，这里有binder线程池开启过程，所有zygote产生的新进程都会在这个环节初始化binder线程池。

其中5，ams开启线程会使用Process.start的方法，实际就是通过套接字向zygote发送开启进程的信号，一般传过来的是"android.app.ActivityThread"这个进程描述。

```c++
virtual void onZygoteInit()
    {
        // Re-enable tracing now that we're no longer in Zygote.
        atrace_set_tracing_enabled(true);


        sp<ProcessState> proc = ProcessState::self();
        ALOGV("App process: starting thread pool.\n");
        proc->startThreadPool();
    }
```

## 系统启动 ServiceManager进程分析

1. init进程开启的诸多子进程之一
2. 顾名思义，提供服务管理功能，类似一个服务注册表
3. 本身也是一个服务，对应一个固定地址的binder。

源码：frameworks\base\cmds\servicemanager\Service_manager.c

```c

int main(int argc, char **argv)
{
    struct binder_state *bs;
    void *svcmgr = BINDER_SERVICE_MANAGER;

    bs = binder_open(128*1024);

    if (binder_become_context_manager(bs)) 
    {
        ALOGE("cannot become context manager (%s)\n", strerror(errno));
        return -1;
    }

    svcmgr_handle = svcmgr;
    binder_loop(bs, svcmgr_handler);
    return 0;
}
```

其中BINDER_SERVICE_MANAGER固定0
binderloop开启循环
binderParse进行命令解析
svcmgr_handler执行对应动作(包括服务的注册和检索)

## 系统启动 SystemServer进程分析
1. zygote启动后首先孵化出SystemServer进程
2. 新进程中最终执行SystemServer的入口函数main
下面分析systemserver.main关键流程:
main函数中直接执行new SystemServer().run()具体我们看run的主要流程：

```java
System.loadLibrary("android_servers");// Initialize native services.
createSystemContext();// Initialize the system context.
startBootstrapServices();//各种相关服务    ams的setSystemProcess会被调用，向sm注册各种binder服务
startCoreServices();//各种相关服务
startOtherServices();//各种相关服务 ims wms dms会注册 ams的systemReady中会启动桌面应用
Looper.loop();//消息循环
```

createSystemContext();
    [
        利用ActivityThread.systemMain()生成AT对象
        进一步对mSystemContext以及mSystemUIContext设置主题相关
    ]
...
startBootstrapServices();
    [
        //注意下述startservice都是SystemServiceManager的对象的方法，不同于ServiceManger(我们使用ServiceManger的addservice和findservice来进行aidl相关的服务注册和查询)
        Installer installer = mSystemServiceManager.startService(Installer.class);//开启installer服务，这个是SystemService子类，非AIDL调用的那种服务，比如SMS、AMS、PMS等。
        ...
        mActivityManagerService = mSystemServiceManager.startService(
                ActivityManagerService.Lifecycle.class).getService(); //开启ActivityManagerService.Lifecycle服务，也是SystemService子类
        ...
        PackageManagerService启动//aidl服务
        mActivityManagerService.setSystemProcess(); //设置ams，其中会调用ServiceManager进行注册各种binder,包括AMS自身。
    ]
startCoreServices();
    [

    ]
startOtherServices();
    [
        各种mSystemServiceManager.startService(xxxx);//SystemService子类
        各种ActivityManager.addService(xxxx);//binder服务
        此环节这里会有IMS、WMS、DisplayManagerService、NetworkManagerService等的相关启动和设置
        mActivityManagerService.systemReady(Runnable,TimingsTraceLog)
            [
                执行runnable()
                    [
                        ...
                        startSystemUi(context, windowManagerF);//开启com.android.systemui.SystemUIService服务 这个继承自Service组件
                            [
                                SystemUIService.onCreate
                                    [
                                        ((SystemUIApplication) getApplication()).startServicesIfNeeded()
                                        [
                                            `资源文件如何搜寻，比如config_systemUIServiceComponents具体在哪里?`
                                            获取names数组(R.array.config_systemUIServiceComponents);(比如com.android.systemui.SystemBars、Recents、PowerUI等，都是SystemUI的子类)
                                            startServicesIfNeeded(names);
                                            [
                                                循环实例化service，这里的service不同于Service组件，只是SystemUI的子类
                                                mServices[i].start(); //开启各个SystemUI组件服务
                                                `具体选择一两个service看下执行过程、如何关联到界面UI绘制?`
                                                如果mBootCompleted则mServices[i].onBootCompleted调用;
                                            ]
                                        ]
                                    ]

                            ]
                        ...
                    ]
                startHomeActivityLocked(currentUserId, "systemReady");//开启桌面
            ]
    ]

辅助*通过ps -T pid可以查看进程下的线程：
system_server进程下有很多线程：
system_server
android.bg
android.ui
android.io
android.display
CpuTracker
ActivityManager(若干个)
binder_xxx(若干个)
InputDispatcher
InputReader
AccountManagerService

## 系统启动 AMS服务开启

ams的初始化以及注册调用流程：

SystemServer.main()->
    run()->
        startBootstrapServices->
            mSystemServiceManager.
            startService(ActivityManagerService.Lifecycle.class).getService();//获取AMS对象，实例化过程也会开启ActivityManagerService线程，一个ServiceThread.
            mActivityManagerService.setSystemProcess->ServiceManager.add(name, this);//向SM注册AMS

AMS初始化关键代码
```java 
mActivityManagerService = mSystemServiceManager.startService(
                ActivityManagerService.Lifecycle.class).getService();
```

ams实例化细节：

mSystemServiceManager.startService((ActivityManagerService.Lifecycle.class))->
    创建ActivityManagerService.Lifecycle->
        LifeCycle实例化过程会生成ams

    liftcycle调用onstart->
        最终调用ams.start->
            mProcessCpuThread.start();//启动cpuTracker线程
            mBatteryStatsService.publish(mContext);//注册atteryStatsService
                ->ServiceManager.addService(BatteryStats.SERVICE_NAME, asBinder());
            mAppOpsService.publish(mContext);//注册AppOpsService服务
                ->ServiceManager.addService(Context.APP_OPS_SERVICE, asBinder());

ams内部类lifecycle
```java

    public static final class Lifecycle extends SystemService {
        private final ActivityManagerService mService;

        public Lifecycle(Context context) {
            super(context);
            mService = new ActivityManagerService(context);//实例化ams
        }

        @Override
        public void onStart() {
            mService.start(); //调用ams的start
        }

        public ActivityManagerService getService() {
            return mService;
        }
    }
```

ams构造函数
```java
public ActivityManagerService(Context systemContext) {
    ....
    mSystemThread = ActivityThread.currentActivityThread();
    mHandlerThread = new ServiceThread(TAG,
            android.os.Process.THREAD_PRIORITY_FOREGROUND, false /*allowIo*/); //新建ActivityManagerService线程
    mHandlerThread.start(); //开启ActivityManagerService线程
    mHandler = new MainHandler(mHandlerThread.getLooper());//ams线程handler
    mUiHandler = new UiHandler();//UiHandler为内部类，会开启"android.ui"的UIThread,并获取handler
    ....
    mProcessCpuThread = new Thread("CpuTracker") {....};//线程构造，具体start是在 LifeCycle.onStart->ams.start时开启
}
```

ams的start方法
```java
private void start() {
    Process.removeAllProcessGroups();
    mProcessCpuThread.start();

    mBatteryStatsService.publish(mContext);
    mAppOpsService.publish(mContext);
    Slog.d("AppOps", "AppOpsService published");
    LocalServices.addService(ActivityManagerInternal.class, new LocalService());
}
```

ams注册调用链：
startBootstrapServices方法最后会对生成的ams进行注册操作，注册到sm。

    mActivityManagerService.setSystemProcess()->
        ServiceManager.addService(Context.ACTIVITY_SERVICE, this, true);

```java
    public void setSystemProcess() {
        try {
            ServiceManager.addService(Context.ACTIVITY_SERVICE, this, true);
            ServiceManager.addService(ProcessStats.SERVICE_NAME, mProcessStats);
            ServiceManager.addService("meminfo", new MemBinder(this));
            ServiceManager.addService("gfxinfo", new GraphicsBinder(this));
            ServiceManager.addService("dbinfo", new DbBinder(this));
            ....
        }
        .....
    }

```

`表格取自gityuan`

服务名	|类名	|功能
-|-|-
activity	|ActivityManagerService	|AMS
procstats	|ProcessStatsService	|进程统计
meminfo	|MemBinder	|内存
gfxinfo	|GraphicsBinder	|图像信息
dbinfo	|DbBinder	|数据库
cpuinfo	|CpuBinder	|CPU
permission	|PermissionController	|权限
processinfo	|ProcessInfoService	|进程服务
usagestats	|UsageStatsService	|应用的使用情况    

通过dumpsys 服务名 查看运行详情：
dumpsys activity
dumpsys cpuinfo
dumpsys window

ams.systemReady方法：

    传入一个runnable动作，根据条件执行runnable行为；
        其中runnable会调用startSystemUi开启系统UI服务；
    开启桌面： startHomeActivityLocked(currentUserId, "systemReady");

systemserver中开启系统UI:

```java
    static final void startSystemUi(Context context) {
        Intent intent = new Intent();
        intent.setComponent(new ComponentName("com.android.systemui",
                    "com.android.systemui.SystemUIService"));
        intent.addFlags(Intent.FLAG_DEBUG_TRIAGED_MISSING);
        //Slog.d(TAG, "Starting service: " + intent);
        context.startServiceAsUser(intent, UserHandle.SYSTEM);
    }    
```

桌面开启的调用链:
    ams.systemReady->ams.startHomeActivityLocked->ActivityStarter.startHomeActivityLocked->as.startActivityLocked 
    `具体如何开启launcher的启动activity?`
    intent的显示调用和隐式调用，显示可以指定对应activity，隐式需要解析出对应的activity列表。

ams中的handler：
AMS.mUiHandler - UiHandler - android.ui线程
AMS.mBgHandler - Handler - android.bg
AMS.mHandler - MainHandler - 'ActivityManager'

`所有跟超时相关的工作都运行在ActivityManager线程，唯独input的超时处理过程并非发生在ActivityManager线程，而是inputDispatcher线程发生的。`

`对于ANR/Crash/Error等几乎所有错误、警告相关的对话框都运行在android.ui线程。`

ams和wms的涉及的一些类:

ams:ActivityRecord;TaskRecord;ActivityStack;
wms:AppWindowToken;Task;TaskStack;


## Android中进程的创建

新进程产生的场景：
1. 系统启动，init进程开启后，根据init.rc文件启动一系列子进程。
2. zygote进程启动后，开启system_server进程，并执行runSelectLoop进入socket监听状态。
3. startActivity或者startService等开启组件，如果需要开启新进程，会通过ActivityThread和SystemServer交互，再有SystemServer向Zygote发起请求进行进程开辟。

最终都是c++层fork方法，对场景3进行java层流程梳理：
- App进程执行startActivity或者startService
- aidl通信、SystemServer进程中AMS执行对应的startActivity或者startService,通过A

[AMS]   startActivity->
    [AMS]   startActivityAsUser->
        [ActivityStarter]   mActivityStarter.startActivityMayWait->
            [ActivityStarter]   mActivityStarter.startActivityLocked->
                [ActivityStarter]   mActivityStarter.startActivityUnchecked->
                     [ActivityStack]    mTargetStack.startActivityLocked(mStartActivity, newTask, mKeepCurTransition, mOptions)->
                        [ActivityStack]    ensureActivitiesVisibleLocked->
                            [ActivityStack]     makeVisibleAndRestartIfNeeded->
                                [ActivityStackSupervisor]   mStackSupervisor.resumeTopActivitiesLocked->
                                    [ActivityStackSupervisor]   mStackSupervisor.startSpecificActivityLocked 进程创建/ realStartActivityLocked 已有进程直接启动activity
如果目标进程已经存在，直接进行activity启动；否则创建进程，进程创建完毕后会继续执行没有启动的这个activity。

- 上述realStartActivityLocked后续步骤：
system_server进程：
[ActivityStackSupervisor] realStartActivityLocked->
    [ActivityStackSupervisor]   app.thread.scheduleLaunchActivity->`aidl调用进入到ActivityThread的scheduleLaunchActivity`
    旧版本有如下aidl调用链：
        [ ApplicationThreadProxy]   ApplicationThreadProxy.scheduleLaunchActivity->transact->ApplicationThreadNative.onTransact->ActivityThread.scheduleLaunchActivity

目前app进程：
[ActivityThread] scheduleLaunchActivity->
            [ActivityThread]  sendMessage(H.LAUNCH_ACTIVITY, r)-> 发送消息给app主线程

[ActivityThread] handler处理消息最终handleLaunchActivity

- 上述startSpecificActivityLocked 进程创建后续步骤:
system_server进程：
[ActivityStackSupervisor]   mStackSupervisor.startSpecificActivityLocked
    [AMS]   startProcessLocked->
            [AMS]  Process.start->
                [Process]   startViaZygote->
                    [Process]   zygoteSendArgsAndGetResult->
                        通过zygoteState.writer写数据，和zygote进程进行socket通信

zygote进程:
[ZygoteInit] runSelectLoop循环等待消息
    [ZygoteInit] peers.get(i).runOnce()
        [ZygoteConnection] runonce
            [ZygoteConnection]  pid = Zygote.forkAndSpecialize
                         [Zygote]   VM_HOOKS.preFork()
                         [Zygote]   pid = nativeForkAndSpecialize
                         [Zygote]   VM_HOOKS.postForkCommon()

接上文子进程(pid>0)执行handleChildProc
[ZygoteConnection] RuntimeInit.handleChildProc
     [ZygoteConnection] RuntimeInit.zygoteInit
        [RuntimeInit]   commonInit
        [RuntimeInit]   nativeZygoteInit
             [AndroidRuntime.cpp]gCurRuntime->onZygoteInit() //这里会开启binder线程
        [RuntimeInit]   applicationInit
            [RuntimeInit]   invokeStaticMain //抛出异常，指定方法名称main,异常捕获的时候会执行对应类的main方法
[ZygoteInit] MethodAndArgsCaller.run
    [ZygoteInit] mMethod.invoke(null, new Object[] { mArgs });//执行ActivityThread main方法
    
继续子进程，即目标app进程：
[APP2 ActivityThread]
   [ActivityThread] main->
     [ActivityThread]  attach(false)->
                    [AMS]   ams.attachApplication->......
                    .....
                        [ActivityStackSupervisor] realStartActivityLocked->后续同前文`realStartActivityLocked后续步骤`

## 系统启动过程中binder知识点

IPC的一些方式：共享内存、管道、socket
binder用于进程间通信，设计思想，客户端binder client,内存映射到内核区域，服务端binder server，和内核进行共享内存，因此双方同步只需要进行一次拷贝操作。
用户空间首个进程init进程启动时会进行binder设备初始化操作
特殊的binder、固定0号binder为ServiceManager，SM进程启动时初始化。
其他的binder都会通过sm.addService进行服务注册，通过sm.getService进行服务检索。
0号binder是固定的，因此可以直接找到。其他的通过名字来进行检索。
一个进程中可以有多个binder服务。
app进程都是由zygote分裂而来，进程初始化后通过onZygoteInit会开启binder线程池，用于binder通信。
可以理解为不同的进程中都有很多binder线程(binder线程池)通过和底层通信来监听binder相关的信号，以进行binder通信。

一个简化的模型来进行理解：
client想要执行server A操作，于是通过server的binderproxy写入操作名、参数等，并根据需要接收服务端返回的数据；
server的binder线程根据获取的操作、参数进行对应的行为，根据需要返回数据等；

android系统中AMS、WMS、PMS的操作：
首先通过获取SM的binder proxy，然后通过aidl通信获取AMS的proxy,然后通过aidl通信进行AMS相关操作。

## aidl binder相关 源码版本演进
几个关键类
ApplicationThreadProxy(ATN的内部类,位置ApplicationThreadNative.java)
ActivityManagerProxy(AMN的内部类，位置ActivityManagerNative.java)
IApplicationThread接口(IApplicationThread.java)
IActivityManager接口(IActivityManager.java)

7.1源码framework目录还有代理类实现。
8.2源码中ATN和AMN被标记为过时的，并且移除了ATP和AMP；同时移除了IApplicationThread.java和IActivityManager.java两个定义接口的文件，但是添加了IActivityMananger.aidl和IApplicationThread.aidl两个文件用于辅助生成aidl相关类。
binder的调用本质没有改变，之前是手动写java层native和proxy代理类，后来使用aidl文件来统一生成相关的中间类。
`生成的类在哪里可以找到？IActivityMananger 以及 IActivityManager.stub的class在哪里，源码里只有对应IActivityMananger.aidl文件`

## APP启动

场景1：
Laucher启动

场景2：普通应用启动
launcher -> app
过程类似`Android中进程的创建`章节中先启动新进程再开启Activity的情况。

1.点击桌面app
2.launcher 进行本地startActivity操作，通过binder调用向system_server发起远程调用startActivity的请求，具体是ams来实现这个过程
3.system_server通过socket向Zygote进程发送请求，要求创建新进程
4.zygote进行分裂，产生新APP进程，执行ActivityThread的main方法
5.新APP进程启动后要求attachApplication，也是binder调用转到system_server进程
6.system_server进程最终会通过binder调用要求新APP进程执行scheduleLaunchActivity操作
7.新App进程执行scheduleLaunchActivity，通过向主线程handler发送消息触发相关操作
8.新APP进程主线程执行handleLaunchActivity
9.目标Activity会被创建，最终onCreate被回调

紧接着思考：
    window的建立
    ui的绘制

## Window建立

1. 窗口的建立
2. view的绘制

WMS负责处理窗口相关，wms通过向android.display线程的handler发送不同消息，display线程进行对应的处理。
下面列举几个:

DO_TRAVERSAL
ADD_STARTING //添加启动窗口
REMOVE_STARTING //移除启动窗口
FINISHED_STARTING //完成启动

处理行为见WMS.H的handleMessage方法


startingwindow的创建过程：

背景:
源进程通过startActivity开启新应用，首先通过binder调用system_server中ams的startactivity,
ams通过as、ass等执行到ActivityStack.startActivityLocked方法:

流程链:
[ActivityStack.startActivityLocked]
ActivityStack.startActivityLocked->
    r.showStartingWindow(prev, showStartingIcon)->

[ActivityRecord.showStartingWindow]
    shown = service.mWindowManager.setAppStartingWindow->

[WMS.setAppStartingWindow]
    Message m = mH.obtainMessage(H.ADD_STARTING, wtoken);//ADD_STARTING消息
    mH.sendMessageAtFrontOfQueue(m);//向display线程发送消息 
    (由于WMS实例化是在display线程中，这里的mH初始化也是display线程中,无参构造函数，使用的是当前线程也就是display线程的looper。)

[WMS.H]
    handleMessage
        mPolicy.addStartingWindow
    
[PhoneWindowManager.addStartingWindow]
    addStartingWindow->
        final PhoneWindow win = new PhoneWindow(context);
        wm = (WindowManager)context.getSystemService(Context.WINDOW_SERVICE); //wm为WindowManagerImpl
        view = win.getDecorView();
        wm.addView(view, params);

[WindowManagerImpl.addview]
        mGlobal.addView(view, params, mContext.getDisplay(), mParentWindow);

[WindowManagerGlobal.addview]
        root = new ViewRootImpl(view.getContext(), display);
        root.setView(view, wparams, panelParentView);
[ViewRootImpl.setView]
         requestLayout()->
            scheduleTraversals()->
         mWindowSession.addToDisplay
[Session.addToDisplay]
         wms.addWindow
[WMS.addWindow]
    updateFocusedWindowLocked->
        mWindowPlacerLocked.performLayoutLockedInner(displayContent, true /*initial*/,
                            updateInputWindows);
[WindowSurfacePlacer.performLayoutLockedInner]
    mService.mPolicy.beginLayoutLw
    mService.mPolicy.finishLayoutLw();
    mService.mH.sendEmptyMessage(UPDATE_DOCKED_STACK_DIVIDER);

[PhoneWindowManager.beginLayoutLw]
    updateSystemUiVisibilityLw

Session中有SurfaceSession对象用于和surfaceflinger进程通信

总结:
Activity启动的时候，组件相关AMS处理,窗口相关WMS处理。
需要展示starting窗口的话wms、PhoneWindowManager、ViewRootImpl、Session等共同参与、启动窗口。

Activity在ActivityThread中创建之后：
handleLaunchActivity->
    performLaunchActivity->
        Activity.attach-> //设置activity和window
    handleResumeActivity->
        performResumeActivity->
            r.activity.makeVisible() //真正界面展示


makevisible方法，如果没有添加window则通过binder调用添加window，最后设置view可见

```java
void makeVisible() {
    if (!mWindowAdded) {
        ViewManager wm = getWindowManager();
        wm.addView(mDecor, getWindow().getAttributes());
        mWindowAdded = true;
    }
    mDecor.setVisibility(View.VISIBLE);
}
```

一些类:
View 
ViewGroup 
ViewRootImpl 
ViewManager
DecorView
PhoneWindow
PhoneWindowManager
WindowManagerGlobal
WindowManagerImpl

WMS中对窗口类型的定义，数值越大，显示的时候越靠前

窗口的主序
TYPE_UNIVERSE_BACKGROUND	11000	
TYPE_WALLPAPER	21000
TYPE_PHONE	31000	
TYPE_SEARCH_BAR	41000
TYPE_RECENTS_OVERLAY	51000	
TYPE_SYSTEM_DIALOG	51000
TYPE_TOAST	61000	
TYPE_PRIORITY_PHONE	71000
TYPE_DREAM	81000	
TYPE_SYSTEM_ALERT	91000
TYPE_INPUT_METHOD	101000	
TYPE_INPUT_METHOD_DIALOG	111000
TYPE_KEYGUARD	121000	
TYPE_KEYGUARD_DIALOG	131000
TYPE_STATUS_BAR_SUB_PANEL	141000	
应用窗口与未知类型的窗口	21000


子窗口类型	子序
TYPE_APPLICATION_PANEL	1
TYPE_APPLICATION_ATTACHED_DIALOG	1
TYPE_APPLICATION_MEDIA	-2
TYPE_APPLICATION_MEDIA_OVERLAY	-1
TYPE_APPLICATION_SUB_PANEL	2

## 系统UI服务

ams.systemReady->
    runnable执行->
        startSystemUi->
            context.startServiceAsUser(intent, UserHandle.SYSTEM);
            ("com.android.systemui.SystemUIService"))

经历service启动后进入onCreate方法：

SystemUIService.onCreate
    [
        ((SystemUIApplication) getApplication()).startServicesIfNeeded()
        [
            `资源文件如何搜寻，比如config_systemUIServiceComponents具体在哪里?`
            获取names数组(R.array.config_systemUIServiceComponents);(比如com.android.systemui.SystemBars、Recents、PowerUI等，都是SystemUI的子类)
            startServicesIfNeeded(names);
            [
                循环实例化service，这里的service不同于Service组件，只是SystemUI的子类
                mServices[i].start(); //开启各个SystemUI组件服务
                `具体选择一两个service看下执行过程、如何关联到界面UI绘制?`
                如果mBootCompleted则mServices[i].onBootCompleted调用;
            ]
        ]
    ]

## launcher启动

桌面开启的调用链:
    ams.systemReady->ams.startHomeActivityLocked->ActivityStarter.startHomeActivityLocked->as.startActivityLocked `具体如何开启launcher的启动activity?`

`boot信号获取在哪里?源码分析`

## IMS(InputManagerService)

1. 启动IMS:

```
SystemServer -> 
    startOtherServices -> 
        inputManager = new InputManagerService(context)
        ServiceManager.addService(Context.INPUT_SERVICE, inputManager);
        inputManager.start();
```

2. InputManagerService实例化过程:
    设置InputManagerService的handler关联"android.display"线程(DisplayThread);
    创建InputDispatcher和InputReader本地对象。

3. IMS.start过程：
    通过native调用开启两个线程，InputReader和InputDispatcher。(java层使用android.display线程处理消息?)

4. Input事件流程：
`这里是从屏幕触摸到ViewRootImp处理，最终如何从viewrootImp到Activity的呀？`
`https://blog.csdn.net/singwhatiwanna/article/details/50775201 这个是解答，其中Thread.dumpStack是一个很好的分析工具`

```
    屏幕触摸-硬件驱动-信号和事件
    InputReader不断获取由驱动产生的按键事件，传给InputDispatcher。`input_event(设备节点) -> RawEvent(EventHub)->inputReader加工一些列低级事件转化为android输入事件`
    InputDispatcher线程从队列取事件，并进行事件传递，派发到合适的窗口。这里会从SystemServer和目标进程进行跨进程通信，InputDispatcher通过socket和远程进程通信(异步非阻塞)。
        -publishKeyEvent->
            -mChannel->sendMessage(&msg);
        收到消息返回调用receiveFinishedSignal进行处理

    目标APP的UI主线程(android.ui线程)looper循环，处理派发到窗口的事件,处理完毕后socket通信发送信号给InputDispatcher。
        -nativePollOnce
            -Looper::pollInner
                -InputConsumer.consume
                    -....
                        -viewrootImp.deliverInputEvent
                        -finishInputEvent
                            -sendFinishedSignal
                                -mChannel->sendMessage(&msg);
```
进一步viewrootImp.deliverInputEvent的处理

-viewrootImp.deliverInputEvent
    -InputStage.deliver
        -InputStage.onDeliverToNext
            -InputStage.deliver
                -ViewPostImeInputStage.onProcess
                    -ViewPostImeInputStage.processPointerEvent
                        -ViewPostImeInputStage.processPointerEvent
                            -view.dispatchPointerEvent
                                -decorview.dispatchTouchEvent
                                    -wc.dispatchTouchEvent(即windowcallback的dispatchtouchevent方法,是activity attach方法的时候对mWindow进行设置的)
                                        -Activity.dispatchTouchEvent(后续正常分发)
                                            -PhoneWindow.superDispatchTouchEvent
                                                -PhoneWindow$DecorView.superDispatchTouchEvent
                                                    -ViewGroup.dispatchTouchEvent(ViewGroup和view按键事件传递)


5. system_server中的ims相关核心线程

shell@CB03:/ $ ps | grep system_server
system    881   300   1167360 76556 ffffffff 00000000 S system_server

shell@CB03:/ $ ps -t 881 | grep input
system    2557  881   1167360 76556 ffffffff 00000000 S InputDispatcher
system    2558  881   1167360 76556 ffffffff 00000000 S InputReader


6. android 系统输入事件流程总结(邓平凡)

Android输入系统的工作原理概括来说，就是监控/dev/input/下的所有设备节点，当某个节点有数据可读时，将数据读出并进行一系列的翻译加工，然后在所有的窗口中寻找合适的事件接收者，并派发给它。

内核将原始事件写入到设备节点中，InputReader不断地通过EventHub将原始事件取出来并翻译加工成Android输入事件，然后交给InputDispatcher。InputDispatcher根据WMS提供的窗口信息将事件交给合适的窗口。窗口的ViewRootImpl对象再沿着控件树将事件派发给感兴趣的控件。控件对其收到的事件作出响应，更新自己的画面、执行特定的动作。所有这些参与者以IMS为核心，构建了Android庞大而复杂的输入体系。

当两个线程启动后，InputReader在其线程循环中不断地从EventHub中抽取原始输入事件，进行加工处理后将加工所得的事件放入InputDispatcher的派发发队列中。InputDispatcher则在其线程循环中将派发队列中的事件取出，查找合适的窗口，将事件写入到窗口的事件接收管道中。窗口事件接收线程的Looper从管道中将事件取出，交由事件处理函数进行事件响应。整个过程共有三个线程首尾相接，像三台水泵似的一层层地将事件交付给事件处理函数

`ims启动前,wms已经启动,这样wms才能接收inputdispatcher派发来的事件并寻找合适的处理对象`

其中用到了Inotify和Epool,线程相关知识 邓系列 https://blog.csdn.net/Innost/article/details/90633199


7. 查看输入设备以及输入事件

adb shell getevent -t

设备列表
add device 1: /dev/input/event4
  name:     "msm8909-skue-snd-card Headset Jack"
add device 2: /dev/input/event3
  name:     "msm8909-skue-snd-card Button Jack"
add device 3: /dev/input/event1
  name:     "qpnp_pon"
add device 4: /dev/input/event0
  name:     "goodix-ts"
could not get driver version for /dev/input/mice, Not a typewriter
add device 5: /dev/input/event2
  name:     "gpio-keys"

基本事件(下面是手头机器电源键按下抬起的记录)

[ 3385509.651590] /dev/input/event1: 0001 0074 00000001
[ 3385509.651590] /dev/input/event1: 0000 0000 00000000
[ 3385509.861141] /dev/input/event1: 0001 0074 00000000
[ 3385509.861141] /dev/input/event1: 0000 0000 00000000

两种方式模拟按键
1. 模拟指定输入设备的指定基本事件 
adb shell sendevent /dev/input/event1 xx xx xx

2. 更为高层的按键事件 
adb shell input keyevent xx(xx为按键码)
adb shell input tap 50 250(点击) 
adb shell input swipe 50 250 250 250 500(滑动) 

## WMS(WindowManagerService)

1. 启动WMS

```
SystemServer->
    startOtherServices->
        wm = WindowManagerService.main(context, inputManager,
                    mFactoryTestMode != FactoryTest.FACTORY_TEST_LOW_LEVEL,
                    !mFirstBoot, mOnlyCore);
        ServiceManager.addService(Context.WINDOW_SERVICE, wm);
        wm.displayReady();
        wm.systemReady();
```

2. WMS实例化(main方法)

```java
    public static WindowManagerService main(final Context context,
            final InputManagerService im,
            final boolean haveInputMethods, final boolean showBootMsgs,
            final boolean onlyCore) {
        final WindowManagerService[] holder = new WindowManagerService[1];
        DisplayThread.getHandler().runWithScissors(new Runnable() {
            @Override
            public void run() {
                holder[0] = new WindowManagerService(context, im,
                        haveInputMethods, showBootMsgs, onlyCore);
            }
        }, 0);
        return holder[0];
    }
```

 利用DisplayThread.getHandler().runWithScissors来调用wms初始化，这里注意两点：初始化过程是在display线程执行，当前线程会等待执行结束(同步操作，runWithScissors特性)，再进行返回。
 `runWithScissors实现，源码学习一下`
 
 WMS实例化：其中调用initPolicy方法，该方法会利用runWithScissors在UIThread中执行mPolicy.init的过程,UiThread.getHandler().runWithScissors,阻塞操作,等待执行后返回本线程再继续走.

 3. 涉及线程
 system_server主线程, “android.display”线程, “android.ui”线程
 `这里的UI线程和app进程中的UI线程?UIThread的单例模式?跨进程?android.ui何时启动的`

 4. 调用流图

 SystemServer->
 WMS.main->
        new WMS[android.display]->
        initPolicy[android.display]->
            mPolicy(PhoneWindowManager对象).init[android.ui]->
            <-
        <-
WMS.displayReady
WMS.systemReady

5. wms中涉及的一些线程
SystemServer主线程、display线程、android.ui线程。
DisplayThread extends ServiceThread(extends HandlerThread) : "android.display"
UIThread extends ServiceThread(extends HandlerThread) : "android.ui"
HandlerThread设计的目的:getLooper方法会阻塞，等到线程开启循环loop后才会返回，避免线程不同步，消息循环还没来得及开启时获取的looper为空。



## ams wms system_server一些知识点
AMS负责四大组件以及进程管理,各种record和stack。
WMS负责窗口管理，会跟APP、SurfaceFlinger交互，实现UI呈现。
SF负责UI绘制,SF在一个单独的进程，也是init开启的子进程，AMS和WMS都在system_server进程中，对应若干个工作线程。

Activity的一些成员变量:
mWindow - PhoneWindow
mWindowManager - WindowManagerImpl
mDecor - View  onResume之后展示的视图

ViewRootImpl:
    mWindowSession - IWindowSession (进程对应的wms中session的代理对象，和wms中Session通信)
    mWindow - IWindow.Stub (ViewRootImpl.W)

WindowState:
    mSession - Session(binder服务端)
    mClient - IWindow(ViewRootImpl.W的代理对象，对应着ViewRootImpl的W类型对象)
    mSurfaceSession - 和SF通信 //api-28没有找到这个成员

Session:
    mSurfaceSession - 和SF通信

Binder服务端： 
WMS/Session/ActivityRecord.Token(system_server进程)
ViewRootImpl.W(app进程)
ApplicationThread(app进程)
SF(SF进程)

一个activity对应一个窗口，一个窗口对应一个ViewRootImpl对象
一个app进程有唯一的WindowManagerGlobal对象
一个app进程对应相同的session
Activity最终视图是decorview
ViewRootImpl用于DecorView和wms的交互，每次wm.addview会创建一个VRI对象


WindowManager可以通过getSystemService(Context.WINDOW_SERVICE)来获取，实际是一个windowManagerImpl对象,可以认为是WMS的代理，但是方法不会完全对应上，不想我们之前看到的xxxProxy那种形式，client和server很好对应，因为这里Windowmanager继承自ViewManager，vm定义了一些操作，wm实现了这些操作，wm对操作的实现是通过windowManagerImpl、进一步通过WindowManagerGlobal来实现的。
WindowManage的AddView调用链:
 wm.addView
    windowManagerImpl.addView
        windowManagerGlobal.addView
            ViewRootImpl.setView
                mWindowSession.addToDisplay  ----  binder调用到wms的Session服务端  session.addToDisplay -> wms.addWindow
相关类梳理：

ViewManager - WindowManager - WindowManagerImpl - WindowManagerGlobal
ViewManager - ViewGroup

ViewParent - ViewRootImpl - ViewRootImpl.W - IWindowSession.stub(Server端)
ViewParent - ViewGroup
WMS - IWindowManager.Stub(Server端)
Session

IWindowManger.aidl
IWindowSession.aidl

参考 深入理解ViewRootImpl 
https://blog.csdn.net/Innost/article/details/47660471
https://wizardforcel.gitbooks.io/deepin-android-vol3/content/7.html
https://silencedut.github.io/2016/08/10/Android%E8%A7%86%E5%9B%BE%E6%A1%86%E6%9E%B6Activity,Window,View,ViewRootImpl%E7%90%86%E8%A7%A3/

## activity、window、viewrootimpl、windowmanager、windowmanagreImpl、windoWmanagerGlobal

ActivityThread.main
    Looper.loop()

    scheduleLaunchActivity
        ActivityClientRecord r = new ActivityClientRecord();
        sendMessage(H.LAUNCH_ACTIVITY, r);
    handleLaunchActivity
        performLaunchActivity
            mInstrumentation.newActivity
            makeApplication //内部创建application是一个单例模式，多数情况此前已经创建过application了
            createBaseContextForActivity//创建实际的contexImpl
                ContextImpl.createActivityContext
            activity.attach
                attachBaseContext(context)
                mWindow = new PhoneWindow(this, window) //mWindow为Window  PhoneWindow
                mWindow.setWindowManager((WindowManager)context.getSystemService(Context.WINDOW_SERVICE),mToken,xx) //注意SystemServiceRegistry初始化过程
                mWindowManager = mWindow.getWindowManager()

            mInstrumentation.callActivityOnCreate
                activity.performCreate
                    activity.onCreate
            mInstrumentation.callActivityOnRestoreInstanceState
                activity.performRestoreInstanceState
                    onRestoreInstanceState
            performStart
                activity.onStart
        handleResumeActivity
            performResumeActivity
                activity.performResume()
                    activity.onResume()
             r.activity.makeVisible()
                 wm.addView(mDecor, getWindow().getAttributes());// 这里的wm是windowmanager，前文有setWindowManager和getWindowManager
                    windowManagerImpl.addView;
                        windowManagerGlobal.addView;
                             root = new ViewRootImpl(view.getContext(), display)//view就是传入的decorView
                                    mWindowSession = WindowManagerGlobal.getWindowSession(); //
                                         IWindowManager windowManager = getWindowManagerService(); //这里取名windowManager，但实际是一个IWindowManager类型，binder client不同于前文WindowManager的对象
                                            sWindowManagerService = IWindowManager.Stub.asInterface(ServiceManager.getService("window"));
                                            return sWindowManagerService
                                         sWindowSession = windowManager.openSession //binder调用进入到WMS.openSession 返回Session对象 Session extends IWindowSession.Stub
                                         return sWindowSession
                                    mWindow = new W(this); //ViewRootImpl.W对象，可以认为是ViewRootImp的binder代理。                                    
                             root.setView(view)
                                 requestLayout();
                                    checkThread();
                                    scheduleTraversals();//向主线程发消息执行performTraversals
                                 mWindowSession.addToDisplay(mWindow)// binder调用;mWindowSession是WMS中Session的代理，这里mWindow是ViewRootImpl.W对象，可以认为是ViewRootImp的binder代理，作为参数传到WMS中
                                     
                                [binder调用进入Session]
                                    mService.addWindow //mService就是wms
                                        wms.addWindow //addWindow具体过程参考## Window建立中wms.addWindow部分

                 mDecor.setVisibility(View.VISIBLE);

IWindowSession
/frameworks/base/core/java/android/view/
IWindowSession.aidl
final class Session extends IWindowSession.Stub


IWindowManager
/frameworks/base/core/java/android/view/
IWindowManager.aidl

public class WindowManagerService extends IWindowManager.Stub


对比ActivityManagerService

public final class ActivityManagerService extends ActivityManagerNative
public abstract class ActivityManagerNative extends Binder implements IActivityManager
class ActivityManagerProxy implements IActivityManager
public interface IActivityManager extends IInterface

/frameworks/base/core/java/android/app/
IActivityManager.java	

对比发现，这里AMS是手动编写而WMS使用aidl文件生成相关类
但是高版本中AMN被标记为过时的，并且移除AMP；同时移除了IActivityManager.java定义接口的文件，但是添加了IActivityMananger.aidl。


高版本中如下设计：
a：ViewRootImpl-IWindowSession-WMS.Session-WMS.       通过viewRootImpl的setview到wms.addWindow，其中会传入IWindow对象，即ViewRootImpl.W对象，是ViewRootImpl.W服务的客户端
b：WMS-IWindow-ViewRootImpl

```java
    void makeVisible() {
        if (!mWindowAdded) {
            ViewManager wm = getWindowManager();
            wm.addView(mDecor, getWindow().getAttributes());
            mWindowAdded = true;
        }
        mDecor.setVisibility(View.VISIBLE);
    }
```

            
## ViewRootImpl

前面谈到WindowManager实际的操作最终调用到ViewRootImpl的一些方法。
ViewRootImpl的setView分析:
调用图前面分析过，截取部分作为概览如下
root.setView(view)
    requestLayout();// Schedule the first layout -before- adding to the window manager 邓大神说这里会产生巧妙地效果，我是没搞明白为啥这样做
    checkThread();//检测是否为主线程，否则不能进行UI操作
    scheduleTraversals();//向主线程发消息执行performTraversals
    mWindowSession.addToDisplay(mWindow)// wms窗口添加到屏幕

1. 其中requestLayout实际是发消息告诉主线程执行performTraversals方法：
requestLayout
    checkThread() //非UI不能发起，因为这里requestLayout是一个public方法，很多地方都可能发起调用
    mLayoutRequested = true//标志是通过requestLayout发起的请求
    scheduleTraversals()//通过mChoreographer向主线程发送消息，传入了一个mTraversalRunnable对象，最终执行该对象的run方法调用doTraversal()->performTraversals()具体见4

2. 其中checkThread检测线程，非UI线程不可更新：

```java
void checkThread() {
    if (mThread != Thread.currentThread()) {
        throw new CalledFromWrongThreadException(
                "Only the original thread that created a view hierarchy can touch its views.");
    }
}
```

3. 其中addToDisplay通过binder进入到session对应方法:

session.addToDisplay
    mService.addWindow
        ..`如何添加到屏幕可以细细分析` 窗口尺寸问题 图形渲染进程交互等 `截取前文分析的片段 并没有彻底搞清`..

```java
[WMS.addWindow]
    updateFocusedWindowLocked->
        mWindowPlacerLocked.performLayoutLockedInner(displayContent, true /*initial*/,
                            updateInputWindows);
[WindowSurfacePlacer.performLayoutLockedInner]
    mService.mPolicy.beginLayoutLw
    mService.mPolicy.finishLayoutLw();
    mService.mH.sendEmptyMessage(UPDATE_DOCKED_STACK_DIVIDER);

[PhoneWindowManager.beginLayoutLw]
    updateSystemUiVisibilityLw

Session中有SurfaceSession对象用于和surfaceflinger进程通信
```

4. 主线程执行performTraversals流程分析:

performTraversals
    measureHierarchy(这个方法在performTraversals中两次被调用)
        performMeasure
    performLayout
        measureHierarchy
        host.layout
    performDraw

measureHierarchy
    {
    childWidthMeasureSpec//计算
        getRootMeasureSpec //根据window size和root view的layout参数设定 root的尺寸参数 ->源码对应规则
    childHeightMeasureSpec//计算
        getRootMeasureSpec
    performMeasure(childWidthMeasureSpec,childHeightMeasureSpec)//对根view执行测量
        view.measure
            view.onMeasure //这个方法很多ViewGroup的子类会重写，各种布局类型有自己的measure逻辑 分析见`后续章节## onMeasure的流程分析`
                view.getDefaultSize //根据一个基准尺寸和传入的规格，计算width/height
                view.setMeasuredDimension
                    
    }
    return windowSizeMayChange //这个数值根据当前尺寸和测量后host的尺寸是否一致来判定true/false

注意这里的measureHierarchy过程中最多可能执行三次performMeasure，也就是上述{}内部这个流程可能进行三次，首先从一个较小的宽度进行尝试，如果两次都没能达到goodMeasure的目的，直接使用窗口宽度，进行第三次测量。

源码中的解释:
```java
// On large screens, we don't want to allow dialogs to just
// stretch to fill the entire width of the screen to display
// one line of text.  First try doing the layout at a smaller
// size to see if it will fit.
```

performLayout(lp, mWidth, mHeight)
    measureHierarchy // `为什么又要来一次?`
    host.layout(0, 0, host.getMeasuredWidth(), host.getMeasuredHeight())//注意这里的host是DecorView-framelayout
        onLayout //布局内部的子view 分析见`后续章节## onLayout的流程分析`
            framelayout.layoutChildren//
                child.layout//所有child都会调用其layout方法
                    child.onLayout// 具体的view或者viewgroup会重写或者实现该方法
                

performDraw()
    draw(fullRedrawNeeded)
        trackFPS //DEBUG_FPS标志设置的话会记录
        scrollToRectOrFocus
        dirty区域计算
        mAttachInfo.mTreeObserver.dispatchOnDraw();
            OnDrawListener.onDraw
        mAttachInfo.mHardwareRenderer.draw
            updateRootDisplayList
                view.updateDisplayListIfDirty
                    view.draw // draw的流程分析见`后续章节## draw的流程分析`
            nSyncAndDrawFrame
   
## requestLayout 和 invalidate

view.requestLayout
    viewparent.requestLayout        //viewparent实际是viewRootImpl，通过assignParent进行设置的
        viewRootImpl.requestLayout
            checkThread             //必须UI线程
            mLayoutRequested = true //这里会打标记，有了这个标记才会进行measure和layout
            scheduleTraversals     //->doTraversal->performTraversal


view.invalidate()
    view.invalidate(true) //Whether the drawing cache for this view should be invalidated
        invalidateInternal
            viewRootImpl.invalidateChild
                 viewRootImpl.invalidateChildInParent
                    viewRootImpl.invalidate/invalidateRectOnScreen
                         viewRootImpl.scheduleTraversals //最终都是到scheduleTraversals方法

前者会执行measure和layout再进行draw
后者只是draw

view.postInvalidate可以在非UI线程发起，最终UI线程执行view.invalidate方法。


## 控件绘制 View/ViewGroup Measure、Layout、Draw

### onMeasure 流程分析
ViewGroup的onMeasure实现其测量，不同于单个子view只测量自身，viewgroup的子类都对onMeasure进行了重写。
其基本思路如下
对所有的child也就是子view进行如下流程
    measureChildWithMargins(child, ....)
        view.measure->view.onMeasure->view.setMeasuredDimension
然后综合所有child的测量结果，得到其自身的测量结果
    setMeasuredDimension

以framelayout为例进行分析：
1. 遍历children，对所有的child分别调用measureChildWithMargins
2. 如果传入的widthMeasureSpec或者heightMeasureSpec不为exactly，则需要把带有match_parent的child记录一下，加入mMatchParentChildren
  对应如下场景
    framelayout设置的wrapcontent，
    子view的为match_parent，
    因此这种情况下第一轮measure只是确定framelayout的整体尺寸，随后要根据这个具体的尺寸再这些待定子view进一步measure并设置
3. 根据需要对mMatchParentChildren中的child重新measure，此时framelayout的尺寸已经经过前面确定了，因此内部的这些match_parent的尺寸也可以确定了。

以LinearLayout vertical为例分析

vertical类型最终measure实现为measureVertical(widthMeasureSpec, heightMeasureSpec)
本质是调用child的measure并最终确定linearlayout的尺寸，然后setMeasuredDimension。

1. 开始遍历并调用measureChildBeforeLayout进一步调用child的measure
2. 宽度上记录最大宽度
3. 长度上累计child高度，计算totalLength
4. 最后根据宽度和高度以及传入的父类规格，生成新规格，进行setMeasuredDimension

其中会有针对weight的处理，如果父类传入的是exactly，并且子view的useExcessSpace为true(lp.height == 0 && lp.weight > 0)，意味着子view使用剩余空间进行weight分布，第一次循环时会跳过该child不进行measure，因为剩余空间没有确定，待整体的高度确定后再处理weight类控件。
上述场景：linearlayout高度设置matchparent或者固定数值，子view使用weight>0并且没有设置height，不满足上述条件的话第一轮也会measure，但是最终会根据剩余空间进行调整。


### onLayout 流程分析
view的onLayout默认空实现；layout有自己的逻辑
viewGroup的onLayout为抽象方法；//改了父类方法的类别?
ViewGroup的layout方法为final类型，不可重写，其中会调用父类也就是view的layout方法。

- frameLayout的onLayout过程(最早调用的是根布局，frameLayout的子类):

- linearlayout的onLayout过程：

- TextView的onLayout过程:


### draw 流程分析

看源码中的说明，一共包含六个步骤
```java
/*
    * Draw traversal performs several drawing steps which must be executed
    * in the appropriate order:
    *
    *      1. Draw the background
    *      2. If necessary, save the canvas' layers to prepare for fading
    *      3. Draw view's content
    *      4. Draw children
    *      5. If necessary, draw the fading edges and restore layers
    *      6. Draw decorations (scrollbars for instance)
    */
``` 
注意view的draw方法规范了整个绘制流程，如果定义子类一般重写onDraw而不要重写这个方法，如果重写draw也一定要调用super.draw方法。

```java
/**
* When implementing a view, implement
* {@link #onDraw(android.graphics.Canvas)} instead of overriding this method.
* If you do need to override this method, call the superclass version.
*/
```
流程中的2、5根据情况可以跳过

1. drawBackground //draw background
2. canvas.saveLayer 
3. onDraw //draw itself
4. dispatchDraw //draw children
5. canvas.drawRect 
6. onDrawForeground //draw decorations (foreground, scrollbars)

其中 onDraw和dispatchDraw的实际行为都在实现类中重写了，view默认的是空方法。

## APP安装
PMS(PackageManagerService)提供包管理服务
PackageInstallerService提供APP安装服务

pms.installPackageAsUser->
    handler发送INIT_COPY消息处理->
        handle
            handler发送MCS_BOUND消息处理->
                handle
                    params.startCopy()
                        handleStartCopy()
                            installArgs.copyApk()  //apk复制到目录 /data/app/
                        handleReturnCode()
                            pms.processPendingInstall(mArgs, mRet) //解析apk
                              pms.installPackageTracedLI
                                pms.installPackageLI
                                    (替换应用)replacePackageLIF

                                    (新应用)  installNewPackageLIF
                                                    -scanPackageTracedLI //扫描
                                                    -updateSettingsLI    //更新信息



data/system/目录，里面有两个文件
packages.list-手机上安装的所有应用列表
packages.xml-所有应用的设置应用

查看system_server中PMS相关线程
```
shell@CB03:/ $ ps grep system_server
USER     PID   PPID  VSIZE  RSS     WCHAN    PC        NAME
system    881   300   1125064 65688 ffffffff 00000000 S system_server

shell@CB03:/ $ ps -t 881 | grep Package
system    1746  881   1125064 65836 ffffffff 00000000 S PackageManager
system    2553  881   1125064 65836 ffffffff 00000000 S PackageInstalle
```


应用程序安装器：
/packages/apps/PackageInstaller/

PackageInstallerActivity是安装应用的入口
onCreate
    initiateInstall
         startInstallConfirm()
            mInstallConfirm.setVisibility(View.VISIBLE);
            mOk.setOnClickListener(this);
                ok-startInstall()
                    startActivity  InstallAppProgress
InstallAppProgress
    onCreate
        initView
            installExistingPackage/installPackageWithVerificationAndEncryption - 经由binder调用最终进入到PMS-installPackageAsUser
后续开始了apk拷贝解析等。

`这里binder调用在安装的过程中，源activity处于什么状态？`


## 图形绘制相关服务

SurfaceFingler进程

## SurfaceView 和 Canvas

## context获取各种服务

SystemServiceRegistry初始化过程
静态代码块各种registerService
`这里的registerService和SercieManager的addService比较?`

## Activity

## Service

## ContentProvider

## BrocastReceiver

## Intent

#Toast工具类
## DisplayToast
静态内部类实现的单例模式
Toast的封装
单例模式的饿汉、懒汉、静态内部类、静态代码块、枚举5种实现。
懒汉模式的懒加载以及并发安全，普通的synchronized不能应对指令重排策略，双重检测机制或者synchronized+volatile来实现线程安全。
静态内部类加载场景以及时机。

DiaplayToast对比Toast中的静态方法makeText以及实例方法show和cancel

Toast.makeText的时候，默认的gravity
```xml
    <!-- Default Gravity setting for the system Toast view. Equivalent to: Gravity.CENTER_HORIZONTAL | Gravity.BOTTOM -->
    <integer name="config_toastDefaultGravity">0x00000051</integer>

```

## 两种inflate方式 LayoutInflater源码研究

```java

LayoutInflater inflate = (LayoutInflater)
                context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
View v = inflate.inflate(com.android.internal.R.layout.transient_notification, null);


View view = LayoutInflater.from(context).inflate(R.layout.toast_layout, null);

```

getApplication getApplicationContext getBaseContext分析
https://blog.csdn.net/guolin_blog/article/details/47028975
测试下Toast和Dialog使用Activity的this和getBaseContext以及getApplicationContext的异同？

## 判断线程主子分别处理toast
```java
private long mUIThreadId;
mUIThreadId = android.os.Process.myTid();
```

利用Process.mTid() 和 初始化activity生成的变量主线程标识进行比较，来决定是否利用handler.post来实现Toast。一般定义在BaseActivity中。这个过程类似对runOnUiThread的实现流程的一个具体应用，直接runOnUiThread也可以。这个方法对主线程的判断是通过Thread mUiThread这个对象来区分的。同理我们的toast也可以利用这个已有变量来区分。

``不同activity的mUiThread值是否相同。上述那个自定义的mUIThreadId是否也是相同的？``

## ChannelUtil工具类
获取设备标识(读取mac地址，设备标识-imei或者使用64位ANDROID_ID = "android_id")
```java
    /**
     * Returns the unique device ID, for example, the IMEI for GSM and the MEID
     * or ESN for CDMA phones. Return null if device ID is not available.
     *
     * <p>Requires Permission:
     *   {@link android.Manifest.permission#READ_PHONE_STATE READ_PHONE_STATE}
     */
     TelephonyManager的getDeviceId()

     /**
         * A 64-bit number (as a hex string) that is randomly
         * generated when the user first sets up the device and should remain
         * constant for the lifetime of the user's device.
    */       
     device_id = android.provider.Settings.Secure.getString(context.getContentResolver(),android.provider.Settings.Secure.ANDROID_ID);     
```
获取版本号
```
context.getPackageManager().getPackageInfo(context.getPackageName(), 0).versionCode;

```

## cockroach代码研究
地址:https://github.com/android-notes/Cockroach
原理分析:通过install传入一个异常处理方法(接口以及匿名内部类的形式传入，可以用新的接口方法即lamda表达式来代替)，install的过程会对主线程looper进行拦截，构建一个新的loop循环(while循环)，对消息处理，发现是QuitCockroachException的instance则退出新建的loop即return，回到系统自己的loop，否则交给传入的自定义异常处理类处理。这里的拦截是通过向mainlooper发送一个消息进行拦截，发送消息后进行如下操作，获取系统默认异常处理getDefaultUncaughtExceptionHandler保存(用于卸载后恢复原状)，然后设置自定义的默认异常处理，即调用install传入的异常处理类的回调函数进行异常处理。这里更改默认异常处理是为了线程的异常也交给异常处理类的回调来处理。再看install传入的异常处理类的异常方法，主要是toast提示异常信息，这里通过对主线程发消息的方式执行，而不是直接toast，因为这里可能会在子线程抛异常时执行，所以通过向主线程发消息的形式最后在主线程上执行所有最后的操作。
线程有两个异常处理类。其中getUncaughtExceptionHandler会优先返回uncaughtExceptionHandler，如果这个是null返回group，而ThreadGroup继承于Thread.UncaughtExceptionHandler，相当于使用 ThreadGroup的uncaughtException,而这里的uncaughtException先回回溯parent，找到跟然后根据defaultUncaughtExceptionHandler的有无来决定对应操作

``这里有几点疑问``
1. install之前发生的异常肯定就无法捕获了(还没进行拦截操作)
2. native相关异常无法捕获(为何无法拦截？线程模型对本地方法异常的默认处理是什么样的？)

2.0版本利用反射技术，达到更全面的监控。>对ActivityThread、系统流程、以及反射技术需要有全面了解。

``研究下jvm系统对线程异常处理流程``

```java

    // null unless explicitly set
    private volatile UncaughtExceptionHandler uncaughtExceptionHandler;

    // null unless explicitly set
    private static volatile UncaughtExceptionHandler defaultUncaughtExceptionHandler;

    public UncaughtExceptionHandler getUncaughtExceptionHandler() {
    return uncaughtExceptionHandler != null ?
        uncaughtExceptionHandler : group;
}


public class ThreadGroup implements Thread.UncaughtExceptionHandler 
{

.................
    public void uncaughtException(Thread t, Throwable e) {
        if (parent != null) {
            parent.uncaughtException(t, e);
        } else {
            Thread.UncaughtExceptionHandler ueh =
                Thread.getDefaultUncaughtExceptionHandler();
            if (ueh != null) {
                ueh.uncaughtException(t, e);
            } else if (!(e instanceof ThreadDeath)) {
                System.err.print("Exception in thread \""
                                 + t.getName() + "\" ");
                e.printStackTrace(System.err);
            }
        }

}


```

## FragmentActivity提出的目的？
对应fragment的使用有差别。
fragment对3.0Honeycomb前后支持差异。

# 基本控件源码分析
1. Toast
2. Dialog 活动周期？
3. PopUpWindow
4. Activity
5. Fragment
6. View & subclasses
7. Service 
8. broadcastreceiver

# 基本工具类源码分析

# Android profiler实战 内存使用分析
退出后依然看得到？如何完全退出？

#Android 消息循环机制
进一步，消息队列阻塞读(确认下一是否阻塞读取，虽然不一定阻塞多久，一般情况下消息应该是连续很多地读不尽的直到程序终止)如何实现的，c、java、Android、系统层面如何做到。
java的wait和notify底层实现?
android消息循环中nativePollOnce和nativeWake方法到c++层使用epoll方式，超时阻塞以及唤醒。

## looper和handler源码分析

Looper/Handler/Message/MessageQueue

对于实现消息循环的线程，需要有一个looper对象，prepare方法进行初始化操作(线程和looper关联，初始化looper的消息队列)，loop方法开启消息循环。
loop方法基本流程描述：
从实例变量MessageQueue对象mQueue中取消息(队列的next方法)，取到了message则开始处理消息，循环执行上述过程。如果next返回了message但是message为null，会退出上述循环，这就是Looper的quit机制。message不为null则进行消息处理，这个消息是通过handler发送的，发送后会放到队列里来，message的处理我们可以简单认为调用handler的handleMessage方法，因为这是我们使用handler时最常用的方式。

loop方法主要流程：

1. 通过mQueue.next()获取队列中的消息message
2. message为null,则函数返回，循环结束，否则执行3。(这里一定是调用了looper的quit方法，才出现这种message为null的情况)
3. 调用message.tartget.dispatchMessage方法处理消息，即handler得dispatchMessage方法。随后回到1，继续循环。

其中handler的dispatchMessage方法:
1. 首先判断msg的callBack是否为null,优先使用msg的callBack进行处理(是一个runnable对象)并返回；
2. 次选地，如果当前handler设置了mCallback(一个handleMessage的接口)，则执行mCallback.handleMessage并返回；
3. 最后地, 1、2都不满足，则使用handler自身的handleMessage方法处理。

其中1场景，在handler.post(Runnable)的时候会生成一个自带runnable的msg；
2场景，new Handler的可以指定mCallback。
3场景，自定义handler类重写handleMessage方法。(比较常见的用法，但会生成新的类)

进一步，上述用到队列的next方法，此方法是阻塞的，直到队列中有消息才返回。
next方法主要流程:

1. 初始化nextPollTimeoutMillis=0，开始循环：
2. nativePollOnce(ptr, nextPollTimeoutMillis);表示等待指定时间;
3. 从队列头开始检查，如果首个消息的时间戳小于等于当前时间，说明此消息可以被执行，返回消息，调整队列;否则计算等待时间(过一段时间再来检查)，跳转2;如果没有消息，nextPollTimeoutMillis = -1，转2(下次等待超时设置为-1，表示没有超时限制，直到有新消息进入队列进行唤醒操作才会结束阻塞)

(上述过程进行了简化，没有考虑同步消息、异步消息、以及消息屏障相关内容)

队列中的消息来源，通过和Looper对象关联的handler进行消息发送：
首先，handler的创建，分析源码，handler的创建可以指定looper,如果不指定，则会使用当前线程的looper。如果当前线程没有looper的话(没有调用Looper.prepare),则不允许创建，会抛出异常。
其次，handler发送消息的过程：
一种是通过sendMessage(sendMessagexxx、sendEmptyMessageXXX等方法)
另一种是通过post(Runnable runnable)方法；
两者最终都使用sendMessageAtTime方法，调用handler的enqueueMessage，最终使用相关联looper的消息队列queue的enqueueMessage方法放入到消息队列中。
消息入队会根据执行时间进行查找合适的位置，根据需要判断是否needWake，如果需要，则进行nativeAwake进行唤醒阻塞的取消息方法。


ThreadLocal

下面是对ThreadLocal的解释
```java
/**
 * Implements a thread-local storage, that is, a variable for which each thread
 * has its own value. All threads share the same {@code ThreadLocal} object,
 * but each sees a different value when accessing it, and changes made by one
 * thread do not affect the other threads. The implementation supports
 * {@code null} values.
 *
 * @see java.lang.Thread
 * @author Bob Lee
 */
```
简而言之，就是所有的线程可以共用一个threadlocal的对象，通过这个对象保存归属于每个线程自己变量。在不同的线程中通过同一个threadlocal对象的get和set可以获取或者设置对应线程的的待存储变量。
具体通过get和set方法可以分析实现机制，每个thread对象都有一个本地的ThreadLocalMap(内部有一个Entry数组)，threadlocal的get和set操作，会首先获取当前线程thread，然后以threadlocal对象为key生成entry加入到对应threadlocalmap(set操作)或者从threadlocalmap中获取对应key值的entry，返回value(get操作)。

-Looper中使用了一个全局的ThreadLocal变量(即类型为static final,为所有looper对象共享)，其中get和set会将当前Looper对象存到当前Thread对象的ThreadLocalMap(内部有一个entry数组)中，entry的key为这个static final的ThreadLocal对象，源码中是ThreadLocal<Looper> sThreadLocal，value即当前这个looper对象。

```java
static final ThreadLocal<Looper> sThreadLocal = new ThreadLocal<Looper>();
```

Looper的static的prepare方法会对sThreadLocal进行操作，并会判断之前是否设置过looper。
获取UI线程，主线程。Looper.getMainLooper().getThread()即可。
判断是否为主线程的两种方法：
Looper.getMainLooper().getThread() == Thread.currentThread();
Looper.myLooper() == Looper.getMainLooper()；

子线程中想要更新UI，使用runOnUiThread,实际是通过Activity中的mHandler对象向主looper发送消息，这里使用的handler的post方法。

延伸点：message的obtain复用实现、ThreadHandler、IntentService等的实现。

`不错的参考资料`
renyugang 消息循环解析，概要介绍、提到HandlerThread、IntentService等
https://blog.csdn.net/singwhatiwanna/article/details/17361775

更为深入的探讨消息循环，涉及handler、looper、messageQueue、同步分隔栏(postSyncBarrier&removeSyncBarrier)、进入不nativeWake()和nativePollOnce()的底层(c++)实现，epoll机制。
https://my.oschina.net/youranhongcha/blog/492591

延伸项：
Messenger(handler中有这个东西，延伸用法,getIMessenger())
HandlerThread(可以使用handler的thread,自带looper，不需要我们去设计looper)
IntentService(进一步有IntentService的实现使用了HadlerThread)
## Handler vs Messenger vs aidl


##httpclient legacy使用
org.apache.http.legacy.jar对应源码
https://android.googlesource.com/platform/external/apache-http/+/master/src/org/apache/http/

直接看不到源码，看到的class提示throw new RuntimeException("Stub!");实际是Android.jar对一些细节实现进行了屏蔽，在实际运行的时候通过反射等动态机制进行ROM里实际方法的替换。在aosp(android opensource system project )查看系统源码。https://android.googlesource.com/

```查看网络请求源码底层，选择urlconnection，httpclient，okhttp进行综合比较。```
```好的工具类util包探索```

#java获取当前类名和函数名的方法。
利用反射或者利用getStackTrace方法，注意方法层级，尤其是进行了log封装之后。

##Android sdk中的binder调用

##Toast源码解析
hack Toast的时间长度的几种策略
https://www.cnblogs.com/net168/p/4058193.html
1. 通过反射，修改Toast的默认show方法，直接调用内部TN的方法。
2. 通过TN的show和hide原理来实现自定义的Toast，借助windowmanager
3. 持续发送toast，对于小于long的，则可以发送long的toast然后隔一段时间取消，来控制时间，大于long的就得通过循环发送来控制了。

默认Toast主要代码，Toast源码sdk中可找到，下述是aidl调用到的server端代码
https://android.googlesource.com/platform/frameworks/base/+/f76a50c/services/java/com/android/server/NotificationManagerService.java


#Activity和Fragment生命周期管理
自定义ActivityManager和系统类ActivityManager
系统如何管理activity的，分别结合client和server端代码分析理解。

## 单例模式懒加载DCL的探索
1.5前后volatie关键字引入，编译器优化影响，指令重排问题。

##获取设备信息

``` 
    #model型号 brand厂商 name名称
    adb shell getprop ro.product.model
    APOS A8

    adb shell getprop ro.product.brand
    LANDI

    adb shell getprop ro.product.name
    APOS A8

    C:\Users\ywb>adb shell cat /system/build.prop | findstr product
    ro.product.model=APOS A8
    ro.product.brand=LANDI
    ro.product.name=APOS_A8
    ro.product.device=APOS_A8
    ro.product.board=msm8909
    # ro.product.cpu.abi and ro.product.cpu.abi2 are obsolete,
    # use ro.product.cpu.abilist instead.
    ro.product.cpu.abi=armeabi-v7a
    ro.product.cpu.abi2=armeabi
    ro.product.cpu.abilist=armeabi-v7a,armeabi
    ro.product.cpu.abilist32=armeabi-v7a,armeabi
    ro.product.cpu.abilist64=
    ro.product.manufacturer=LANDI
    ro.product.locale.language=zh
    ro.product.locale.region=CN
    # ro.build.product is obsolete; use ro.product.device
    ro.build.product=APOS_A8
```
## adb 命令(shell) 总结

1. 安装提示错误 INSTALL_FAILED_INSUFFICIENT_STORAGE
adb shell df 查看设备空间，发现/data下空间不足，卸载一些程序在安装



## android.util中懒加载的单例模板类
```java
/**
 * Singleton helper class for lazily initialization.
 *
 * Modeled after frameworks/base/include/utils/Singleton.h
 *
 * @hide
 */
public abstract class Singleton<T> {
    private T mInstance;

    protected abstract T create();

    public final T get() {
        synchronized (this) {
            if (mInstance == null) {
                mInstance = create();
            }
            return mInstance;
        }
    }
}
```

## android进程间通信
AMS(AMP proxy)的startActivity,消息通信，同步异步？阻塞还是马上返回?
Instrumentation 的execStartActivity 同步异步？
最终通信通过dump or nativeWriteString?
操作系统级别进程线程通信方式？

## android studio 快速查找的缺陷
ctrl + N 查找class，看不到一些系统类，src中有这些，通过ctrl+shift+N进行文件名查找
还有一些属于内部类，不能直接找到，应先找其外部类。

##activity application这些组件的生命周期调用，外部的控制，模板的实现原理。

##activity 启动过程

binder client->server
                      ActivityManagerService
ActivityManagerProxy
ActivityManagerNative

binder server->client
ApplicationThread
                      ApplicationThreadNative
                      ApplicationThreadProxy

ActivityThread
   H handleMessage

``注意api26和23已经有了差异，部分Native和Proxy过时被移除，简化了设计``

## Activity的startActivity和 getApplicationContext.StartActivity()区别?
最后都是通过Instrumentation 的execStartActivity，有什么区别，对于后者进行分析，涉及到baseContext的赋值，在app启动阶段。
解答：最终都要通过instrumentation对象进行execStartActivity方法，前者是activity创建的时候进行attach来关联的，关联的对象就是ActivityThread中的instrumentation；后者通过mMainThread.getInstrumentation()获取对象，两者是同一个对象。

##synchronized 锁的是什么，具体底层的实现，延申到各种锁以及线程同步问题

##源码分析，一些关键环节
startactivity
startservice
bindservice
registerreceiver
sendbroadcast
aidl 
binder
ams
activitystack
源码分析后绘制uml图
框架流程的角度，了解数据走向
设计模式角度，分析设计方法


##修改git HTTP post buffer
一些基本操作https://www.jianshu.com/p/729206b5770a
git config --global http.postBuffer 524288000
git config --global user.name wenburgyan
git config --global user.email wenboyan2014@163.com
查看配置
git config -l
也可以查看或者编辑user目录下的.gitconfig文件来实现上述操作

## listview scrollview nestedscrollview
```java
    * <p>Scroll view supports vertical scrolling only. For horizontal scrolling,
    * use {@link HorizontalScrollView} instead.</p>
    *
    * <p>Never add a {@link android.support.v7.widget.RecyclerView} or {@link ListView} to
    * a scroll view. Doing so results in poor user interface performance and a poor user
    * experience.</p>
    *
    * <p class="note">
    * For vertical scrolling, consider {@link android.support.v4.widget.NestedScrollView}
    * instead of scroll view which offers greater user interface flexibility and
    * support for the material design scrolling patterns.</p>
```

##一些习以为常的认知的思考、持续更新
1. fragment的意义
2. mvp mvc mvvm databinding lifecircle 框架公开化标准化也就在一定程度上意味着过时，透过现象抓住本质

## 一些练习
1. 最小原型app(调研一些app)
2. 尝试开发模式的利用mvp，mvvm
3. 尝试instant app开发
4. 尝试flutter dart开发
5. 自己的后台搭建，java？dart？go？后台接口
6. 各种图解，知识脉络，脑图，uml。

## mvp
几点概括
1. 不要将传统三层架构(表示层、逻辑层、数据访问及持久化层)和mvc mvp生硬对应
2. model层并非单单数据实体，也可以有业务逻辑
3. mvc最早提出于1980s,主要桌面GUI程序，后续再web应用中推广。主要目的，代码复用，解耦。
4. mvp，作为mvc的派生，增强展示逻辑中的seperate concerns，更方便的单元测试。[from wiki``MVP is a user interface architectural pattern engineered to facilitate automated unit testing and improve the separation of concerns in presentation logic``]
5. 一般mvp有passive view和 supervisor 两种模式，前者view-model不直接交互，由presenter中介来做，后者部分逻辑会在view中，view-model会有交互，可通过数据绑定，这种情况下和mvc到比较相似。
6. Android app本身的框架，xml+activity是一种mvc模式，其gui设计是否基于mvc需要研究下系统框架。
7. 我们所讨论的Android应用开发时使用的mvp，mvc是我们对应用代码进行的一个逻辑划分。
8. Android中mvp的使用，activity或者fragment属于view层，实现iview的接口，其中引用presenter，使用presenter进行逻辑处理，隔离model和view，presenter中拥有view和model的引用，调用model进行数据请求，通过iview接口引用来进行界面更新，iview接口的实现一般在view层。有种说法，mvp只对presentation layer的一个分层，更完整的看mvp clean。
9. mvp重点进行解耦，关注点分离，便于更好的单元测试，就Android而言，会使得单元测试更为容易。
10. 进一步有mvp clean，提供domain，usecase等。
11. 实用起来不应该有严格的界限，本身在Android上没有严格的标准。
12. app所谓架构的问题，酌情使用，综合考虑。
https://antonioleiva.com/mvp-android/
https://fernandocejas.com/2014/09/03/architecting-android-the-clean-way/
https://fernandocejas.com/2015/07/18/architecting-android-the-evolution/
https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html
https://www.slideshare.net/PedroVicenteGmezSnch/effective-android-ui-english
https://www.slideshare.net/PedroVicenteGmezSnch/software-design-patterns-on-android


```css
所谓架构的目的：
Easy to maintain.
Easy to test.
Very cohesive.
Decoupled.

软件设计注意事项
Respect SOLID principles.
Do not over think (do not do over engineering).
Be pragmatic.
Minimize framework (android) dependencies in your project as much as you can.
```
## launch mode 和 intent flag
Android doc中有介绍，一些规则，归根结底看源码中的处理规则。

## activitystack taskrecord activityrecord processrecord
taskrecord 任务栈

## 装饰器模式和继承
https://en.wikipedia.org/wiki/Decorator_pattern
decorator作为subclass的一种替换方案。
contex的具体实现，一些组件的实现使用了装饰器模式。



## intent filter 作用原理
启动标识，launcher程序会用到，用于解析决定图标点击后启动哪个应用的哪个activity

##apk安装过程相关
pms启动以及安装核心过程
https://maoao530.github.io/2017/01/10/packagemanager/
应用程序安装过程
https://maoao530.github.io/2017/01/18/package-install/
http://liuwangshu.cn/framework/pms/1-packageinstaller-initialize.html
http://liuwangshu.cn/framework/pms/2-packageinstaller-apk.html
http://www.cnblogs.com/Jax/p/6910745.html



##Android 学习路线
renyugang https://blog.csdn.net/singwhatiwanna/article/details/49560409
艺术探索

view和控件类
xvyisheng https://blog.csdn.net/column/details/androidui.html(群英传)
http://hencoder.com/ui-1-1/(扔物线)

源码类
老罗csdn
http://gityuan.com/friends/ 列表

## aidl与序列化(serializable parcelable)

## contentprovider，broadcastreceiver，service的基本使用

## socket 跨进程通信的思考
socket的实现原理

## java 注解实现原理，控制反转，依赖注入等。

## Android di框架

## Android 系统源码调试

## Android view 框架
1. drag view的实现？
2. view的scrollTo 和 scrollBy是针对view的content，而不是view自身位置的移动。viewgroup则会移动内部的子view。想要变换view的位置(在父容器中的位置)，使用其他方式，比如offsetLeftAndRight(offsetTopAndBottom)等。
3. scroller是一个"数值变换发生器"，提供一定形式的曲线函数和实时计算，view的computeScroll预留和scroller配合实现平滑的scrollTo或者scrollBy。
4. ObjectAnimator属性动画

5. view、viewgroup事件分发，父子布局空间传递，重叠空间事件分发。
基本事件传递(`事件如何触发activity的dispatch方法这里不涉及`，只包含从activity处理开始后续环节)
activity的dispatchTouchEvent:
A1. 如果是down事件，触发onUserInteraction函数，此函数用户重写定义行为,否则直接下一步;
A2. 调用getWindow().superDispatchTouchEvent，如果其调用返回值为true，则直接返回true，否则下一步；
A3. 调用activity的onTouchEvent函数，将其返回值返回。(一般情况下activity的onTouchEvent返回false)；
其中A2，getWindow()得到的是PhoneWindow,进一步调用decorview(继承自framelayout，属于ViewGroup)的superDispatchTouchEvent，再调用super.dispatch,因此，A2会调用ViewGroup的dispatchTouchEvent。

ViewGroup的dispatchTouchEvent主要环节：
A1. 初始 handled = false
A2. 如果是down事件，清空之前状态cancelAndClearTouchTargets, resetTouchState,这两个方法具体细节不表，我们主要关心两处FLAG_DISALLOW_INTERCEPT重置和mFirstTouchTarget = null，否则下一步;
A3. 计算intercepted(根据字面含义，viewgroup是否拦截事件，如果拦截就不会再从子view中寻找处理对象)，计算过程如下
A3-1. 如果down事件或者mFirstTouchTarget不为空，判断disallowIntercept。disallowIntercept为true，则intercepted为false，否则将onInterceptTouchEvent(ev)赋值为intercepted，此方法默认返回false;
(如果发生down事件，前面说了会重置一些数据，mFirstTouchTarget必定也为空；如果不是down事件但mFirstTouchTarget为空，对应场景为：之前的down事件没有找到子view处理，也就是A4过程没有找到mFirstTouchTarget)。
A3-2. 不满足A3-1的判断条件，即这不是一个按下事件并且之前没有给mFirstTouchTarget赋值，则intercepted赋值true;
A4. 如果intercepted为false,说明不拦截，对于down事件，从子view中寻找处理对象，从上向下遍历，根据触摸区域，会将事件分发给对应区域的子view，并记录能处理的第一个子view，退出分发查找过程，用mFirstTouchTarget标记，对应方法addTouchTarget。否则下一步。 
A5.判断mFirstTouchTarget，如果为空，交由父类处理，通过dispatchTransformedTouchEvent调用父类的也就是view的dispatchTouchEvent，表示viewgroup自己处理该事件，并将返回值赋给handled。否则下一步
A6.mFirstTouchTarget不为空，说明前面的down事件在A3-2中找到了处理子view，通过dispatchTransformedTouchEvent调用该子view的dispatchTouchEvent，并将返回值赋给handled。注意子view可能是view也可能是viewgroup。(场景分析：如果是down事件走到这里，说明在A3-1中找到了target，而且已经向其分发过事件，这里实际上通过判断alreadyDispatchedToNewTouchTarget发现之前分发过而且子view能处理，就会直接对handled赋值true，而不会再去调用dispatchTransformedTouchEvent；如果不是down事件走到这里，说明是down事件后续的move和up，则会通过通过dispatchTransformedTouchEvent调用该子view的dispatchTouchEvent，并将返回值赋给handled。)
A7.返回handled。

View的dispatchTouchEvent主要环节(对代码流程进行了一定扩展以便于描述，更易于理解，实际代码可能比较简练晦涩):
A1. 初始result为false。(result为最终要返回的数值，表示是否处理了该事件)
A2. 检查是否设置了onTouchListener,如果设置有则执行A3,否则A4.
A3. 检测控件是否enable，enable的话才去调用onTouchListener，如果listener的onTouch返回true，则对result进行赋值true。
A4. 检查result，如果不为true，则执行onTouchEvent,如果该方法返回true，则对result赋值true。
A5. 返回result

onTouchEvent方法中默认行为：检测控件的是否满足以下条件之CLICKABLE或者LONGCLICKABLE或者CONTEXT_CLICKABLE，满足之前就会进行事件处理并返回true，否则返回false。在onTouchEvent的的actionUp事件处理中会调用onClick的listener(如果设置了的话)。

总结，一般情况下view应该是enable的，处理过程可以简化为：首先调用listener的onTouch(设置了的话)，如果为false，才会再去调用view的onTouchEvent，如果是clickable的话，一定会返回true，否则false。clickable的情况下对不同事件具体处理，actionUp的时候会触发click的listener。关于clickable属性，view默认的是不可点击，button是可点击，这个在view初始化的时候会加载属性。我们也可以主动设置，代码设置或者xml中设置。

参考：
https://blog.csdn.net/yanbober/article/details/45887547
http://gityuan.com/2015/09/19/android-touch/


事件分发若干解析：
https://blog.csdn.net/yanbober/article/details/45887547
https://blog.csdn.net/yanbober/article/details/45912661
https://blog.csdn.net/yanbober/article/details/45932123

https://blog.csdn.net/lmj623565791/article/details/39102591
http://gityuan.com/2015/09/19/android-touch/

https://blog.csdn.net/singwhatiwanna/article/details/17339857

https://blog.csdn.net/chunqiuwei/article/details/50164791
https://blog.csdn.net/chunqiuwei/article/details/41084921

1分析各种常见场景实例加深理解。
2低版本源码看起来更简单,高版本许多附加判断或者新增安全机制，不好把握重点。

一些场景:
view设置disable的话，即使设置了onTouchListener也不会调用，进而执行onTouchEvent，在onTouchEvent中默认行为如下：如果按钮可以点击(满足三者中的一个CLICKABLE、LONG_CLICKABLE、CONTEXT_CLICKABLE)，则会消费事件(return true，无论view是否disable)。如果view enable才会在action up的时候performClick。`其中longclick的处理也可以思考下`

6. findviewbyid的实现
通过activity的findviewbyid方法->window.fbd->decorview.fbd->view.fbd->view.findViewTraversal（由于decorview是viewgroup，这里实际上调用的是viewgroup的findViewTraversal）
最终调用viewgroup的findViewTraversal，会对children进行遍历，依此调用view的fbd->findViewTraversal,找到符合id的view并返回，否则返回null。
`注意decorview是在setcontentview中初始化的，最后都会用到inflate和addview两个方法，前者从xml得到view(也可能把这个view加到parent中)，后者向父容器添加子view`。

7. setcontentview的实现，布局空间初始化过程，结合activity的创建过程，context的创建等。
调用getWindow().setContentView,这里的mWindow实际是PhoneWindow对象，  最终到phoneWindow的setContentView。`Activity的attach方法中对mWindow进行初始化,这里不分析activity生成过程，以及attach方法调用的时机`
具体过程：
A1. 判断mContentParent，为空则installDecor来初始化mContentParent，否则移除mContentParent的所有子view。
A2. 填充mContentParent内容，通过inflate方法将指定id的xml资源初始化，并添加到mContentParent这个viewgroup中。
A3. 回调activity的onContentChanged方法，默认行为空。`这里的Callback对象cb是在Activity的attach方法中，mWindow初始化后，通过mWindow.setCallback(this)设置的，实际行为是activity的onContentChanged方法`

其中,A1的installDecor具体行为如下：
A1-1. new一个decorview(继承自framelayout)，[首先判断mDecor，为空，则mDecor = generateDecor(-1);否则利用已有的decorview设置对应窗体:mDecor.setWindow(this);]
A1-2. mContentParent赋值[判断mContentParent，为空则mContentParent = generateLayout(mDecor);]
//A1-3. mLayoutInflater.inflate(layoutResID, mContentParent);布局文件实例化，并添加到mContentParent中。

A1-2中mContentParent的生成过程，也就是 generateLayout(mDecor)的详细过程:
A1-2-1. 首先根据主题选择对应的窗体框架资源，即layoutResource的赋值，布局中一般都要包含一个id为content的framelayout。 //这也是我们通过代码设置activity窗体属性的时候，要在setcontentview之前的原因.
A1-2-2. 然后mDecor.onResourcesLoaded将对应窗体框架初始化，可以理解为inflate窗体框架资源文件后添加到添加到decorview中(并将实例化的资源root赋值给DecorView中的成员mContentRoot)。
A1-2-3. 然后赋值contentParent，从window也就是decorview中找id为content的viewgroup，也就是前面描述的framelayout。返回contentParent。

A1-2-2 DecorView的onResourcesLoaded过程如下
通过final View root = inflater.inflate(layoutResource, null);获得实例化view。
试着生成mDecorCaptionView，如果生成，则添加到DecorView中，并将root添加到mDecorCaptionView中。
如果mDecorCaptionView为空，直接通过add将root加到DecorView中。

`setcontentview后，界面并没有显示，实际的显示是在ActivityThread.的handleResumeActivity，会调用activity.makeVisible()最终会mDecor.setVisibility(View.VISIBLE)，这里才显示。`


```java
    // This is the view in which the window contents are placed. It is either
    // mDecor itself, or a child of mDecor where the contents go.
    ViewGroup mContentParent;


    //generateLayout(DecorView decor)中关键部分，模板资源加载部分
    mDecor.startChanging();
    mDecor.onResourcesLoaded(mLayoutInflater, layoutResource);//mLayoutInflater在window初始化的时候赋值，即activity的attach方法中，layoutResource在此处代码前面通过主题判断进行资源文件选择
    ViewGroup contentParent = (ViewGroup)findViewById(ID_ANDROID_CONTENT);
    //....
    //....
    //generateLayout(DecorView decor)结果返回部分
    mDecor.finishChanging();
    return contentParent;
```

8. button初始化的时候会根据默认的buttonstyle把clickable设置成true。看构造函数即可找到。对应的参数com.android.internal.R.attr.buttonStyle根据主题找到buttonStyle可以看到clickable设定为true。类似的去看textview，对应的文件没有设置clickable，再去看imageview，对应参数为0，直接使用父类view的初始化。

9. view绘制流程
(setcontentview流程中有一个addview方法会触发绘制，调用performtraversal)
正常view绘制依次触发三个关键方法:onMeasure(), onLayout(), onDraw(),分别是计算控件尺寸，计算布局位置，绘制控件。
ViewRootImpl是所有布局的根。其中performTraversals是view绘制流程的重点函数，其中会调用到，performMeasure，performLayout,performDraw等方法，这三个方法最终会调用各个子view的onmeasure、onlayout、ondraw方法。

view的requestLayout会递归调用，直到根ViewRootImpl的scheduleTraversals，这里会通过handler发送消息，异步调用到ViewRootImpl的doTraversal，doTraversals会调用performTraversals进行绘制，requestlayout的时候设置了一些参数，会执行onMeasure和onLayout，不会执行onDraw()(这一点待确定...)
view的invalidate或者postInvalidate同步或者异步的形式，最后会调用viewgroup的invalidateChild，在这个方法里会调用invalidateChildInParent(viewgroup中有方法的具体实现)，do..while..直到根布局的invalidateChildInParent，通过invalidateRectOnScreen调用scheduleTraversals，类似前面的，此方法最终会到performTraversals触发绘制。
Activity-Window-PhoneWindow-DecorView-布局中的contentid为我们的xml布局-ViewGroup以及view

10. LayoutInflater过程和原理
获取LayoutInflater实例， 通过LayoutInflator.from方法，进一步会调用context.getSystemService(Context.LAYOUT_INFLATER_SERVICE)，因此这两种方法实质一样的。
得到一个LayoutInflater对象后，可以mLayoutInflator.inflate进行实例化view，三个参数，资源id、root、attachToRoot。
首先result = root;资源解析得到的为tmp。
如果root不为空，且不绑定父布局，则会获取其布局参数，并将设置给资源文件得到的tmp；
root不空，且绑定父布局，调用root.addView，addview的时候会用到root的布局参数。
root为空，或者不绑定父布局，result赋值资源解析出的view，也就是把上面那个tmp给result。
最后返回result。

`参考链接 https://blog.csdn.net/yanbober/article/details/45970721`

上述的分析过程可以很好地应用到一个listview中item的使用场景：
一般这个item的布局文件要有一个总的layout包裹，否则inflate的时候可能有各种问题，比如布局中的尺寸设置可能失效。通过最外层包裹，使得最外层尺寸参数可能失效，但是内部尺寸参数得以保持。
是否失效在于inflate返回result之前是否对tmp设置params即temp.setLayoutParams(params)或者执行root.addView(temp, params)，这里有许多不同场景，根据实际源码并结合params的生成进行分析。


又因为listview的addview是默认抛异常的，所以inflate的时候参数配合不能触发parent的addview操作，否则会有异常。`为什么AdapterView禁止addview操作?设计原因?`


inflate最终都要执行如下过程：
1. inflate(@LayoutRes int resource, @Nullable ViewGroup root, boolean attachToRoot)，对资源文件进行xml解析，得到parser,进入到下一步；
2. inflate(XmlPullParser parser, @Nullable ViewGroup root, boolean attachToRoot),首先找到根布局tmp，遍历children，最后返回root或者tmp。
rInflateChildren是2中会用到的遍历子布局的方法。
rInflateChildren->rInflate->rInflateChildren递归调用。每个view遍历结束都会执行view的onFinishInflate。



```java
   View inflate(XmlPullParser parser, @Nullable ViewGroup root, boolean attachToRoot)说明
   
    /**
     * Inflate a new view hierarchy from the specified XML node. Throws
     * {@link InflateException} if there is an error.
     * <p>
     * <em><strong>Important</strong></em>&nbsp;&nbsp;&nbsp;For performance
     * reasons, view inflation relies heavily on pre-processing of XML files
     * that is done at build time. Therefore, it is not currently possible to
     * use LayoutInflater with an XmlPullParser over a plain XML file at runtime.
     * 
     * @param parser XML dom node containing the description of the view
     *        hierarchy.
     * @param root Optional view to be the parent of the generated hierarchy (if
     *        <em>attachToRoot</em> is true), or else simply an object that
     *        provides a set of LayoutParams values for root of the returned
     *        hierarchy (if <em>attachToRoot</em> is false.)
     * @param attachToRoot Whether the inflated hierarchy should be attached to
     *        the root parameter? If false, root is only used to create the
     *        correct subclass of LayoutParams for the root view in the XML.
     * @return The root View of the inflated hierarchy. If root was supplied and
     *         attachToRoot is true, this is root; otherwise it is the root of
     *         the inflated XML file.
     */
     
```

11. statusbar的处理
https://developer.android.com/training/system-ui/status#java
4.0之前可以通过代码或者xml的theme来实现。
4.0之后通过代码来实现。

12. window的布局
statusbar在什么时候生成的？
viewrootImp的初始化和performTraversals的调用时机？(visibility变化会触发绘制,decorview设置visible)
activity-window-decorview-xml content;
Theme和style的加载时机：
installDecor->generateLayout的时候：
TypedArray a = getWindowStyle();
这里的getwindowstyle获取window的一些属性设置，此方法会先找到activity的theme，然后获取com.android.internal.R.styleable.Window这个属性组对应的属性。
后续会根据主题下这些属性的数值进行窗体设置。
theme的设置(赋值)是在ActivityThread启动activity的时候根据xml内容进行的设置，或者activity中代码设置主题。

values.xml -> 各种theme,根theme。
attrs.xml-> 各种属性定义。
`https://blog.csdn.net/yanbober/article/details/51015630`
R.style-R.styleable-R.attr

13. view viewgroup的measure,layout，draw细节
https://blog.csdn.net/yanbober/article/details/46128379
http://liuwangshu.cn/application/view/7-measure-sourcecode.html
根据linearlayout的layout方法，可以看到，如果orientation是vertical的话，layout的gravity属性中只能选则bottom、top、center_vertical,其他属性无效，也就是center_horizonal无效，如果想使得child剧中，利用child的layout_gravity属性设置，此情况下child的layout_gravity只有水平方向的有效，也就是right、left、center_horizonal。可以这样理解，父布局垂直的，则其gravity不应在处理水平的设置信息，进而把水平方向的设置交给child的layout_gravity属性。这样便于理解，根本原因是源码就是这样控制的，也就决定了其行为。

onMeasure计算尺寸
onLayout计算布局位置
看下细节，细节设计决定了使用的规则。
requestLayout不会触发onDraw。
invalidate会调用onDraw。

MeasureSpec规格，高两位表示模式：UNSPECIFIED,EXACTLY,AT_MOST,低30位表示尺寸。
子view的MeasureSpec是由其layoutpara和父group的MeasureSpec共同决定的。最根部ViewRootImpl是初始设定的,通过getRootMeasureSpec方法获取，mode为exactly，size为屏幕大小，layoutpara均为为match_parent。

ViewGroup的onMeasure一般在具体的实现类中重写逻辑。比如linearlayout的onMeasure中有measureVertical和measureHorizontal方法。父类的measure具体过程一般是遍历子布局，会根据viewgroup的MeasureSpec，子view的布局参数(也有父布局的padding等参数)生成子view的MeasureSpec，比如getChildMeasureSpec这个方法就会用来生成子view的measure参数。
所有的measure只是设定尺寸参数，这些参数会在layout中使用。
关于getChildMeasureSpec可以仔细看一下，对应分析这样一种场景，viewgroup设定wrapcontent,内部view设定matchparent,控件什么行为？通过源码分析会看到，此时内部view设定matchaparent和wrapcontent表现行为一样。1.外部viewgroup的wrapcontent会使得其measure参数模式为AT_MOST,进而内部view的measure也会变为AT_MOST。

14. 滑动的实现？
listview滑动的实现
嵌套listview的滑动事件处理。
onInterceptTouchEvent方法说明，父布局夺取子view的事件处理权，最终会到viewgroup的onTouchEvent方法。如果当前有target作为目标处理器，则target会先收到一个ACTION_CANCEL事件，后续事件不会到原先的target。
```java
/**    Return true to steal motion events from the children and have
     * them dispatched to this ViewGroup through onTouchEvent().
     * The current target will receive an ACTION_CANCEL event, and no further
     * messages will be delivered here.
     */
```



## listview源码
id 和 position的区别，看了实现细节自然清楚了。


## using math.net to do data fit.
非常强大的一个数学库，可以学习下源代码，对照数学知识。
math.net结合zedgraph进行绘制，一种数学分析+曲线绘制的组合。

## Android studio常用快捷键
括号匹配 ctrl+shift+M
搜索action ctrl+shift+A
快速检索 double shift
查找类 ctrl+N
查找文件 ctrl+shift+N

重写方法 ctrl+O
方法导览 ctrl+F12

注释 ctrl+/
类或方法说明 ctrl+Q
自动补全提示 ctrl+shift+space
操作提示 alt+enter

查找方法的具体实现 ctrl+alt+B (find implementation)
查找引用 alt+F7， ctrl+shift+alt+F7  (ctrl+鼠标点击)
跳转到变量定义或者方法原型(ctrl+鼠标点击)

添加书签 F11、Ctrl+F11
查看书签列表 Shit+F11

## android studio & gradle
1. 项目加载过程
gradle版本，gradle-plugin,sdk,build-tools;
dependency deal;
test deal;
每一步走什么

## hencoder学习记录
1. canvas和paint
paint：setStyle,setColor,setStrokeWidth,setStrokeCap,setTextSize,setAntiAlias
canvas:drawColor,drawRect,drawRoundRect,drawCircle,drawOval,drawLine,drawLines,drawArc,drawPoint,drawPoints,drawPath
path:addXX，XXto,close.

## 问题列表
1. AppCompatActivity和Activity，前者有ActionBar，后者是TitleBar
2. 模板，模板参数，继承。
3. onEditorAction
4. 几种实现加载框的方式，dialog和progressbar的各种子类，自定义view。
5. 矢量图制作
6. textAppearanceSmall 系统属性
7. 墨刀 & 蓝湖
8. jcenter和maven库比较
9. gradle build tools  & gradle plugin & gradle & android studio 的version匹配，蛋疼死!!
10. 状态栏问题，沉浸式，actionbar和toolbar
11. 模块化，组件化，路由AROUTER
12. 插件化
13. listview和edittext问题，焦点问题，嵌套layout触摸事件问题。


## edittext设置输入类型没有生效的问题
editText.setInputType(InputType.TYPE_CLASS_NUMBER|InputType.TYPE_NUMBER_FLAG_DECIMAL);
注意TYPE_CLASS_XX这个一定要有

## Android sqlite的官方示例

## Android LiveData ViewModel

## 新出的框架到底好在哪里？

## apk包生成过程，签名过程。做一个相关过程分析工具。

## keystore详解、证书、公钥私钥

## tartget api和 miniapi的作用
如果实际机型api低于target的话，运行库情况，比如httplicent的冲突情况

## fastjson源码研究

## 各种语言类型转换的精度问题，Java double string相关转换，bigdecimal以及numberformat等。

## requestFeature() must be called before adding content
直接new Dialog，先show再调用setcontentview，区别于使用builder的setview。

## 记录一次webview问题排查
1. 问题描述

pos机呈现开票二维码，应用内使用webview加载的页面，按钮点击没有响应，但是通过手机扫描二维码打开能正常点击。
观察应用日志

    logcat:I/chromium:"Uncaught TypeError undifined is not a function",source:http:.xxxx.js(96)。

2. 分析过程

webview首先加载网页，然后会对相关的资源进行加载，并根据需要执行一些操作(这些操作可能定义在相关的js文件中)，找到对应的js文件定位到指定行数，是一个函数调用，根据提示应该认为找不到这个函数，但是手机浏览器和支付宝扫描都是正常的，进一步分析，这个函数定义在另一个js文件中。在另一个js文件中定位到这个函数，发现是一个函数变量，这个函数变量的赋值是有条件的，如果允许存储才进行赋值。也就是说之所以报错，是因为这个变量没有具体数值，所以提示不是一个函数。转过头来看，我们的webview没有设置dom存储相关，加上如下设置，即可。

    mWeb.getSettings().setDomStorageEnabled(true);//系统默认是false

修改后发现了新问题，pos机上页面打开没有问题，提交响应也有了，但是提交后，跳转到第二个页面，部分信息没有展示，按钮点击依然有问题。log提示如下：

    logcat:I/chromium:"Uncaught TypeError unexpected token",
    source:http:.xxxx.js(712)。

但是在手机上安装跳转和提交都没问题(前面存储的问题已经解决)。

根据之前的经验，肯定是js在某处解析又错了，找到对应的地方，是一个多行字符串的使用，报错提示不识别字符(反引号)，查阅后发现这是ES6的语法，很可能是webview内核不支持ES6,通过`chrome://inspect`查看到设备weview使用的是39.000版本的chrome内核，在`https://caniuse.com/#search=ES6`查阅，对ES6 Template Literals (Template Strings)的支持是在chrome40.0之后才可以。这也就解释了手机上运行app没有问题的原因，手机系统webview版本比较新，支持这个语法，但pos机上webview版本比较旧。

这个webview版本和Android系统版本没有强制的对应，Android5之后webview成为一个独立的app，可以通过google play进行升级(心疼..1s中)，他的版本对应chrome内核版本。Android4.3之后webview内核切换到chromium，之前是webkit。

解决办法：

1. 修改js,扩展兼容性，手动修改或者工具转换；
2. pos机具webview应用更新；
3. 放弃原生webview,使用其他方案。
```

    //查看系统版本号
    shell@CB03:/ $ getprop ro.build.version.release
    5.1.1
    //查看sdk api
    shell@CB03:/ $ getprop ro.build.version.sdk
    22
```
## webview的替换方案
1. CrossWalk-安装包体积会增大很多，相当于加了一个浏览器内核。

2. TBS(腾讯浏览器内核)，附加资源不大300k左右，但是使用的时候会下载内核资源(数十M),如果本机有这个内核则不再下载(腾讯系应用使用时可能已经下载过了)，如果本机没有资源也不联网下载，则使用内置webview。tbs速度比webview要快。

3. tab chrome.依赖于chrome服务，速度上custome tab > open in chrome app > webview。