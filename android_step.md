## APP性能优化

## APK包体积优化

## APP内存优化

## APP网络优化

## 主流APP分析(正反向)

## 优化实践

## 插件化技术原理

## 组件化和动态化

## 常用分析手段
1. APP内Activity、Fragment生命周期监控-打点插桩分析、耗时信息、各周期信号检测
2. 系统角度监控Activity调用，可从WMS入手。(Fragment有没有类似策略?从WMS入手?)

```
//ActivityManager
12-31 10:17:08.808 1681-3283/? W/ActivityManager: startActivityMayWait() packageName = top.wenburgyan.officeassistant, appSourcePath = /data/app/top.wenburgyan.officeassistant-2/base.apk
12-31 10:17:08.810 1681-3283/? I/ActivityManager: START u0 {cmp=top.wenburgyan.officeassistant/.domain_contact.ContactDetailActivity (has extras)} from uid 10068 on display 0
12-31 10:17:09.099 1681-2145/? I/ActivityManager: Displayed top.wenburgyan.officeassistant/.domain_contact.ContactDetailActivity: +271ms
12-31 10:17:15.394 1681-3330/? W/ActivityManager: startActivityMayWait() packageName = top.wenburgyan.officeassistant, appSourcePath = /data/app/top.wenburgyan.officeassistant-2/base.apk
12-31 10:17:15.397 1681-3330/? I/ActivityManager: START u0 {cmp=top.wenburgyan.officeassistant/.SettingActivity} from uid 10068 on display 0
12-31 10:17:15.634 1681-2145/? I/ActivityManager: Displayed top.wenburgyan.officeassistant/.SettingActivity: +214ms


```

```
//WindowManager
12-31 10:18:46.633 1681-3410/? V/WindowManager: addAppToken: AppWindowToken{1b6d473 token=Token{2fba08e2 ActivityRecord{1e569dad u0 top.wenburgyan.officeassistant/.domain_contact.ContactDetailActivity t1619}}} to stack=1 task=1619 at 1
12-31 10:18:46.730 1681-3392/? V/WindowManager: Adding window Window{1a6c67a9 u0 top.wenburgyan.officeassistant/top.wenburgyan.officeassistant.domain_contact.ContactDetailActivity} at 6 of 13 (after Window{399e851d u0 top.wenburgyan.officeassistant/top.wenburgyan.officeassistant.HomeActivity})
12-31 10:18:52.908 1681-3353/? V/WindowManager: addAppToken: AppWindowToken{1d7dc063 token=Token{203a6e92 ActivityRecord{3a874c1d u0 top.wenburgyan.officeassistant/.SettingActivity t1619}}} to stack=1 task=1619 at 1
12-31 10:18:53.003 1681-3262/? V/WindowManager: Adding window Window{16d18d19 u0 top.wenburgyan.officeassistant/top.wenburgyan.officeassistant.SettingActivity} at 6 of 13 (after Window{399e851d u0 top.wenburgyan.officeassistant/top.wenburgyan.officeassistant.HomeActivity})

```


3. APP内GC日志检测(dalvik和art的GC算法 vs jvm GC算法、内存分布、app初始内存分配策略)

