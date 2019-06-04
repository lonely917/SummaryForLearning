## 问题清单


Q: waiting for another flutter command to release the startup lock

    A: 任务管理器中关掉dart.exe,在flutter sdk目录，找到bin\cache\目录下的lockfile，删掉即可。

`Q: 通过Navigator进行路由的时候，A->B->C，从摁下设备返回键，有的返回到B,有的返回到A?`

    A: XXX

Q: 使用MaterialPageRoute的时候，新的界面没有左上方的返回箭头?

    A: 注意新的widget是Scaffold即可，如果是MaterialApp则看不到返回箭头。

Q: expanded的使用

Q: column和row 高度或者宽度超出边界的处理?

Q: MainAxisAlignment的五种对齐方式?

Q: 布局超出屏幕宽度或者高度，滑动的实现?

Q：BoxFit的不同形式?

Q: Container的width不起作用?

Q: Flutter APP 使用卡顿?(列表、界面切换等)
dubug版本确实会出现这样的情况，使用release包的时候会流畅很多，不会有如此明显的卡顿。
在工程目录执行flutter build apk命令(相当于flutter build apk --release),会生成app-release.apk,可以使用flutter install进行安装.

Q: declarative UI vs imperative UI

Q: 使用flutter的widget来实现Android中的不同布局，linearlayout、framelayout、relativelayout？

    A: flutter从一个不同维度进行布局的组成，通过新的控件体系来实现不同布局。
    下述资料(flutter官方推荐博文)对照性质地进行了一定程度的剖析，来熟悉flutter widgets的不同属性。
    https://stackoverflow.com/questions/44396075/equivalent-of-relativelayout-in-flutter
    https://proandroiddev.com/flutter-for-android-developers-how-to-design-linearlayout-in-flutter-5d819c0ddf1a
    对应示例https://github.com/burhanrashid52/FlutterForAndroidExample

    {stack、column、row} {align、positioned、expanded、flexible}
    布局+对内约束;布局+对外约束

Q: flutter项目的依赖包下载到哪里去了?
    A：flutter sdk目录中.pub-cache\hosted\目录下有dart和flutter相关的依赖包目录。

Q: flutter项目依赖包通过什么下载的，程序通过什么编译的?
    A：下载工具 sdk\packages\flutter_tools\gradle指定了基本的gradle版本和仓库地址。

Q: flutter常用命令
    A: 
    flutter packages get; 
    flutter build apk (--release/debug); 
    flutter run (dart file name)

Q：布局常见调试错误

1. widget build的返回值不能是null
2. 外部没有material app,Text一定要有textdirection，否则会报错：Text或者其ancestor没有方向。
3. bottomnavigationbaritem的title不能为空
4. RaisedButton上方和下方有一个空白区域，垂直方向padding设置不成0?使用MaterialTapTargetSize属性设置，选择shrinkwrap,否则控件默认会保留一部分点击区域。
https://stackoverflow.com/questions/53714646/unwanted-space-appearing-between-raisedbutton-widgets-in-a-column

Q：Navigator operation requested with a context that does not include a Navigator

    A:  两个解决方案
        1-使用builder来创建floatingActionButton
        2-把MaterialApp的home单独拿出来写一个class，不要使用直接在home:参数中创建的形式。(https://github.com/flutter/flutter/issues/15919)

Q：TextField控件，键盘弹出问题，布局是否resize?
    A：In your Scaffold, set resizeToAvoidBottomPadding property to false.
    默认为true，使得底部边缘上移，相关的控件(依附于bottom的布局)会resize。设置属性为false，则弹出的键盘会遮盖相关控件。注意，上移只会使得受影响控件上移，若想要整体布局上移需要从布局构建方面进行考虑。比如用一个控件包裹一块区域，然后这个区域上移，类似微信聊天输入内容的时候，界面的行为。对于登陆场景，一般都是将输入框的位置放置高位，键盘遮盖不到即可(这也是一种主流适配方案,相对于resize体验更好)，对于Android的键盘设置行为，softinputmode属性等。

Q：rendering.dart中的调试参数设置，不起作用?