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

查看安装包里的证书信息，解压文件
keytool -printcert -file META-INF/CERT.RSA

生成密钥
keytool -genkey 。。。。。。




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
oncreate中setcontentview之前执行设置requestWindowFeature(Window.FEATURE_NO_TITLE);
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

源码分析后绘制uml图

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
2. 

