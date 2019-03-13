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