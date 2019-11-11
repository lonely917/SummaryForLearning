## 问题清单


Q: waiting for another flutter command to release the startup lock

>    A: 任务管理器中关掉dart.exe,在flutter sdk目录，找到bin\cache\目录下的lockfile，删掉即可。

`Q: 通过Navigator进行路由的时候，A->B->C，从摁下设备返回键，有的返回到B,有的返回到A?`

>    A: XXX

Q: 使用MaterialPageRoute的时候，新的界面没有左上方的返回箭头?

>    A: 注意新的widget是Scaffold即可，如果是MaterialApp则看不到返回箭头。

Q: expanded的使用

Q: column和row 高度或者宽度超出边界的处理?

Q: MainAxisAlignment的五种对齐方式?

Q: 布局超出屏幕宽度或者高度，滑动的实现?

Q：BoxFit的不同形式?

Q: Container的width不起作用?

Q: Flutter APP 使用卡顿?(列表、界面切换等)

>    A：dubug版本确实会出现这样的情况，使用release包的时候会流畅很多，不会有如此明显的卡顿。在工程目录执行flutter build apk命令(相当于flutter build apk --release),会生成app-release.apk,可以使用flutter install进行安装.

Q: declarative UI vs imperative UI

Q: 使用flutter的widget来实现Android中的不同布局，linearlayout、framelayout、relativelayout？

>    A: flutter从一个不同维度进行布局的组成，通过新的控件体系来实现不同布局。
    下述资料(flutter官方推荐博文)对照性质地进行了一定程度的剖析，来熟悉flutter widgets的不同属性。    
    {stack、column、row} {align、positioned、expanded、flexible}
    布局+对内约束;布局+对外约束
    
>    https://stackoverflow.com/questions/44396075/equivalent-of-relativelayout-in-flutter

>    https://proandroiddev.com/flutter-for-android-developers-how-to-design-linearlayout-in-flutter-5d819c0ddf1a

>    对应示例https://github.com/burhanrashid52/FlutterForAndroidExample



Q: flutter项目的依赖包下载到哪里去了?

>    A：flutter sdk目录中.pub-cache\hosted\目录下有dart和flutter相关的依赖包目录。Android相关的通过gradle下载到.gradle中相关目录caches/jar-3目录下。

Q: flutter项目依赖包通过什么下载的，程序通过什么编译的?
    
>    A：下载工具 sdk\packages\flutter_tools\gradle指定了基本的gradle版本和仓库地址。

`flutter packages get 指令通过什么下载的? gradle or dart？`

Q: flutter常用命令
    
A: 
1. flutter packages get;   //下载到flutter/.pub-cache目录下
2. flutter build apk (--release/debug); 
3. flutter run (dart file name)
4. 添加--verbose选项可以输出详细日志

Q：布局常见调试错误

1. widget build的返回值不能是null
2. 外部没有material app,Text一定要有textdirection，否则会报错：Text或者其ancestor没有方向。
3. bottomnavigationbaritem的title不能为空
4. RaisedButton上方和下方有一个空白区域，垂直方向padding设置不成0?使用MaterialTapTargetSize属性设置，选择shrinkwrap,否则控件默认会保留一部分点击区域。https://stackoverflow.com/questions/53714646/unwanted-space-appearing-between-raisedbutton-widgets-in-a-column

Q：Navigator operation requested with a context that does not include a Navigator

A:  两个解决方案

1. 使用builder来创建floatingActionButton
2. 把MaterialApp的home单独拿出来写一个class，不要使用直接在home:参数中创建的形式。(https://github.com/flutter/flutter/issues/15919)

Q：TextField控件，键盘弹出问题，布局是否resize?

>    A：In your Scaffold, set resizeToAvoidBottomPadding property to false.
    默认为true，使得底部边缘上移，相关的控件(依附于bottom的布局)会resize。设置属性为false，则弹出的键盘会遮盖相关控件。注意，上移只会使得受影响控件上移，若想要整体布局上移需要从布局构建方面进行考虑。比如用一个控件包裹一块区域，然后这个区域上移，类似微信聊天输入内容的时候，界面的行为。对于登陆场景，一般都是将输入框的位置放置高位，键盘遮盖不到即可(这也是一种主流适配方案,相对于resize体验更好)，对于Android的键盘设置行为，softinputmode属性等。

Q：rendering.dart中的调试参数设置，不起作用?

Q: flutter run -d chrome 

Q: flutter run -d chrome报错，no device found with name or id matching 'chrome'
 
A:
1. 明确使用的flutter sdk已经支持web开发，注意是否已经从stable分支切换到master分支。
2. flutter doctor --verbose //如果支持web 会有“Chrome - develop for the web”的提示
3. flutter channel //查看当前频道
4. flutter channel master //切换到master分支
5. flutter config --enable-web //开启web支持
6. flutter run -d chrome //从chrome运行web应用 

Q: Android Studio 自定义一些工作目录减小c盘空间占用

A: 

AS的bin目录idea.properties文件中有以下几行说明

    # idea.config.path=${user.home}/.AndroidStudioPreview/config
    # idea.system.path=${user.home}/.AndroidStudioPreview/system
    # idea.plugins.path=${idea.config.path}/plugins

这些目录都会占用一定的存储空间，可以自定义修改位置

1. .gradle目录，默认c盘user目录会有一个.gradle目录，这里会有程序构建的时候下载的gradle可执行包以及源码包，还会有项目依赖的第三方aar，可以在AS-setting-build-gradle选项中修改service directory path，更改AS相关的gradle工作目录，因为gradle目录长期会下载很多包，比较占用空间

2. .avd目录，默认c盘user目录会有一个.avd目录，存放虚拟机相关文件，会比较占空间，可以在环境变量中添加ANDROID_SDK_HOME=xxxxx指定创建虚拟机的时候对应文件的创建位置，对于已经创建的虚拟机，可以移动对应镜像文件后修改镜像相关的ini配置文件，其中的path指定了虚拟机的位置。

3. windows临时目录更改，环境变量中有TEMP和TMP两个变量，对应默认c盘user目录用户名文件夹下的AppData\Local\Temp，这是一个系统临时目录，对AS而言sdk相关包下载的时候会首先下载到这里作为一个暂存位置，所以c盘空间不足的时候，下载sdk包或者镜像文件的过程中会报错，可以修改变量值，更改到其他位置。

通过上述步骤可以减小对c盘空间的负荷。


Q: flutter run 报错 

    [ +357 ms] FAILURE: Build failed with an exception.
    [   +3 ms] * What went wrong:
    [   +1 ms] Unable to start the daemon process.

>A: gradle.properties中heap大小参数设置不适合本机，没有启动daemon服务，可以改小一些，比如改为512。修改后为org.gradle.jvmargs=-Xmx512M。


Q: 