<!-- TOC -->

- [Context源码分析](#context源码分析)
    - [继承关系](#继承关系)
    - [mBase的赋值以及ContextImpl的生成](#mbase的赋值以及contextimpl的生成)
    - [attachBaseContext的usuages](#attachbasecontext的usuages)
    - [关于Context的几点说明](#关于context的几点说明)
    - [延伸问题](#延伸问题)
    - [一些资料](#一些资料)
- [**Android Binder](#android-binder)
    - [一些知识点](#一些知识点)
    - [学习资料](#学习资料)
- [**binder机制原理](#binder机制原理)
- [**ServiceManager、AMS、XXMS、Binder相关资料](#servicemanageramsxxmsbinder相关资料)
- [系统启动 概述](#系统启动-概述)
- [系统启动 init进程分析](#系统启动-init进程分析)
    - [进程描述](#进程描述)
    - [启动流程](#启动流程)
    - [系统分析](#系统分析)
- [系统启动 Zygote进程分析(trace dump监测应用变化?)](#系统启动-zygote进程分析trace-dump监测应用变化)
    - [进程描述](#进程描述-1)
    - [启动流程](#启动流程-1)
    - [onZygoteInit源码](#onzygoteinit源码)
- [系统启动 ServiceManager进程分析](#系统启动-servicemanager进程分析)
    - [进程描述](#进程描述-2)
    - [源码分析](#源码分析)
    - [一份服务相关的表格](#一份服务相关的表格)
    - [dumpsys工具](#dumpsys工具)
- [系统启动 SystemServer进程分析](#系统启动-systemserver进程分析)
    - [进程描述](#进程描述-3)
    - [源码分析](#源码分析-1)
    - [系统分析](#系统分析-1)
- [系统启动 AMS服务](#系统启动-ams服务)
    - [启动流程](#启动流程-2)
    - [源码分析](#源码分析-2)
    - [AMS.systemReady](#amssystemready)
    - [AMS中的handler：](#ams中的handler)
    - [AMS和WMS涉及的一些类:](#ams和wms涉及的一些类)
    - [AMS超时处理](#ams超时处理)
- [Launcher启动](#launcher启动)
    - [调用链](#调用链)
    - [扩展](#扩展)
    - [Launcher 加载](#launcher-加载)
    - [Launcher启动其他app](#launcher启动其他app)
- [Android中进程的创建/含Activity启动](#android中进程的创建含activity启动)
    - [新进程产生的场景](#新进程产生的场景)
    - [场景3分析(AMS startActivity)](#场景3分析ams-startactivity)
- [系统启动过程中binder知识点](#系统启动过程中binder知识点)
- [Binder-AIDL 源码版本演进](#binder-aidl-源码版本演进)
    - [关键类分析](#关键类分析)
    - [7.1 vs 8.2](#71-vs-82)
    - [总结](#总结)
    - [扩展](#扩展-1)
- [APP启动流程](#app启动流程)
    - [场景](#场景)
    - [扩展](#扩展-2)
- [系统UI服务](#系统ui服务)
    - [启动调用链](#启动调用链)
    - [startSystemUi](#startsystemui)
    - [SystemUIService](#systemuiservice)
- [系统启动 IMS服务(InputManagerService)](#系统启动-ims服务inputmanagerservice)
    - [启动流程](#启动流程-3)
    - [InputManagerService实例化过程](#inputmanagerservice实例化过程)
    - [IMS.start过程](#imsstart过程)
    - [Input事件来源](#input事件来源)
    - [ims相关核心线程](#ims相关核心线程)
    - [android 系统输入事件流程总结(取自深入理解Android卷3)](#android-系统输入事件流程总结取自深入理解android卷3)
    - [查看输入设备以及输入事件](#查看输入设备以及输入事件)
- [系统启动 WMS服务(WindowManagerService)](#系统启动-wms服务windowmanagerservice)
- [Window建立](#window建立)
- [ams wms system_server一些知识点](#ams-wms-system_server一些知识点)
- [activity、window、viewrootimpl、windowmanager、windowmanagreImpl、windoWmanagerGlobal](#activitywindowviewrootimplwindowmanagerwindowmanagreimplwindowmanagerglobal)
- [ViewRootImpl](#viewrootimpl)
- [requestLayout 和 invalidate](#requestlayout-和-invalidate)
- [控件绘制 View/ViewGroup Measure、Layout、Draw](#控件绘制-viewviewgroup-measurelayoutdraw)
    - [onMeasure 流程分析](#onmeasure-流程分析)
    - [onLayout 流程分析](#onlayout-流程分析)
    - [draw 流程分析](#draw-流程分析)
- [APP安装](#app安装)
- [framework层源码调试跟踪执行过程的实现?](#framework层源码调试跟踪执行过程的实现)
- [图形绘制相关服务](#图形绘制相关服务)
- [SurfaceView 和 Canvas](#surfaceview-和-canvas)
- [context获取各种服务](#context获取各种服务)
- [Activity](#activity)
    - [Activity的启动](#activity的启动)
    - [Activity 成员分析](#activity-成员分析)
- [Service](#service)
- [ContentProvider](#contentprovider)
- [BrocastReceiver](#brocastreceiver)
- [Intent](#intent)
- [PackageInfo & LoadedApk & Context中的base以及ContextImpl中的](#packageinfo--loadedapk--context中的base以及contextimpl中的)
- [getWidth getMeasuredWidth getLayoutParams.witdth 比较](#getwidth-getmeasuredwidth-getlayoutparamswitdth-比较)
- [Window Dialog PopupWindow Toast分析](#window-dialog-popupwindow-toast分析)
- [从Activity中WindowManager谈起](#从activity中windowmanager谈起)
- [Toast调用流程(跨进程、多次binder交互)](#toast调用流程跨进程多次binder交互)
- [资源加载过程](#资源加载过程)
- [Android 性能优化](#android-性能优化)

<!-- /TOC -->


## Context源码分析

### 继承关系
```
Activity-ContextThemeWrapper-ContextWrapper-Context
                                    |            |__abstract getApplicationContext()
                                    |__Context mBase 
                                    |__getBaseContext()
                                    |__getApplicationContext()
                                    |__attachBaseContext(Context base)
```
`Activity Service Application 与 ContextWrapper Context ContextImpl这样层次设计的目的?代理，包装，设计缘由?`

### mBase的赋值以及ContextImpl的生成

attachBaseContext的使用，这是一个关键操作，涉及到真实getApplicationContext的具体实现。Activity,Service,Application实例创建后，最终都会通过attachBaseContext关联一个contextImpl对象。可以从ActivityThread出发进行分析。

Activity

ContextImpl.createActivityContext 创建ContextImpl的实例对象，通过activity的attach方法最终调用attachBaseContext，将activity关联上contextImpl.

    handleLaunchActivity 
        performLaunchActivity
            ContextImpl.createActivityContext
            activity.attach
                activity.attachBaseContext
            activity.onCreate

Service

    handleCreateService ->
        ContextImpl.createAppContext
        service.attach
        service.onCreate

Application

    handleBindApplication -> 
    loadedapk.makeApplication -> 
    ContextImpl.createAppContext创建ContextImpl的实例对象->
    mActivityThread.mInstrumentation.newApplication(cl, appClass, appContext)创建Application对象->
    newApplication方法中通过app.attach 进行application和ContextImpl的关联->
    instrumentation.callApplicationOnCreate(app);最终调用application的onCreate.

### attachBaseContext的usuages

Activity、Application、Service都用到这个操作。
ContextImpl和ActivityThread两个类不在android sdk系统中，未提示这里的引用，但这两个文件很关键。
`context的实际实现是ContextImpl`。

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

### 关于Context的几点说明

context的使用，可能是一层一层的包装。注意几点：
1. Activity、Service、Application何时初始化context的, 和2、3、4关联
2. ActivityThread的各种handle和perform，比如handleBindApplication，performLaunchActivity.
3. LoadedApk的makeApplication
4. ContextImpl的createSystemContext、createSystemUiContext、createAppContext、createActivityContext，被调用的创建ContextImpl的方法。
5. Activity的getApplication 和 getApplicationContext最终返回应该是一样的，都是唯一的Application的对象。(?`Application对象只能有一个吗？`)

### 延伸问题

1. 关注ContextImpl和ActivityThread的流程，探索`apk安装以及启动全过程`。
2. app启动-注意launcher process - system process - app process结合思考。
3. context.getSystemService源码分析；
4. LayoutInflater提供了cloneInContext()函数的用处；

### 一些资料

[context简单解析]

https://blog.csdn.net/singwhatiwanna/article/details/21829971
https://blog.csdn.net/chunqiuwei/article/details/50068141
https://my.oschina.net/youranhongcha/blog/1807189

[Binder与AMS流程。AMS负责四大组件生命周期。]

https://blog.csdn.net/bfboys/article/details/52564531
https://www.jianshu.com/p/e69d22ec0582


## **Android Binder

### 一些知识点
1. System Server进程启动众多服务并addservice，这些服务会在ServiceManager中进行注册。
2. 许多java层类方法同过jni调用转到c++层对应的类中进行相关操作。
3. 通过ServiceManager的getService我们可以获取服务的binderProxy，然后通过binder驱动和远程的服务进行跨进程通信。
4. jni是可以实现双向操作的，java访问navtive,navtive访问java。

### 学习资料
从c++到java、罗升阳博客

java层</br>
https://blog.csdn.net/luoshengyang/article/details/6642463(内含一个binder学习路线，若干篇老罗出品，依次学习)

c++层若干篇</br>
https://blog.csdn.net/luoshengyang/article/details/6618363


## **binder机制原理

## **ServiceManager、AMS、XXMS、Binder相关资料

这是一些很好的文章，综合起来看，多看几遍：

binder系列文章开篇,内有一个提纲，从系统启动到服务注册，从c++到java。<br/>
http://gityuan.com/2015/10/31/binder-prepare/

侯亮系列，binder开篇，binder以及servicemanager<br/>
https://my.oschina.net/youranhongcha/blog/149575

renyugang,这篇文章介绍了另外两篇，比较有深度的两篇。<br/>
https://blog.csdn.net/singwhatiwanna/article/details/19756201<br/>
 http://www.cnblogs.com/innost/archive/2011/01/09/1931456.html(邓平凡?，阿拉神农)<br/>
 http://blog.csdn.net/universus/article/details/6211589

深度相对浅一点的一篇(博客可以看出作者有系统地构建自己的知识体系)<br/>
http://liuwangshu.cn/framework/component/1-activity-start-2.html


## 系统启动 概述
长摁电源键后，系统启动(这里不涉及上电后引导区加载等过程，直接从系统的第一个进程开始分析)

1. init进程启动，用户空间的第一个进程。具体文件system\core\init\init.c,main函数启动后会解析init.rc脚本文件。
2. 解析init.rc脚本，init_parse_config_file("/init.rc")生成service_list和action_list。
3. 构建action_queue，可以理解为init进程要执行的一些动作。
4. 逐步执行各种行为，可以理解为开启各种进程。(首先是core类型服务，然后启动main类型服务)
5. 无限循环，等待其他进程发来的信号，并进行响应。
6. 服务对应的进程挂掉后会给init进程发送信号，init进程的对应的响应(回调方法)是尝试restart服务。

```
4中涉及的core服务代表举例:
    console(/system/bin/sh),
    servicemanager(/system/bin/servicemanager) ;
4中涉及的main服务代表举例:
    zygote(/system/bin/app_process),
    media(/system/bin/mediaserver),
    netd(/system/bin/netd);
```
7. ServiceManager服务进程启动，启动后最终进入loop状态(binder_loop),涉及内核交互,可以理解为从任务队列里取任务并执行，没有任务会进入阻塞状态等待唤醒。

8. Zygote进程启动，这是Android应用层进程的母体，其中会调用start_system_server开启system_server进程，而后进程进入loop状态，通过socket监听信号，执行动作。
(zygote进程创建时会初始化binder相关结构，开启binder监听线程等工作)

9. SystemServer进程启动，会加载各种服务，比如AMS实例化,并将AMS注册到SM中，开启AM线程，启动webview进程(`待确定`)，最后开会开启桌面launcher。这里涉及很多服务的注册，ams、wms、ims、pms等等。

10. 开启桌面会启动新的Activity,首先会创建对应的进程，启动ActivityThread，然后scheduleLaunchActivity开启Activity。
(开启新进程时通过socket和Zygote通信然后创建的新进程,新进程加载的ActivityThread的main方法，启动Activity是利用跨进程binder通信来实现的，进程内部调用scheduleLaunchActivity使用handler发消息最终handleLaunchActivity方法被调用，Activity的attach方法触发，最终onCreate被调用)


## 系统启动 init进程分析

### 进程描述
Android系统底层基于Linux Kernel，内核启动过程会建立用户空间的第一个进程即init进程，pid为1。文件位置 /system/core/init/Init.cpp，main方法开启。

### 启动流程
1. 一些初始化设置(如文件属性、内核log、属性服务，初始化epoll功能、初始化子进程退出处理函数、启动属性服务器);
2. 解析"init.rc"文件,init_parse_config_file("/init.rc");
3. 构建action_queue，可以理解为init进程要执行的一些动作。这里利用action_for_each_trigger、queue_builtin_action多次处理，生成合适的执行顺序。
4. while循环，执行队列中的指令来开启服务、检测是否需要重启某些服务、监听子进程的退出信号等。

    4.1 execute_one_command 取指令执行服务对应的方法<br/>
    4.2 service_start 遍历2中得到的服务列表，根据需要进行重启服务<br/>

```
其中4中会按照一定顺序执行开启各种服务进程，有core类型、main类型，比如servicemanager属于前者，zygote属于后者。

4中涉及的core服务代表举例:
    console(/system/bin/sh),
    servicemanager(/system/bin/servicemanager) ;
4中涉及的main服务代表举例:
    zygote(/system/bin/app_process),
    media(/system/bin/mediaserver),
    netd(/system/bin/netd);
```

### 系统分析

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


1. init为pid为1,ppid为0;
2. servicemanager父进程为init(ppid=1)
3. zygote父进程为init(ppid=1);
4. servicemanager进程先于zygote进程创建(pid更小)

系统进程system_server由zygote孵化
普通应用进程com.aisino.xfb.pay由zygote孵化(是通过socket给zygote发送信号创建的进程)

## 系统启动 Zygote进程分析(trace dump监测应用变化?)

###进程描述
zygote服务为init.rc脚本文件中描述的服务之一，init进程通过service_start启动的服务，顾名思义，zygote主要功能就是孵化新的进程。zygote对应文件路径(frameworks\base\cmds\app_process\App_main.cpp)。

### 启动流程

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

### onZygoteInit源码

`这里有binder线程池开启过程，所有zygote产生的新进程都会在这个环节初始化binder线程池`

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

### 进程描述
1. init进程开启的诸多子进程之一
2. 顾名思义，提供服务管理功能，类似一个服务注册表
3. 本身也是一个服务，对应一个固定地址的binder(0号binder)。

### 源码分析
源码：frameworks\base\cmds\servicemanager\service_manager.c

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

    其中BINDER_SERVICE_MANAGER固定0;
    binderloop开启循环;
    binderParse进行命令解析;
    svcmgr_handler执行对应动作(包括服务的注册和检索);

### 一份服务相关的表格

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

### dumpsys工具

通过dumpsys 服务名 查看运行详情：

    dumpsys activity
    dumpsys cpuinfo
    dumpsys window


## 系统启动 SystemServer进程分析

### 进程描述
1. zygote启动后首先孵化出SystemServer进程
2. 新进程中最终执行SystemServer的入口函数main

### 源码分析

下面分析systemserver.main关键流程:

main函数中直接执行new SystemServer().run()，关键代码如下：

```java
Looper.prepareMainLooper();//消息循环初始化,对应最后开启循环
System.loadLibrary("android_servers");// Initialize native services.
createSystemContext();// Initialize the system context.
startBootstrapServices();//各种相关服务    ams的setSystemProcess会被调用，向sm注册各种binder服务
startCoreServices();//各种相关服务
startOtherServices();//各种相关服务 ims wms dms会注册 ams的systemReady中会启动桌面应用
Looper.loop();//消息循环
```

```
createSystemContext();
    [
        利用ActivityThread.systemMain()生成AT对象
        进一步对mSystemContext以及mSystemUIContext设置主题相关
    ]


startBootstrapServices();
    [
        //注意下述startservice都是SystemServiceManager的对象的方法，不同于ServiceManger(我们使用ServiceManger的addservice和findservice来进行aidl相关的服务注册和查询)

        //开启installer服务，这个是SystemService子类，非AIDL调用的那种服务，比如SMS、AMS、PMS等。
        Installer installer = mSystemServiceManager.startService(Installer.class);
        ...
        //开启ActivityManagerService.Lifecycle服务，也是SystemService子类
        mActivityManagerService = mSystemServiceManager.startService(
                ActivityManagerService.Lifecycle.class).getService(); 
        ...
        PackageManagerService启动//aidl服务
        ...
        
        //设置ams，其中会调用ServiceManager进行注册各种binder,包括AMS自身。
        mActivityManagerService.setSystemProcess(); 
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
```
### 系统分析

adb shell进入后，通过ps -T pid可以查看某一进程下的线程。

system_server进程下有很多线程，下面列出一些：

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

## 系统启动 AMS服务

### 启动流程
```
SystemServer.main()
    SystemServer.run()
        startBootstrapServices()
            mSystemServiceManager.startService(ActivityManagerService.Lifecycle.class).getService();
            mActivityManagerService.setSystemProcess
                ServiceManager.add(name, this);//向SM注册AMS
```

### 源码分析

AMS初始化关键代码

```java 

//获取AMS对象，实例化过程也会开启ActivityManagerService线程，一个ServiceThread.
mActivityManagerService = mSystemServiceManager.startService(
                ActivityManagerService.Lifecycle.class).getService();
```

AMS实例化细节

mSystemServiceManager.startService((ActivityManagerService.Lifecycle.class))->
1. 创建ActivityManagerService.Lifecycle、LifeCycle实例化过程会生成ams
2. 调用lifecycle的onStart方法   

```
lifecycle.onstart
    ams.start
        mProcessCpuThread.start();//启动cpuTracker线程
        mBatteryStatsService.publish(mContext);//注册atteryStatsService
            ServiceManager.addService(BatteryStats.SERVICE_NAME, asBinder());
        mAppOpsService.publish(mContext);//注册AppOpsService服务
            ServiceManager.addService(Context.APP_OPS_SERVICE, asBinder());
```

AMS内部类LifeCycle.class
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

AMS构造函数

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

AMS的start方法

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

AMS注册调用链：

startBootstrapServices方法最后会对生成的ams进行注册操作，注册到sm。

    systemserver.startBootstrapServices
        ams.setSystemProcess
            sm.addService

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

### AMS.systemReady

1. 传入一个runnable动作，根据条件执行runnable行为；
   其中runnable会调用startSystemUi开启系统UI服务；
   
2. 开启桌面： startHomeActivityLocked(currentUserId, "systemReady");

### AMS中的handler：

    AMS.mUiHandler - UiHandler - android.ui线程
    AMS.mBgHandler - Handler - android.bg
    AMS.mHandler - MainHandler - 'ActivityManager'

### AMS和WMS涉及的一些类:

    ams:ActivityRecord;TaskRecord;ActivityStack;
    wms:AppWindowToken;Task;TaskStack;

### AMS超时处理

`所有跟超时相关的工作都运行在ActivityManager线程，唯独input的超时处理过程并非发生在ActivityManager线程，而是inputDispatcher线程发生的。`

`对于ANR/Crash/Error等几乎所有错误、警告相关的对话框都运行在android.ui线程。`


## Launcher启动

### 调用链
桌面开启的完整调用链:

    ss.main->
    ss.run->
    ss.startOtherService->
    ams.systemReady->
    ams.startHomeActivityLocked->
    ActivityStarter.startHomeActivityLocked->
    as.startActivityLocked 

### 扩展

1. `具体如何开启launcher的启动activity?`
2. intent的显示调用和隐式调用实现原理，显示可以指定对应activity，隐式需要解析出对应的activity列表。
3. `boot信号获取在哪里?源码分析`

### Launcher 加载

开启的launcher应用源码位于:

    /packages/apps/Launcher3/src/com/android/launcher3/Launcher.java

继承自BaseDraggingActivity

```java
public class Launcher extends BaseDraggingActivity implements LauncherExterns,
    LauncherModel.Callbacks, LauncherProviderChangeListener, UserEventDelegate

```
onCreate进行界面初始化
    
```java
    mLauncherView = LayoutInflater.from(this).inflate(R.layout.launcher, null);
    setupViews();
```

### Launcher启动其他app

点击桌面图标，触发的是launcher的startActivitySafely，最终还是activity的startActivity方法，然后进行后续调用链，包括与ams交互、新进程创建、根activity的创建。

```java

public boolean startActivitySafely(View v, Intent intent, ItemInfo item) {
    boolean success = super.startActivitySafely(v, intent, item);  //父类的方法
    if (success && v instanceof BubbleTextView) {
        // This is set to the view that launched the activity that navigated the user away
        // from launcher. Since there is no callback for when the activity has finished
        // launching, enable the press state and keep this reference to reset the press
        // state when we return to launcher.
        BubbleTextView btv = (BubbleTextView) v;
        btv.setStayPressed(true);
        setOnResumeCallback(btv);
    }
    return success;
}
```

后续调用链可以参考`Android中进程创建以及Activity启动`
下面进行一个简单的概述
Activity.startActivity
    Instrumentation.exec
         ActivityManagerNative.getDefault().startActivity //8.0之前的版本

随后binder调用，AMS中的startActivity执行

    ams.startActivity
        ams.startActivityAsUser
            mActivityStarter.startActivityMayWait
                as.startActivityLocked //ActivityStarter
                    as.startActivityUnchecked
                        ass.resumeFocusedStackTopActivityLocked//ActivityStackSupervisor
                            targetStack.resumeTopActivityUncheckedLocked
                                astack.resumeTopActivityInnerLocked//ActivityStack
                                    mStackSupervisor.startSpecificActivityLocked
                                        ass.realStartActivityLocked //进程已经存在
                                            app.thread.scheduleLaunchActivity //进入目标app进程
                                        
                                        mService.startProcessLocked //进程不存在先创建进程

概括即：ams的startActivity经过层层调用最后到ActivityStackSupervisor的startSpecificActivityLocked，然后判断目标process是否已经存在，如果存在则执行ass的realStartActivityLocked，最终通过app.thread这个目标进程binder服务的客户端发起scheduleLaunchActivity的请求；如果process不存在则会进行进程创建，创建完成后再发起Activity的启动请求。

## Android中进程的创建/含Activity启动

### 新进程产生的场景

1. 系统启动，init进程开启后，根据init.rc文件启动一系列子进程。
2. zygote进程启动后，开启system_server进程，并执行runSelectLoop进入socket监听状态。
3. startActivity或者startService等开启组件，如果需要开启新进程，会通过ActivityThread和SystemServer交互，再有SystemServer向Zygote发起请求进行进程开辟。

### 场景3分析(AMS startActivity)

进程创建最终都是c++层fork方法，对场景3进行java层流程梳理：
1. App进程执行startActivity或者startService
2. aidl通信、SystemServer进程中AMS执行对应的startActivity或者startService

`Activity创建实际的调用链会根据具体场景有所不同，比如是否启动一个newTask、之前是否已有同类Activity启动等，同样不同版本sdk代码细节或者名称可能会有差异，但整体思路是一样的，下面是根据24版本源码进行追踪得到的结果，调用路径之一`
```
    [AMS - startActivity]
        startActivityAsUser->
            mActivityStarter.startActivityMayWait->
    
    [ActivityStarter - startActivityMayWait]
        startActivityLocked->
            startActivityUnchecked->
                mTargetStack.startActivityLocked(mStartActivity, newTask, mKeepCurTransition, mOptions)->

    [ActivityStack - startActivityLocked] 
        ensureActivitiesVisibleLocked->
            makeVisibleAndRestartIfNeeded->
                mStackSupervisor.startSpecificActivityLocked

    [ActivityStackSupervisor - startSpecificActivityLocked] 
        [分支：进程创建或者直接启动Activity]
            mService.startProcessLocked//进程创建
            realStartActivityLocked //已有对应进程则直接启动activity

    如果目标进程已经存在，直接进行activity启动；否则创建进程，进程创建完毕后会继续执行没有启动的这个activity。
    
    [分支1：AMS - startProcessLocked]
    
    [分支2：ActivityStackSupervisor - realStartActivityLocked]

```

上述realStartActivityLocked后续步骤，分system_server和新app进程两方面来看：

    1. system_server进程
        
    [ActivityStackSupervisor] 
        realStartActivityLocked->
            app.thread.scheduleLaunchActivity->

    `aidl调用进入到ActivityThread的scheduleLaunchActivity`

    旧版本有如下aidl调用链：
    从ApplicationThreadProxy发起(24中还含有ApplicationThreadProxy这个文件)
    ApplicationThreadProxy在ApplicationThreadNative.java中
    ApplicationThread是ApplicationThreadNative的一个子类，位于ActivityThread.java中
    ATP发起调用通过mRemote这个binder接口进行操作
    App进程中的ApplicationThread的onTransact会被调用(这个方法是父类ATN的)，onTransact中会根据参数调用指定的方法，于是就从system_server中ATP的操作映射到app进程中ApplicationThread的操作。

    [ApplicationThreadProxy].
        scheduleLaunchActivity->
            mRemote.transact->
    

            

    2. app进程
    
    [ApplicationThread extends ApplicationThreadNative]
        onTransact
            scheduleLaunchActivity
                sendMessage(H.LAUNCH_ACTIVITY, r)//从binder线程发送消息给app主线程

    [ActivityThread] 
        handlerMessage
            handleLaunchActivity

上述startProcessLocked 进程创建后续步骤:

    1. system_server进程：
        [AMS]   
            startProcessLocked->
                Process.start->
                
        [Process - start]
             startViaZygote->
                zygoteSendArgsAndGetResult->
                    通过zygoteState.writer写数据，和zygote进程进行socket通信

    2. zygote进程
    [ZygoteInit] 
        runSelectLoop\\循环等待消息
            peers.get(i).runOnce()\\收到消息执行动作
            
    [ZygoteConnection - runOnce] 
        pid = Zygote.forkAndSpecialize
        pid==0?handleChildProc:handleParentProc
    
    [Zygote - forkAndSpecialize]
        VM_HOOKS.preFork()
        pid = nativeForkAndSpecialize //native方法创建进程
        VM_HOOKS.postForkCommon()

    接上文runOnce方法中pid=0时执行handleChildProc
    
    [ZygoteConnection - handleChildProc] 
        RuntimeInit.zygoteInit

    [RuntimeInit - zygoteInit]
        commonInit
        nativeZygoteInit
            [AndroidRuntime.cpp]gCurRuntime->onZygoteInit() //这里会开启binder线程
        applicationInit
            invokeStaticMain //抛出异常，指定方法名称main,异常捕获的时候会执行对应类的main方法

    生成的子进程进行一些初始化操作后，通过invokeStaticMain抛出异常，使得子进程跳出runSelectLoop循环，被外部catch捕获，捕获后执行对应类ActivityThread的的main方法

    [ZygoteInit - catch MethodAndArgsCaller] 
        caller.run
        
    [MethodAndArgsCaller - run]
        mMethod.invoke(null, new Object[] { mArgs });//执行ActivityThread main方法
        
    3.目标app进程(子进程)：
    [APP2 ActivityThread]
    [ActivityThread] 
        main->
            attach(false)->
                ams.attachApplication->......
                        .....
                    ass.realStartActivityLocked->后续同前文`realStartActivityLocked后续步骤`

## 系统启动过程中binder知识点

1. IPC的一些方式：共享内存、管道、socket。
2. binder用于进程间通信，设计思想：客户端binder client,内存映射到内核区域，服务端binder server，和内核进行共享内存，因此双方同步只需要进行一次拷贝操作。
3. 用户空间首个进程init进程启动时会进行binder设备初始化操作。
4. 特殊的binder、固定0号binder为ServiceManager，SM进程启动时初始化。
5. 其他的binder都会通过sm.addService进行服务注册，通过sm.getService进行服务检索。
6. 0号binder是固定的，因此可以直接找到。其他的通过名字来进行检索。
7. 一个进程中可以有多个binder服务。
8. app进程都是由zygote分裂而来，进程初始化后通过onZygoteInit会开启binder线程池，用于binder通信。
9. 可以理解认为不同的进程中都有很多binder线程(binder线程池)，通过和底层通信来监听binder相关的信号，以进行binder通信。

`一个简化的模型来进行理解binder通信`：
client想要执行server的A操作，于是通过server的binderproxy写入操作名、参数等，并根据需要接收服务端返回的数据；
server的binder线程根据获取的操作、参数进行对应的行为，使用传来的参数并执行A，根据需要返回数据等；

`android系统中AMS、WMS、PMS服务的获取和使用`：
首先获取SM的binder proxy，然后通过binder通信获取某一服务的binder proxy,然后通过binder通信调用服务的一些方法。



## Binder-AIDL 源码版本演进

`AIDL` : Android Interface Definition Language


### 关键类分析

    ApplicationThreadProxy(ATN的内部类,位置ApplicationThreadNative.java)
    ActivityManagerProxy(AMN的内部类，位置ActivityManagerNative.java)
    IApplicationThread接口(IApplicationThread.java)
    IActivityManager接口(IActivityManager.java)

### 7.1 vs 8.2 
7.1源码framework目录还有代理类实现。
8.2源码中ATN和AMN被标记为过时的，并且移除了ATP和AMP；同时移除了IApplicationThread.java和IActivityManager.java两个定义接口的文件，但是添加了IActivityMananger.aidl和IApplicationThread.aidl两个文件用于辅助生成aidl相关类。

### 总结

Binder调用本质没有改变，之前是手动写java层native和proxy代理类，后来使用aidl文件来统一生成相关的中间类。
中间类xxx.stub是服务端, xxx是客户端。同一个进程在多次binder交互中可能既是某次调用的发起端，也是另一次调用的服务端。
以App1调用ams启动App2的某个Activity为例。这个过程中会有如下交互：App1 利用binder调用使用system_server中的ams服务(ActivityManangerProxy->ActivityManagerNative);system_server利用binder调用使用app2中的PerformLaunchActivity方法(ApplicationThreadProxy->ApplicationThreadNative);system_server利用binder调用触发app1的schedulePauseActivity(ApplicationThreadProxy->ApplicationThreadNative)。

### 扩展

`生成的类在哪里可以找到？IActivityMananger 以及 IActivityManager.stub的class在哪里，源码里只有对应IActivityMananger.aidl文件`

## APP启动流程

### 场景

场景1：
Laucher启动

场景2：普通应用启动

launcher -> app
过程类似`Android中进程的创建`章节中先启动新进程再开启Activity的情况。

1. 点击桌面app
2. launcher 进行本地startActivity操作，通过binder调用向system_server发起远程调用startActivity的请求，具体是ams来实现这个过程
3. system_server通过socket向Zygote进程发送请求，要求创建新进程
4. zygote进行分裂，产生新APP进程，执行ActivityThread的main方法
5. 新APP进程启动后要求attachApplication，也是binder调用转到system_server进程
6. system_server进程最终会通过binder调用要求新APP进程执行scheduleLaunchActivity操作
7. 新App进程执行scheduleLaunchActivity，通过向主线程handler发送消息触发相关操作
8. 新APP进程主线程执行handleLaunchActivity
9. 目标Activity会被创建，最终onCreate被回调

### 扩展

1. window的建立
2. ui的绘制


## 系统UI服务

### 启动调用链

    ss.main->
    ss.run->
      ss.startOtherService->
       ams.systemReady->
        runnable执行->
            startSystemUi->
                context.startServiceAsUser(intent, UserHandle.SYSTEM);
                ("com.android.systemui.SystemUIService"))

### startSystemUi

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

### SystemUIService

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

## 系统启动 IMS服务(InputManagerService)

### 启动流程

```
SystemServer -> 
    startOtherServices -> 
        inputManager = new InputManagerService(context)
        ServiceManager.addService(Context.INPUT_SERVICE, inputManager);
        inputManager.start();
```

### InputManagerService实例化过程

```java

public InputManagerService(Context context) {
    this.mContext = context;
    this.mHandler = new InputManagerHandler(DisplayThread.get().getLooper());

    mUseDevInputEventForAudioJack =
            context.getResources().getBoolean(R.bool.config_useDevInputEventForAudioJack);
    Slog.i(TAG, "Initializing input manager, mUseDevInputEventForAudioJack="
            + mUseDevInputEventForAudioJack);
    mPtr = nativeInit(this, mContext, mHandler.getLooper().getQueue());

    LocalServices.addService(InputManagerInternal.class, new LocalService());
}
    
```
    设置InputManagerService的handler关联"android.display"线程(DisplayThread);//这也说明(DisplayManagerService在startBootstrapServices时被启动)

    nativeInit创建InputDispatcher和InputReader本地对象。

### IMS.start过程

```java
    public void start() {
        Slog.i(TAG, "Starting input manager");
        nativeStart(mPtr);

        // Add ourself to the Watchdog monitors.
        Watchdog.getInstance().addMonitor(this);

        registerPointerSpeedSettingObserver();
        registerShowTouchesSettingObserver();
        registerAccessibilityLargePointerSettingObserver();

        mContext.registerReceiver(new BroadcastReceiver() {
            @Override
            public void onReceive(Context context, Intent intent) {
                updatePointerSpeedFromSettings();
                updateShowTouchesFromSettings();
                updateAccessibilityLargePointerFromSettings();
            }
        }, new IntentFilter(Intent.ACTION_USER_SWITCHED), null, mHandler);

        updatePointerSpeedFromSettings();
        updateShowTouchesFromSettings();
        updateAccessibilityLargePointerFromSettings();
    }

```

会通过native调用开启两个线程，InputReader和InputDispatcher。一个用于从文件读取事件、一个用于派发事件。

### Input事件来源
这里分三个阶段进行分析

1.原始输入事件到ViewRootImpl的过程：

屏幕触摸->硬件驱动->信号和事件；
InputReader不断获取由驱动产生的按键事件，加入队列。`事件依次经过input_event(设备节点) -> RawEvent(EventHub)->inputReader等节点，从一些列低级事件转化为android应用层输入事件`。
InputDispatcher线程从队列取事件，并进行事件传递，派发到合适的窗口。这里会从SystemServer和目标进程进行跨进程通信，InputDispatcher通过socket和远程进程通信(异步非阻塞)。
```
-publishKeyEvent->
    -mChannel->sendMessage(&msg);

        收到消息返回调用receiveFinishedSignal进行处理
```
目标APP的UI主线程(android.ui线程)looper循环，处理派发到窗口的事件,处理完毕后socket通信发送信号给InputDispatcher。 `socket通信最后如何触发主线程handler消息的?`

```
-nativePollOnce
    -Looper::pollInner
        -InputConsumer.consume
            -....
                -viewrootImp.deliverInputEvent
                -finishInputEvent
                    -sendFinishedSignal
                        -mChannel->sendMessage(&msg);
```

2.ViewRootImpl到Activity和窗口的过程(viewRootImp.deliverInputEvent)

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

3.后续就是常见的ViewGroup的事件传递(具体分析见ViewGroup事件派发专题)


资料：
`https://blog.csdn.net/singwhatiwanna/article/details/50775201` 是对2过程的一个详细介绍，其中Thread.dumpStack是一个很好的分析工具。

### ims相关核心线程

    shell@CB03:/ $ ps | grep system_server
    system    881   300   1167360 76556 ffffffff 00000000 S system_server

    shell@CB03:/ $ ps -t 881 | grep input
    system    2557  881   1167360 76556 ffffffff 00000000 S InputDispatcher
    system    2558  881   1167360 76556 ffffffff 00000000 S InputReader


### android 系统输入事件流程总结(取自深入理解Android卷3)

Android输入系统的工作原理概括来说，就是监控/dev/input/下的所有设备节点，当某个节点有数据可读时，将数据读出并进行一系列的翻译加工，然后在所有的窗口中寻找合适的事件接收者，并派发给它。

内核将原始事件写入到设备节点中，InputReader不断地通过EventHub将原始事件取出来并翻译加工成Android输入事件，然后交给InputDispatcher。InputDispatcher根据WMS提供的窗口信息将事件交给合适的窗口。窗口的ViewRootImpl对象再沿着控件树将事件派发给感兴趣的控件。控件对其收到的事件作出响应，更新自己的画面、执行特定的动作。所有这些参与者以IMS为核心，构建了Android庞大而复杂的输入体系。

当两个线程启动后，InputReader在其线程循环中不断地从EventHub中抽取原始输入事件，进行加工处理后将加工所得的事件放入InputDispatcher的派发发队列中。InputDispatcher则在其线程循环中将派发队列中的事件取出，查找合适的窗口，将事件写入到窗口的事件接收管道中。窗口事件接收线程的Looper从管道中将事件取出，交由事件处理函数进行事件响应。整个过程共有三个线程首尾相接，像三台水泵似的一层层地将事件交付给事件处理函数

`ims启动前,wms已经启动,这样wms才能接收inputdispatcher派发来的事件并寻找合适的处理对象`

其中用到了Inotify和Epool,线程相关知识 邓系列 https://blog.csdn.net/Innost/article/details/90633199


### 查看输入设备以及输入事件

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

1.模拟指定输入设备的指定基本事件 

    adb shell sendevent /dev/input/event1 xx xx xx

2.更为高层的按键事件 

    adb shell input keyevent xx(xx为按键码)
    adb shell input tap 50 250(点击) 
    adb shell input swipe 50 250 250 250 500(滑动) 

## 系统启动 WMS服务(WindowManagerService)

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



## Window建立

1. 窗口的生成
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


`WindowManager.LayoutParams中有不同的窗口类型 和上面这个有什么关系？`


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

## framework层源码调试跟踪执行过程的实现?

如何查看源码中日志输出部分(root机?)

## 图形绘制相关服务
SurfaceFingler进程

## SurfaceView 和 Canvas

## context获取各种服务

SystemServiceRegistry初始化过程
静态代码块各种registerService
`这里的registerService和SercieManager的addService比较?`

## Activity

注意对组件的认识可以从不同的角度，比如启动过程、内部组织、生命周期管理等。

### Activity的启动

1. 名义上第一个APK的启动-Launcher的启动

见章节`## launcher启动`

这里涉及到先启动进程(`## Android中进程的创建`),然后再启动对应Activity。
?wms.systemReady开始进行一些列调用，最后启动launcher. 这里应该是隐式调用。
?我们自定义launcher一般是通过xml设置filter标签，由于隐式调用，我们就有机会选择自定义的launcher
?boot完成广播信号的发起是何时，开机自启动的关键。

2. 桌面图标点击后启动应用程序

进程角度看调用流程：
 launcher app -> system_server -> zygote -> app1 

3. 应用程序内部startActivity/startActivityForResult进行显式或者隐式调用

startActivity调用流程

- 发起端
[Activity]startActivity
    [Activity]startActivityForResult
        [Activity]mParent.startActivityFromChild
            [Activity]mInstrumentation.execStartActivity
                [Instrumentation] ActivityManager.getService().startActivitystartActivity
                    [IActivityManager] startActivity //这里binder调用到system_server进程中ams服务，ams对应的的binder线程进行处理

高版本使用IAcvitityManager.aidl机制替换了之前完全手写实现的方式


- system_server中ams服务提供(binder线程池中某一线程提供client调用的服务-startActivity)
具体可以参考章节`## Android中进程的创建`
概述：
ams.startActivity
    mActivityStarter.startActivityMayWait
        mTargetStack.startActivityLocked
            mStackSupervisor.startSpecificActivityLocked 进程创建/ realStartActivityLocked 已有进程直接启动activity



- system_server主线程 handler处理launcheActivity的消息
[ActivityThread] 
handleMessage
    handleLaunchActivity
        performLaunchActivity
            mInstrumentation.newActivity
            activity.attach
            mInstrumentation.callActivityOnCreate
                activity.performCreate                   
                    onCreate // onCreate触发
            activity.performStart()
                mInstrumentation.callActivityOnStart
                    onStart // onStart触发
    handleResumeActivity
        ....
        onResume // onResume触发
        ....



### Activity 成员分析

## Service

## ContentProvider

## BrocastReceiver

## Intent

## PackageInfo & LoadedApk & Context中的base以及ContextImpl中的

## getWidth getMeasuredWidth getLayoutParams.witdth 比较

注意三者获取时机、三者的含义/差异/单位、layout布局文件中的dp px加载的时候如何进行转化的

## Window Dialog PopupWindow Toast分析

https://blog.csdn.net/yanbober/article/details/46361191

都要调用wm.addView


## 从Activity中WindowManager谈起

1. getWindowManager

```java
  public WindowManager getWindowManager() {
        return mWindowManager;
    }
```

2. getSystemService(Context.WINDOW_SERVICE)

```java
public Object getSystemService(@ServiceName @NonNull String name) {
    if (getBaseContext() == null) {
        throw new IllegalStateException(
                "System services not available to Activities before onCreate()");
    }

    if (WINDOW_SERVICE.equals(name)) {
        return mWindowManager;
    } else if (SEARCH_SERVICE.equals(name)) {
        ensureSearchManager();
        return mSearchManager;
    }
    return super.getSystemService(name);
}
```

3. getApplicationContext.getSystemService(Context.WINDOW_SERVICE)


getApplicationContext 等同于 getBaseContext.getApplicationContext；
都是调用其中base的方法，而这个base是主线程启动Activity的时候创建并attach上的，是一个ContextImpl对象。

因此3的实际实现在ContextImpl中：contextImpl中的getSystemService：

```java
public Object getSystemService(String name) {
    return SystemServiceRegistry.getSystemService(this, name);
}
```

4. 对比分析

对比1和2，1直接返回mWindowManager成员变量，这个变量是ActivityThread中启动Activity的时候调用activity的attach时赋值的。
2方法对window类型的服务进行了拦截，直接返回了本地变量mWindowManager，因此实际上对于获取wm服务两者得到的对象是一样的。
2中除了WINDOW_SERVICE和SEARCH_SERVICE，会调用父类方法，父类中又会对LAYOUT_INFLATER_SERVICE一次过滤，除了这三种类型，最终都会调用contextImpl的getsystemservice方法，也就和3效果一样了。
3方法是一个最根本的方法，甚至于在对mWindowManager赋值的时候也用到了这个方法。

activity的attach方法(`mWindowManager初始化过程`)

```java
final void attach(...){
    mWindow.setWindowManager(
            (WindowManager)context.getSystemService(Context.WINDOW_SERVICE),
            mToken, mComponent.flattenToString(),
            (info.flags & ActivityInfo.FLAG_HARDWARE_ACCELERATED) != 0);
    if (mParent != null) {
        mWindow.setContainer(mParent.getWindow());
    }
    mWindowManager = mWindow.getWindowManager();
}

```
其中 setWindowManager方法

```java
public void setWindowManager(WindowManager wm, IBinder appToken, String appName,
        boolean hardwareAccelerated) {
    mAppToken = appToken;
    mAppName = appName;
    mHardwareAccelerated = hardwareAccelerated
            || SystemProperties.getBoolean(PROPERTY_HARDWARE_UI, false);
    if (wm == null) {
        wm = (WindowManager)mContext.getSystemService(Context.WINDOW_SERVICE);
    }
    mWindowManager = ((WindowManagerImpl)wm).createLocalWindowManager(this);  //这里生成WindowManagerImpl对象
}
```
实际上这里通过类似3的方式获取了WindowManger，然后又创建了一个本地的mWindowManager。

1和2获取一个本地副本，3则是直接获取全局唯一的WindowManager服务类。具体差别我们继续看：

```java
public WindowManagerImpl createLocalWindowManager(Window parentWindow) {
    return new WindowManagerImpl(mContext, parentWindow);
}

private WindowManagerImpl(Context context, Window parentWindow) {
    mContext = context;
    mParentWindow = parentWindow;
}
```
这个mWindowManager的mParentWindow是被赋值的，也就是我们最初的mWindow，是一个PhoneWindow对象。

而3的方式获取的对象其mParentWindow为空，我们根据源码追踪:
```java
//ContextImpl.java
public Object getSystemService(String name) {
    return SystemServiceRegistry.getSystemService(this, name);
}

//SystemServiceRegistry.java 静态代码块中有对应的初始化
static{
registerService(Context.WINDOW_SERVICE, WindowManager.class,
        new CachedServiceFetcher<WindowManager>() {
    @Override
    public WindowManager createService(ContextImpl ctx) {
        return new WindowManagerImpl(ctx);
    }});
}

//WindowManagerImpl.java 构造函数
public WindowManagerImpl(Context context) {
    this(context, null);   //传入的window为null
}

private WindowManagerImpl(Context context, Window parentWindow) {
    mContext = context;
    mParentWindow = parentWindow;
}

```

WindowManangerGlobal中addView的时候会判断window.parentWindow
```java
    final WindowManager.LayoutParams wparams = (WindowManager.LayoutParams) params;
    if (parentWindow != null) {
        parentWindow.adjustLayoutParamsForSubWindow(wparams);           //有parent，说明添加的是子窗口，调整参数
    } else {
        // If there's no parent, then hardware acceleration for this view is
        // set from the application's hardware acceleration setting.
        final Context context = view.getContext();
        if (context != null
                && (context.getApplicationInfo().flags
                        & ApplicationInfo.FLAG_HARDWARE_ACCELERATED) != 0) {
            wparams.flags |= WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED;
        }
    }
```


5. 关于Dialog、PopupWindow、Toast

都要传入一个context，前两者不可使用getApplicationContext，否则会报错。Toast可以使用。
注意三者对应的窗口类型：TYPE_APPLICATION、TYPE_APPLICATION_PANEL、TYPE_TOAST。(wm.layoutparams.type参数)
原因分析：
最终都会调用mWindowManager.addView方法，而这个mWindowManager就是根据我们传入的context获取的，我们
前两类窗口显示的时候要求能够关联对应的Activity的窗体，因此需要传入activity这种类型的context，但是传入getApplicationContext的话在addview检查的时候会报错token为null。

核心问题在于wms服务端的addWindow方法的校验过程，`有待进一步分析并测试`。

```java
/**
    * Window type: a normal application window.  The {@link #token} must be
    * an Activity token identifying who the window belongs to.
    * In multiuser systems shows only on the owning user's window.
    */
public static final int TYPE_APPLICATION        = 2;

/**
    * Window type: a panel on top of an application window.  These windows
    * appear on top of their attached window.
    */
public static final int TYPE_APPLICATION_PANEL = FIRST_SUB_WINDOW;

/**
    * Window type: transient notifications.
    * In multiuser systems shows only on the owning user's window.
    */
public static final int TYPE_TOAST              = FIRST_SYSTEM_WINDOW+5;

```




## Toast调用流程(跨进程、多次binder交互)  

Toast的实现流程:
Toast.makeText.show

makeText:
```java
public static Toast makeText(Context context, CharSequence text, @Duration int duration) {
    Toast result = new Toast(context);  //实例化，其中会构建TN对象

    LayoutInflater inflate = (LayoutInflater)
            context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
    View v = inflate.inflate(com.android.internal.R.layout.transient_notification, null);
    TextView tv = (TextView)v.findViewById(com.android.internal.R.id.message);
    tv.setText(text);
    
    result.mNextView = v;      //设置view
    result.mDuration = duration;//设置持续时间

    return result;
}
```

makeText的时候会实例化一个Toast对象，实例化的过程中会构造一个TN对象(这里可以认为是App端的ToastNative对象，app的服务端，类似一个token，后续传给TMS用于回调)

```java
private static class TN extends ITransientNotification.Stub{
    .....
    .....
        final Runnable mShow = new Runnable() {
            @Override
            public void run() {
                handleShow();
            }
        };

        final Runnable mHide = new Runnable() {
            @Override
            public void run() {
                handleHide();
                // Don't do this in handleHide() because it is also invoked by handleShow()
                mNextView = null;
            }
        };

        public void show() {
            if (localLOGV) Log.v(TAG, "SHOW: " + this);
            mHandler.post(mShow);
        }

           public void hide() {
            if (localLOGV) Log.v(TAG, "HIDE: " + this);
            mHandler.post(mHide);
        }
}

```
最后app进程中tn对应的binder线程会调用show和hide来展示和取消toast，通过向app主线程发送消息来实现。
也就是说handleShow和handleHide会在App进程中的UI线程执行。

那么什么时候发生上述所说的`tn对应的binder线程会调用show和hide`？分析如下：

toast.show方法

```java
  public void show() {
        if (mNextView == null) {
            throw new RuntimeException("setView must have been called");
        }

        INotificationManager service = getService();    //对应   INotificationManager.Stub服务端的一个代理，用于binder调用
        String pkg = mContext.getOpPackageName();
        TN tn = mTN;
        tn.mNextView = mNextView;   //设置tn的view

        try {
            service.enqueueToast(pkg, tn, mDuration); //binder调用到systemserver进程
        } catch (RemoteException e) {
            // Empty
        }
    }
```
会调用目标服务(我们可以称之为ToastManagerService)的enqueueToast方法，将工作加入一个toast队列。
enqueueToast的服务端代码位于：

```java

 private final IBinder mService = new INotificationManager.Stub() {
     public void enqueueToast(String pkg, ITransientNotification callback, int duration){
         //注意这里的callback就是我们客户端binder调用时传过来的TN的代理
         //传过来的这个callback是app端ITransientNotification.stub服务的代理
         ....
     }
 }
```
随后会构建ToastRecord并加入一个队列中(其中先进行一些规则验证)，这里有队列就应有一个`消费者线程`取任务，任务执行使用callback进行回调向app进程发送消息。
通过调用callback的show和hide进行binder调用，触发我们之前TN的show和hide方法。正常是先调用callback.show，设置超时任务，过duration时间段后调用callback.hide


toast展示到窗口的过程：

Toast.Tn.handleShow最终在主线程执行

1. 根据需要进行handlehide
2. 获取context对象
    2.1 优先获取application级别 mView.getContext().getApplicationContext()
    2.2 前一步为空则使用mView.getContext()
3. 根据context获取windowmanager
4. 设置params
5. 利用wm的addview添加view到窗口

补充说明:
1. ToastManagerService何时注册(更为准确地描述,注册的是 INotificationManager.Stub的一个实例化binder服务对象)
注册流程链：

SystemServer.main
    run
        mSystemServiceManager.startService(NotificationManagerService.class);
            NotificationManagerService.onStart
                publishBinderService(Context.NOTIFICATION_SERVICE, mService);
                    SM.addService //注册服务
        notification = INotificationManager.Stub.asInterface(
                    ServiceManager.getService(Context.NOTIFICATION_SERVICE));

2. `tms中的toastqueue对应的消费者线程在哪里?没找到`

enqueToast方法分析:

第一次调用成功将ToastRecord放入队列后，会立即执行showNextToastLocked;
showNextToastLocked主要做两件事：
    向app端发起callback.show的调用(app端会处理toast的展示)
    scheduleTimeoutLocked,利用本线程的WorkerHandler发送一个消息，延时触发cancelToastLocked
cancelToastLocked执行过程也主要两件事：
     向app端发起callback.hide的调用(app端会处理toast的展示)、队列中移除记录
     如果队列中还有任务，执行showNextToastLocked，`这里就形成了一个循环`

`每次enqueueToast都会确定消费掉queue中的所有事件`
     


3. 为何使用这种模式?app->tms->app

完全可以省略掉于tms交互的环节，内部使用handler也可以做到。
具体原因没找到，一些猜测：
Toast窗口级别问题，代码版本演进问题。
可能早期toast这种系统级窗口需要系统进程来发起，那么通过tms来实现就可以理解了，但是24版本代码addview依然是在app发起的，可以在看下更早的版本实现。

不过snackbar就是app进程内使用handler来操作的。`snackbar应属于应用窗口?`

## 资源加载过程


## Android 性能优化