```
12-31 10:12:28.934 30616-30636/top.wenburgyan.officeassistant I/art: Explicit concurrent mark sweep GC freed 400998(12MB) AllocSpace objects, 14(5MB) LOS objects, 40% free, 5MB/8MB, paused 27.097ms total 175.987ms
12-31 10:12:30.768 30616-30636/top.wenburgyan.officeassistant I/art: Explicit concurrent mark sweep GC freed 4413(337KB) AllocSpace objects, 0(0B) LOS objects, 39% free, 4MB/8MB, paused 746us total 53.745ms
12-31 10:12:32.943 30616-30636/top.wenburgyan.officeassistant I/art: Explicit concurrent mark sweep GC freed 319(14KB) AllocSpace objects, 0(0B) LOS objects, 39% free, 4MB/8MB, paused 1.129ms total 59.907ms
12-31 10:12:35.118 30616-30636/top.wenburgyan.officeassistant I/art: Explicit concurrent mark sweep GC freed 304(13KB) AllocSpace objects, 0(0B) LOS objects, 39% free, 4MB/8MB, paused 1.114ms total 61.968ms
12-31 10:12:37.278 30616-30636/top.wenburgyan.officeassistant I/art: Explicit concurrent mark sweep GC freed 304(13KB) AllocSpace objects, 0(0B) LOS objects, 39% free, 4MB/8MB, paused 824us total 48.452ms

```
4. dumpsys指令
```
//找到指定的进程
ywb@aisino MINGW64 ~/Desktop
$ adb shell ps | grep assistant
u0_a68    30616 301   1011448 92124 ffffffff 00000000 S top.wenburgyan.officeassistant

//查看进程中的线程
$ adb shell ps -t 30616
USER     PID   PPID  VSIZE  RSS     WCHAN    PC        NAME
u0_a68    30616 301   1010392 92132 ffffffff 00000000 S top.wenburgyan.officeassistant
u0_a68    30619 30616 1010392 92132 ffffffff 00000000 S Heap thread poo
u0_a68    30620 30616 1010392 92132 ffffffff 00000000 S Heap thread poo
u0_a68    30621 30616 1010392 92132 ffffffff 00000000 S Heap thread poo
u0_a68    30623 30616 1010392 92132 ffffffff 00000000 S Signal Catcher
u0_a68    30625 30616 1010392 92132 ffffffff 00000000 S JDWP
u0_a68    30627 30616 1010392 92132 ffffffff 00000000 S ReferenceQueueD
u0_a68    30628 30616 1010392 92132 ffffffff 00000000 S FinalizerDaemon
u0_a68    30629 30616 1010392 92132 ffffffff 00000000 S FinalizerWatchd
u0_a68    30630 30616 1010392 92132 ffffffff 00000000 S HeapTrimmerDaem
u0_a68    30631 30616 1010392 92132 ffffffff 00000000 S GCDaemon
u0_a68    30632 30616 1010392 92132 ffffffff 00000000 S Binder_1
u0_a68    30633 30616 1010392 92132 ffffffff 00000000 S Binder_2
u0_a68    30636 30616 1010392 92132 ffffffff 00000000 S LeakCanary-Heap
u0_a68    30641 30616 1010392 92132 ffffffff 00000000 S RenderThread
u0_a68    30642 30616 1010392 92132 ffffffff 00000000 S AsyncTask #1
u0_a68    30643 30616 1010392 92132 ffffffff 00000000 S AsyncTask #2
u0_a68    31274 30616 1010392 92132 ffffffff 00000000 S AsyncTask #3
u0_a68    31275 30616 1010392 92132 ffffffff 00000000 S AsyncTask #4
u0_a68    31315 30616 1010392 92132 ffffffff 00000000 S AsyncTask #5
u0_a68    2338  30616 1010392 92132 ffffffff 00000000 S Binder_3
u0_a68    9175  30616 1010392 92132 ffffffff 00000000 S pool-1-thread-1
u0_a68    27449 30616 1010392 92132 ffffffff 00000000 S hwuiTask1
u0_a68    27450 30616 1010392 92132 ffffffff 00000000 S hwuiTask2
u0_a68    31947 30616 1005828 69744 ffffffff 00000000 S pool-14-thread- //进入到activity启动了一个线程池，executorservice线程池，activity退出后结束
u0_a68    31950 30616 1005828 69744 ffffffff 00000000 S OkHttp Connecti //okhttp相关 心跳相关，默认过比较长的一段时间后会结束，几分钟
u0_a68    31951 30616 1005828 69744 ffffffff 00000000 S Okio Watchdog //okhttp相关 一段时间后结束
u0_a68    29115 30616 1010392 92132 ffffffff 00000000 S GL updater


//查看应用相关activity状态，应用相关的task可能有多个，每个task里面是activity的堆叠
adb shell dumpsys activity "top.wenburgyan.officeassistant"

TASK com.squareup.leakcanary.top.wenburgyan.officeassistant id=1621
  ACTIVITY top.wenburgyan.officeassistant/leakcanary.internal.activity.LeakLauncherActivity 144e6a09 pid=30616
    Local Activity 33cbb4b5 State:
      mResumed=false mStopped=true mFinished=false
      mLoadersStarted=false
      mChangingConfigurations=false
      mCurrentConfig={1.0 ?mcc?mnc zh_CN ?layoutDir sw360dp w360dp h567dp 320dpi nrml port finger -keyb/v/h -nav/h s.5}
    FragmentManager misc state:
      mActivity=leakcanary.internal.activity.LeakActivity@33cbb4b5
      mContainer=android.app.Activity$1@80ac56d
      mCurState=3 mStateSaved=true mDestroyed=false
    ViewRoot:
      mAdded=true mRemoved=false
      mConsumeBatchedInputScheduled=false
      mConsumeBatchedInputImmediatelyScheduled=false
      mPendingInputEventCount=0
      mProcessInputEventsScheduled=false
      mTraversalScheduled=false
      android.view.ViewRootImpl$NativePreImeInputStage: mQueueLength=0
      android.view.ViewRootImpl$ImeInputStage: mQueueLength=0
      android.view.ViewRootImpl$NativePostImeInputStage: mQueueLength=0
    Choreographer:
      mFrameScheduled=false
      mLastFrameTime=114326788 (115373 ms ago)
    View Hierarchy:
      com.android.internal.policy.impl.PhoneWindow$DecorView{140b75a2 I.E..... R....... 0,0-720,1280}
        com.android.internal.widget.ActionBarOverlayLayout{2404a233 V.E..... ........ 0,0-720,1184 #102037e android:id/decor_content_parent}
          android.widget.FrameLayout{25d6c9f0 V.E..... ........ 0,162-720,1184 #1020002 android:id/content}
            ....
    Looper (main, tid 1) {f49ca03}
      (Total messages: 0, idling=false, quitting=false)

TASK top.wenburgyan.officeassistant id=1623
  ACTIVITY top.wenburgyan.officeassistant/.domain_contact.ContactDetailActivity 195dcaaa pid=30616
    Local FragmentActivity baefe7d State:
      mCreated=truemResumed=false mStopped=true mReallyStopped=true
      mLoadersStarted=true
    FragmentManager misc state:
      mHost=android.support.v4.app.FragmentActivity$HostCallbacks@2c4dc3b8
      mContainer=android.support.v4.app.FragmentActivity$HostCallbacks@2c4dc3b8
      mCurState=2 mStateSaved=true mDestroyed=false
    View Hierarchy:
      com.android.internal.policy.impl.PhoneWindow$DecorView{270e9591 I.E..... ... 0,0-720,1280}
        android.widget.LinearLayout{11ee0cf6 V.E..... ... 0,0-720,1184}

  ACTIVITY top.wenburgyan.officeassistant/.HomeActivity 3aa937b4 pid=30616
    Local FragmentActivity 352fef4f State:
      mCreated=truemResumed=false mStopped=true mReallyStopped=true
      mLoadersStarted=true
    Active Fragments in 5ab9c39:
      #0: null
      #1: ContactFragment{3c1b8642 #1 id=0x7f09002c}
        mFragmentId=#7f09002c mContainerId=#7f09002c mTag=null
        mState=2 mIndex=1 mWho=android:fragment:1 mBackStackNesting=0
        mAdded=true mRemoving=false mFromLayout=false mInLayout=false
        mHidden=false mDetached=false mMenuVisible=true mHasMenu=false
        mRetainInstance=false mRetaining=false mUserVisibleHint=true
        mFragmentManager=FragmentManager{5ab9c39 in HostCallbacks{314b73df}}
        mHost=android.support.v4.app.FragmentActivity$HostCallbacks@314b73df
        mSavedViewState={2131296377=AbsListView.SavedState{442fd7e selectedId=-9223372036854775808 firstId=-1 viewTop=0 position=0 height=742 filter=null checkState=null}, 2131296426=TextView.SavedState{73682df start=0 end=0 text=}}
        mContainer=android.widget.FrameLayout{a0c2f5 V.E..... ........ 4,4-716,906 #7f09002c app:id/content}
        mView=android.widget.FrameLayout{58fdb2c V.E..... ........ 0,0-712,902}
        mInnerView=android.widget.FrameLayout{58fdb2c V.E..... ........ 0,0-712,902}
        Child FragmentManager{230fe9f5 in ContactFragment{3c1b8642}}:
          FragmentManager misc state:
            mHost=android.support.v4.app.FragmentActivity$HostCallbacks@314b73df
            mContainer=android.support.v4.app.Fragment$2@2f8d738a
            mParent=ContactFragment{3c1b8642 #1 id=0x7f09002c}
            mCurState=2 mStateSaved=true mDestroyed=false
    Added Fragments:
      #0: ContactFragment{3c1b8642 #1 id=0x7f09002c}
    FragmentManager misc state:
      mHost=android.support.v4.app.FragmentActivity$HostCallbacks@314b73df
      mContainer=android.support.v4.app.FragmentActivity$HostCallbacks@314b73df
      mCurState=2 mStateSaved=true mDestroyed=false
      mAvailIndices: [0]
    View Hierarchy:
      com.android.internal.policy.impl.PhoneWindow$DecorView{39879971 I.E..... ... 0,0-720,1280}
        android.widget.LinearLayout{2de2f356 V.E..... ... 0,0-720,1184}
          android.view.ViewStub{3a49ffd7 G.E..... ... 0,0-0,0 #1020379}


//查看系统当前activity状态，两个Stack，一个属于home、一个属于应用，应用的stack中有多个task，每个task的描述是TaskRecord,task内部会有多个activity，activity的描述是ActivityRecord。

adb shell dumpsys activity activities

ACTIVITY MANAGER ACTIVITIES (dumpsys activity activities)
Display #0 (activities from top to bottom):
  Stack #1:
    Task id #1623
    * TaskRecord{3b740dbf #1623 A=top.wenburgyan.officeassistant U=0 sz=2}
      userId=0 effectiveUid=u0a68 mCallingUid=u0a76 mCallingPackage=com.pax.ipp.launcher
      affinity=top.wenburgyan.officeassistant
      intent={act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=top.wenburgyan.officeassistant/.HomeActivity}
      realActivity=top.wenburgyan.officeassistant/.HomeActivity
      autoRemoveRecents=false isPersistable=true numFullscreen=2 taskType=0 mTaskToReturnTo=1
      rootWasReset=true mNeverRelinquishIdentity=true mReuseTask=false
      Activities=[ActivityRecord{3aa937b4 u0 top.wenburgyan.officeassistant/.HomeActivity t1623}, ActivityRecord{195dcaaa u0 top.wenburgyan.officeassistant/.domain_contact.ContactDetailActivity t1623}]
      askedCompatMode=false inRecents=true isAvailable=true
      lastThumbnail=android.graphics.Bitmap@8a2de52 lastThumbnailFile=/data/system/recent_images/1623_task_thumbnail.png
      hasBeenVisible=true firstActiveTime=1577787663809 lastActiveTime=1577787663809 (inactive for 4s)
      * Hist #1: ActivityRecord{195dcaaa u0 top.wenburgyan.officeassistant/.domain_contact.ContactDetailActivity t1623}
          packageName=top.wenburgyan.officeassistant processName=top.wenburgyan.officeassistant
          launchedFromUid=10068 launchedFromPackage=top.wenburgyan.officeassistant userId=0
          app=ProcessRecord{25c25857 30616:top.wenburgyan.officeassistant/u0a68}
          Intent { cmp=top.wenburgyan.officeassistant/.domain_contact.ContactDetailActivity bnds=[46,542][673,1169] (has extras) }
          frontOfTask=false task=TaskRecord{3b740dbf #1623 A=top.wenburgyan.officeassistant U=0 sz=2}
          taskAffinity=top.wenburgyan.officeassistant
          realActivity=top.wenburgyan.officeassistant/.domain_contact.ContactDetailActivity
          baseDir=/data/app/top.wenburgyan.officeassistant-2/base.apk
          dataDir=/data/data/top.wenburgyan.officeassistant
          stateNotNeeded=false componentSpecified=true mActivityType=0
          compat={320dpi} labelRes=0x7f0f0020 icon=0x7f0d0000 theme=0x7f100005
          config={1.0 ?mcc?mnc zh_CN ?layoutDir sw360dp w360dp h567dp 320dpi nrml port finger -keyb/v/h -nav/h s.5}
          taskDescription: iconFilename=null label="null" color=ff3f51b5
          launchFailed=false launchCount=0 lastLaunchTime=-11m7s64ms
          haveState=false icicle=null
          state=RESUMED stopped=false delayedResume=false finishing=false
          keysPaused=false inHistory=true visible=true sleeping=false idle=true
          fullscreen=true noDisplay=false immersive=false launchMode=0
          frozenBeforeDestroy=false forceNewConfig=false
          mActivityType=APPLICATION_ACTIVITY_TYPE
          waitingVisible=false nowVisible=true lastVisibleTime=-3s371ms
      * Hist #0: ActivityRecord{3aa937b4 u0 top.wenburgyan.officeassistant/.HomeActivity t1623}
          packageName=top.wenburgyan.officeassistant processName=top.wenburgyan.officeassistant
          launchedFromUid=10076 launchedFromPackage=com.pax.ipp.launcher userId=0
          .....
          mActivityType=APPLICATION_ACTIVITY_TYPE
          waitingVisible=false nowVisible=false lastVisibleTime=-11m34s33ms
    Task id #1621
    * TaskRecord{201c3328 #1621 A=com.squareup.leakcanary.top.wenburgyan.officeassistant U=0 sz=1}
      userId=0 effectiveUid=u0a68 mCallingUid=u0a76 mCallingPackage=com.pax.ipp.launcher
      affinity=com.squareup.leakcanary.top.wenburgyan.officeassistant
      intent={act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=top.wenburgyan.officeassistant/leakcanary.internal.activity.LeakActivity}
      origActivity=top.wenburgyan.officeassistant/leakcanary.internal.activity.LeakLauncherActivity
      realActivity=top.wenburgyan.officeassistant/leakcanary.internal.activity.LeakActivity
      autoRemoveRecents=false isPersistable=true numFullscreen=1 taskType=0 mTaskToReturnTo=1
      rootWasReset=true mNeverRelinquishIdentity=true mReuseTask=false
      Activities=[ActivityRecord{144e6a09 u0 top.wenburgyan.officeassistant/leakcanary.internal.activity.LeakLauncherActivity t1621}]
      askedCompatMode=false inRecents=true isAvailable=true
      lastThumbnail=android.graphics.Bitmap@d543c8c lastThumbnailFile=/data/system/recent_images/1621_task_thumbnail.png
      hasBeenVisible=true firstActiveTime=1577781415573 lastActiveTime=1577781415573 (inactive for 6252s)
      * Hist #0: ActivityRecord{144e6a09 u0 top.wenburgyan.officeassistant/leakcanary.internal.activity.LeakLauncherActivity t1621}
          packageName=top.wenburgyan.officeassistant processName=top.wenburgyan.officeassistant
          launchedFromUid=10076 launchedFromPackage=com.pax.ipp.launcher userId=0
          app=ProcessRecord{25c25857 30616:top.wenburgyan.officeassistant/u0a68}
          Intent { act=android.intent.action.MAIN cat=[android.intent.category.LAUNCHER] flg=0x10200000 cmp=top.wenburgyan.officeassistant/leakcanary.internal.activity.LeakLauncherActivity bnds=[57,265][662,870] }
          frontOfTask=true task=TaskRecord{201c3328 #1621 A=com.squareup.leakcanary.top.wenburgyan.officeassistant U=0 sz=1}
          taskAffinity=com.squareup.leakcanary.top.wenburgyan.officeassistant
          realActivity=top.wenburgyan.officeassistant/leakcanary.internal.activity.LeakLauncherActivity
          baseDir=/data/app/top.wenburgyan.officeassistant-2/base.apk
          dataDir=/data/data/top.wenburgyan.officeassistant
          stateNotNeeded=false componentSpecified=true mActivityType=0
          compat={320dpi} labelRes=0x7f0f002f icon=0x7f0d0002 theme=0x7f10017f
          config={1.0 ?mcc?mnc zh_CN ?layoutDir sw360dp w360dp h567dp 320dpi nrml port finger -keyb/v/h -nav/h s.5}
          taskDescription: iconFilename=null label="null" color=ff212121
          launchFailed=false launchCount=0 lastLaunchTime=-7h57m46s818ms
          haveState=true icicle=Bundle[mParcelledData.dataSize=1984]
          state=STOPPED stopped=true delayedResume=false finishing=false
          keysPaused=false inHistory=true visible=false sleeping=false idle=true
          fullscreen=true noDisplay=false immersive=false launchMode=0
          frozenBeforeDestroy=false forceNewConfig=false
          mActivityType=APPLICATION_ACTIVITY_TYPE
          waitingVisible=false nowVisible=false lastVisibleTime=-1h44m56s140ms

    Running activities (most recent first):
      TaskRecord{3b740dbf #1623 A=top.wenburgyan.officeassistant U=0 sz=2}
        Run #2: ActivityRecord{195dcaaa u0 top.wenburgyan.officeassistant/.domain_contact.ContactDetailActivity t1623}
        Run #1: ActivityRecord{3aa937b4 u0 top.wenburgyan.officeassistant/.HomeActivity t1623}
      TaskRecord{201c3328 #1621 A=com.squareup.leakcanary.top.wenburgyan.officeassistant U=0 sz=1}
        Run #0: ActivityRecord{144e6a09 u0 top.wenburgyan.officeassistant/leakcanary.internal.activity.LeakLauncherActivity t1621}

    mResumedActivity: ActivityRecord{195dcaaa u0 top.wenburgyan.officeassistant/.domain_contact.ContactDetailActivity t1623}
    mLastPausedActivity: ActivityRecord{195dcaaa u0 top.wenburgyan.officeassistant/.domain_contact.ContactDetailActivity t1623}

  Stack #0:
    Task id #1594
    * TaskRecord{b0db327 #1594 A=com.android.systemui U=0 sz=1}
      userId=0 effectiveUid=1000 mCallingUid=1000 mCallingPackage=com.android.systemui
      affinity=com.android.systemui
      intent={act=com.android.systemui.recents.SHOW_RECENTS flg=0x10804000 cmp=com.android.systemui/.recents.RecentsActivity bnds=[36,925][685,1574]}
      realActivity=com.android.systemui/.recents.RecentsActivity
      autoRemoveRecents=false isPersistable=false numFullscreen=1 taskType=2 mTaskToReturnTo=1
      rootWasReset=false mNeverRelinquishIdentity=true mReuseTask=false
      Activities=[ActivityRecord{3fbbf072 u0 com.android.systemui/.recents.RecentsActivity t1594}]
      askedCompatMode=false inRecents=true isAvailable=true
      lastThumbnail=null lastThumbnailFile=/data/system/recent_images/1594_task_thumbnail.png
      hasBeenVisible=true firstActiveTime=1577787663802 lastActiveTime=1577787663802 (inactive for 4s)
      * Hist #0: ActivityRecord{3fbbf072 u0 com.android.systemui/.recents.RecentsActivity t1594}
          packageName=com.android.systemui processName=com.android.systemui
          launchedFromUid=1000 launchedFromPackage=com.android.systemui userId=0
          app=ProcessRecord{17698133 2697:com.android.systemui/1000}
          Intent { act=com.android.systemui.recents.SHOW_RECENTS flg=0x10804000 cmp=com.android.systemui/.recents.RecentsActivity bnds=[36,925][685,1574] }
          frontOfTask=true task=TaskRecord{b0db327 #1594 A=com.android.systemui U=0 sz=1}
          taskAffinity=com.android.systemui
          realActivity=com.android.systemui/.recents.RecentsActivity
          baseDir=/system/priv-app/SystemUI/SystemUI.apk
          dataDir=/data/data/com.android.systemui
          stateNotNeeded=true componentSpecified=true mActivityType=2
          compat={320dpi} labelRes=0x7f08009f icon=0x7f0200ec theme=0x7f0c0004
          config={1.0 ?mcc?mnc zh_CN ?layoutDir sw360dp w360dp h567dp 320dpi nrml port finger -keyb/v/h -nav/h s.5}
          taskDescription: iconFilename=null label="null" color=ffe6e6e6
          launchFailed=false launchCount=0 lastLaunchTime=-1d7h47m17s523ms
          haveState=true icicle=Bundle[mParcelledData.dataSize=1412]
          state=STOPPED stopped=true delayedResume=false finishing=false
          keysPaused=false inHistory=true visible=false sleeping=false idle=true
          fullscreen=true noDisplay=false immersive=false launchMode=3
          frozenBeforeDestroy=false forceNewConfig=false
          mActivityType=RECENTS_ACTIVITY_TYPE
          waitingVisible=false nowVisible=false lastVisibleTime=-7s790ms

    Task id #1590
    * TaskRecord{3c882841 #1590 I=com.pax.ipp.launcher/com.pax.launcher.ui.activity.LauncherActivity U=0 sz=1}
      userId=0 effectiveUid=u0a76 mCallingUid=1000 mCallingPackage=android
      intent={act=android.intent.action.MAIN cat=[android.intent.category.HOME] flg=0x10800000 cmp=com.pax.ipp.launcher/com.pax.launcher.ui.activity.LauncherActivity}
      realActivity=com.pax.ipp.launcher/com.pax.launcher.ui.activity.LauncherActivity
      autoRemoveRecents=false isPersistable=false numFullscreen=1 taskType=1 mTaskToReturnTo=0
      rootWasReset=false mNeverRelinquishIdentity=true mReuseTask=false
      Activities=[ActivityRecord{188bbfb9 u0 com.pax.ipp.launcher/com.pax.launcher.ui.activity.LauncherActivity t1590}]
      askedCompatMode=false inRecents=true isAvailable=true
      lastThumbnail=null lastThumbnailFile=/data/system/recent_images/1590_task_thumbnail.png
      hasBeenVisible=true firstActiveTime=1577787589124 lastActiveTime=1577787589124 (inactive for 78s)
      * Hist #0: ActivityRecord{188bbfb9 u0 com.pax.ipp.launcher/com.pax.launcher.ui.activity.LauncherActivity t1590}
          packageName=com.pax.ipp.launcher processName=com.pax.ipp.launcher
          launchedFromUid=0 launchedFromPackage=null userId=0
          app=ProcessRecord{32e7755a 3447:com.pax.ipp.launcher/u0a76}
          Intent { act=android.intent.action.MAIN cat=[android.intent.category.HOME] flg=0x10800000 cmp=com.pax.ipp.launcher/com.pax.launcher.ui.activity.LauncherActivity }
          frontOfTask=true task=TaskRecord{3c882841 #1590 I=com.pax.ipp.launcher/com.pax.launcher.ui.activity.LauncherActivity U=0 sz=1}
          taskAffinity=null
          realActivity=com.pax.ipp.launcher/com.pax.launcher.ui.activity.LauncherActivity
          baseDir=/data/app/com.pax.ipp.launcher-1/base.apk
          dataDir=/data/user/0/com.pax.ipp.launcher
          stateNotNeeded=true componentSpecified=false mActivityType=1
          compat={320dpi} labelRes=0x7f070015 icon=0x7f030000 theme=0x7f0900d3
          config={1.0 ?mcc?mnc zh_CN ?layoutDir sw360dp w360dp h567dp 320dpi nrml port finger -keyb/v/h -nav/h s.5}
          taskDescription: iconFilename=null label="null" color=ff212121
          launchFailed=false launchCount=0 lastLaunchTime=-1d7h52m35s202ms
          haveState=true icicle=Bundle[mParcelledData.dataSize=4132]
          state=STOPPED stopped=true delayedResume=false finishing=false
          keysPaused=false inHistory=true visible=false sleeping=false idle=true
          fullscreen=true noDisplay=false immersive=false launchMode=2
          frozenBeforeDestroy=false forceNewConfig=false
          mActivityType=HOME_ACTIVITY_TYPE
          waitingVisible=false nowVisible=false lastVisibleTime=-1m21s26ms

    Running activities (most recent first):
      TaskRecord{b0db327 #1594 A=com.android.systemui U=0 sz=1}
        Run #1: ActivityRecord{3fbbf072 u0 com.android.systemui/.recents.RecentsActivity t1594}
      TaskRecord{3c882841 #1590 I=com.pax.ipp.launcher/com.pax.launcher.ui.activity.LauncherActivity U=0 sz=1}
        Run #0: ActivityRecord{188bbfb9 u0 com.pax.ipp.launcher/com.pax.launcher.ui.activity.LauncherActivity t1590}

    mLastPausedActivity: ActivityRecord{3fbbf072 u0 com.android.systemui/.recents.RecentsActivity t1594}

  mFocusedActivity: ActivityRecord{188bbfb9 u0 com.pax.ipp.launcher/com.pax.launcher.ui.activity.LauncherActivity t1590}
  mFocusedStack=ActivityStack{1b1a87be stackId=1, 12 tasks} mLastFocusedStack=ActivityStack{1b1a87be stackId=1, 12 tasks}
  mSleepTimeout=false
  mCurTaskId=1624
  mUserStackInFront={}
  mActivityContainers={0=ActivtyContainer{0}A, 1=ActivtyContainer{1}A}



//dumpsys meminfo 查看指定程序内存信息

$ adb shell dumpsys meminfo "top.wenburgyan.officeassistant"
Applications Memory Usage (kB):
Uptime: 116700880 Realtime: 116700880

** MEMINFO in pid 30616 [top.wenburgyan.officeassistant] **
                   Pss  Private  Private  Swapped     Heap     Heap     Heap
                 Total    Dirty    Clean    Dirty     Size    Alloc     Free
                ------   ------   ------   ------   ------   ------   ------
  Native Heap     8065     8028        0      460     9592     6838     2753
  Dalvik Heap     6639     6592        0        0     9888     6016     3872
 Dalvik Other      260      260        0        0
        Stack      204      204        0        4
      Gfx dev     2172     2172        0        0
    Other dev        5        0        4        0
     .so mmap      867      192      216     1472
    .apk mmap      203        0       52        0
    .ttf mmap      159        0       36        0
    .dex mmap     4525        0     4524        0
    .oat mmap     2713        0     1332        0
    .art mmap     1628     1096      200        0
   Other mmap       47        4        4        0
   EGL mtrack    13440    13440        0        0
      Unknown      144      144        0       52
        TOTAL    41071    32132     6368     1988    19480    12854     6625

 Objects
               Views:      143         ViewRootImpl:        2
         AppContexts:        4           Activities:        2
              Assets:        3        AssetManagers:        3
       Local Binders:       13        Proxy Binders:       24
       Parcel memory:        6         Parcel count:       26
    Death Recipients:        0      OpenSSL Sockets:        0

 SQL
         MEMORY_USED:      110
  PAGECACHE_OVERFLOW:        8          MALLOC_SIZE:       62

 DATABASES
      pgsz     dbsz   Lookaside(b)          cache  Dbname
         4      236             72      123/36/10  /data/data/top.wenburgyan.officeassistant/databases/leaks.db
  
```

