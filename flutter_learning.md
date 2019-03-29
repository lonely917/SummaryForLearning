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