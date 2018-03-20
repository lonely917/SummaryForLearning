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