## art-gc
1. Explicit concurrent mark sweep GC freed
2. Background sticky concurrent mark sweep GC
3. Background sticky concurrent mark sweep GC

```
01-02 10:28:50.765 32249-32268/top.wenburgyan.officeassistant I/art: Explicit concurrent mark sweep GC freed 318(13KB) AllocSpace objects, 0(0B) LOS objects, 39% free, 5MB/8MB, paused 647us total 47.472ms
01-02 10:28:52.926 32249-32268/top.wenburgyan.officeassistant I/art: Explicit concurrent mark sweep GC freed 322(14KB) AllocSpace objects, 0(0B) LOS objects, 40% free, 5MB/8MB, paused 698us total 46.264ms
01-02 10:29:04.297 32249-32264/top.wenburgyan.officeassistant I/art: Background sticky concurrent mark sweep GC freed 310034(9MB) AllocSpace objects, 0(0B) LOS objects, 32% free, 15MB/23MB, paused 10.614ms total 83.384ms
01-02 10:29:13.685 32249-32264/top.wenburgyan.officeassistant I/art: Background partial concurrent mark sweep GC freed 202755(6MB) AllocSpace objects, 3(1946KB) LOS objects, 39% free, 15MB/26MB, paused 1.829ms total 110.659ms
```
## LeakCanary分析之-LeakActivity(单Activity应用设计)
-  ContentProvider的利用
-  NavigationActivity的设计
-  Screen的概念