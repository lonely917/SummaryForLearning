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

#Android 跨进程调用途径和原理
intent、广播、aidl。
intent 的action和uri跳转。
网页调用app。


#自定义控件
组合，继承，绘制

#activity启动模式

#adb shell 启动关闭应用
adb install XX.apk
adb install -r XX.apk
adb uninstall com.spdb.apiservice.test
unstall -k 包名

adb shell am force-stop
adb shell am start -n 包名/启动类名称

#退出adb shell
如果ctrl+c不能退出adb shell则使用exit命令。

#action 和 category

android.intent.action.MAIN
android.intent.category.LAUNCHER
android.intent.category.HOME

taskAffinity和process属性

adb shell dumpsys activity查看当前任务栈情况

各种启动模式
https://blog.csdn.net/ljz2009y/article/details/26621815

<action android:name="android.intent.action.BOOT_COMPLETED"/>

#Context & Activity

#打印驱动的几种模式
1. 有deviceServiceApi服务层，利用aidl调用，aidl相关类以及上层方法可以打成jar包，一般不涉及so文件，服务层apk中会用到so库，底层硬件调用。
2. 一个jar包封装接口和底层操作，so库提供支持，所以一般一个jar包一个so库。
上述结论待核实

#settext null的处理
EditText 的 settext以及Toast的toast都会到TextView的setText(CharSequence text, BufferType type,boolean notifyBefore, int oldlen)方法，会对传入数值判断，null则变为""。

#gradle相关目录
1. 下载第三方依赖库
.gradle\caches\modules-2\files-2.1

2. gradle不同版本的目录
.gradle\wrapper\dists

#打包aar以及依赖本地aar
allprojects {
    repositories {
        jcenter()
        maven { url "https://jitpack.io" }
        flatDir{dirs 'libs'}
    }
}

compile (name:'printerutils-debug',ext:'aar')

#引入多个jar包带来的jar包冲突，或引入aar和项目其他jar包冲突，aar的生成是否包含其依赖包也是一种考量。
https://blog.csdn.net/jiujiedexiaoming/article/details/76520376

#构建app最小系统

#FragmentActivity

# so库和abi架构
https://www.cnblogs.com/Bugtags2015/p/5578541.html

#Context探究
```
Activity-ContextThemeWrapper-ContextWrapper-Context
                                    |            |__abstract getApplicationContext()
                                    |__Context mBase 
                                    |__getBaseContext()
                                    |__getApplicationContext()
                                    |__attachBaseContext(Context base)
```
attachBaseContext的引用，这是一个关键操作，涉及到真实getapplicationcontext的具体实现。
Activity、Application、Service都用到这个操作。
ContextImpl和ActivityThread两个类不在android sdk系统中，未提示这里的引用，但这两个文件很关键。
context的实际实现是ContextImpl。

```
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

## looper和handler

Looper

Handler

Message

MessageQueue

ThreadLocal
-使用一个全局的ThreadLocal变量，其中get和set会获取当前线程thread,然后对thread的ThreadLocalMap进行add操作，key为全局的ThreadLocal变量。

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

``注意api26和23已经有了差异，部分Native和Proxy过失被移除，简化了设计``

## Activity的startActivity和 getApplicationContext.StartActivity()区别?
最后都是通过Instrumentation 的execStartActivity，有什么区别，对于后者进行分析，涉及到baseContext的赋值，在app启动阶段。

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

## Android binder机制
从c++到java
https://blog.csdn.net/luoshengyang/article/details/6618363
System Server进程启动众多服务进程并addservice，这些服务进程会在ServiceManager中进行注册。
许多java层类方法同过jni调用转到c++层对应的类中进行相关操作。
通过ServiceManager的getService我们可以获取服务的binderProxy，然后通过binder驱动和远程的服务进行跨进程通信。
jni是可以实现双向操作的，java访问navtive,navtive访问java。

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

## binder机制原理
java层
https://blog.csdn.net/luoshengyang/article/details/6642463
c++层若干篇
https://blog.csdn.net/luoshengyang/article/details/6618363

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