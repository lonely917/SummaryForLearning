<!-- TOC -->

- [View绘制关键类](#view绘制关键类)
    - [Paint](#paint)
    - [Canvas](#canvas)
    - [Path](#path)
- [Canvas的drawXX系列](#canvas的drawxx系列)
    - [drawColor](#drawcolor)
    - [drawCircle](#drawcircle)
    - [drawRect](#drawrect)
    - [drawPoint](#drawpoint)
    - [drawOval](#drawoval)
    - [drawLine/drawLines](#drawlinedrawlines)
    - [drawRoundRect](#drawroundrect)
    - [drawArc](#drawarc)
    - [drawPath](#drawpath)
    - [drawText & drawBitmap](#drawtext--drawbitmap)
- [View绘制的一些辅助](#view绘制的一些辅助)
    - [shader使用](#shader使用)
    - [canvas变换](#canvas变换)
- [View绘制顺序](#view绘制顺序)
    - [draw绘制逻辑](#draw绘制逻辑)
    - [setWillNotDraw-是否执行完整绘制流程](#setwillnotdraw-是否执行完整绘制流程)
    - [onDraw重绘优化](#ondraw重绘优化)
    - [view的forground属性](#view的forground属性)
- [如何查看某一个控件的所有属性](#如何查看某一个控件的所有属性)
- [属性动画](#属性动画)
- [android中像素、英寸、dpi、dip、DisplayMetrics](#android中像素英寸dpidipdisplaymetrics)
- [android中字体大小的含义](#android中字体大小的含义)
- [string.xml使用格式化参数](#stringxml使用格式化参数)
- [控件源码](#控件源码)
- [布局源码](#布局源码)
- [centerInside和centerCrop等模式](#centerinside和centercrop等模式)
- [傻瓜式修正尺寸-resolveSize](#傻瓜式修正尺寸-resolvesize)
- [onAttachedToWindow](#onattachedtowindow)
- [触摸反馈](#触摸反馈)
- [ViewStub](#viewstub)
- [扩展](#扩展)

<!-- /TOC -->
## View绘制关键类

### Paint

### Canvas

### Path

## Canvas的drawXX系列

### drawColor
绘制一个纯色画布
```java
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    canvas.drawColor(Color.YELLOW);
}
```
### drawCircle
绘制一个实心圆和一个空心圆
```java
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    Paint paint = new Paint();
    paint.setAntiAlias(true); //边缘光滑效果与否 - 是
    paint.setColor(Color.BLACK);//画笔颜色设置 - 黑色
    paint.setStyle(Paint.Style.FILL);//画笔实心填充或者画线 - 实心
    canvas.drawCircle(100,100,80, paint);//圆形

    paint.setColor(Color.BLACK);
    paint.setAntiAlias(false);//边缘光滑效果与否 - 否
    paint.setStyle(Paint.Style.STROKE);//画笔实心填充或者画线 - 实心
    paint.setStrokeWidth(20);//画线的宽度
    canvas.drawCircle(300,300,80, paint);
}
```
### drawRect
绘制一个居中的实心矩形
```java
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    Paint paint = new Paint();
    paint.setColor(Color.BLACK);
    paint.setStyle(Paint.Style.FILL);
    int width = 400;
    int height = 400;
    int left = getMeasuredWidth()/2 - width/2;
    int top = getMeasuredHeight()/2 - height/2;
    int right =  getMeasuredWidth()/2 + width/2;
    int bottom = getMeasuredHeight()/2 + height/2;
    canvas.drawRect(left,top,right,bottom,paint);//居中绘制实心矩形
}
```

### drawPoint
绘制一个方形和一个圆形的点
```java
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    int center_x = getMeasuredWidth()/2;
    int center_y = getMeasuredHeight()/2;

    Paint paint= new Paint();
    paint.setStrokeWidth(50); //线宽，点大小，这里设置50相当于一个50*50的区域里面绘制图形
    paint.setStrokeCap(Paint.Cap.ROUND); //圆点
    canvas.drawPoint(center_x-200,center_y,paint);

    paint.setStrokeCap(Paint.Cap.SQUARE);//方点，也是一种绘制方形的思路，只能填充，根据中心点和边长
    canvas.drawPoint(center_x+200,center_y,paint);
}
```

### drawOval
绘制椭圆

```java
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);

    int center_x = getMeasuredWidth()/2;
    int center_y = getMeasuredHeight()/2;

    Paint paint= new Paint();
    paint.setAntiAlias(true); //边缘光滑效果
    paint.setStyle(Paint.Style.FILL);
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
        canvas.drawOval(center_x-200,center_y-100,center_x+200,center_y+100, paint);
    }
}
```
### drawLine/drawLines
drawlines绘制一系列点，不是按顺序将所有点连接起来，是一对儿点确定一条线，依次绘制各条线。

```java
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);

    Paint paint = new Paint();
    paint.setStrokeWidth(10);
    paint.setColor(Color.BLACK);
    canvas.drawLine(200, 200, 400, 400, paint);

    canvas.drawLines(new float[]{0,0,0,100,0,100,100,100,100,100,50,50}, paint);
}
```

### drawRoundRect
native_drawRoundRect的实现?圆角实现原理，radius_x和radius_y如何作用?

```java
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);

    Paint paint = new Paint();
    paint.setStyle(Paint.Style.FILL);
    paint.setAntiAlias(true);
    paint.setColor(Color.BLACK);
    int width = 400;
    int height = 200;
    int center_x = getMeasuredWidth()/2;
    int center_y = getMeasuredHeight()/2;
    float left = center_x - width/2;
    float top = center_y - height/2;
    float right = center_x + width/2;
    float bottom = center_y + height/2;
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
        canvas.drawRoundRect(left, top, right, bottom, 30, 30, paint);
    }
}
```

### drawArc
画弧形和扇形
```java
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    Paint paint = new Paint();
    paint.setAntiAlias(true);
    paint.setColor(Color.BLACK);
    int width = 400;
    int height = 200;
    int center_x = getMeasuredWidth()/2;
    int center_y = getMeasuredHeight()/2;
    float left = center_x - width/2;
    float top = center_y - height/2;
    float right = center_x + width/2;
    float bottom = center_y + height/2;
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
        canvas.drawArc(left, top, right, bottom,30,60,true,paint);//绘制扇形
        canvas.drawArc(left, top, right, bottom, -30, -60, false, paint); // 绘制弧形

        paint.setStyle(Paint.Style.STROKE); // 画线模式
        canvas.drawArc(left, top, right, bottom, -120, -30, true, paint); //不填充的扇形
        canvas.drawArc(left, top, right, bottom, 120, 30, false, paint); // 绘制不封口的弧形
    }

}

```

### drawPath
画心形
1. path有addXX以及XXto的方法。
2. addXX添加封闭图形(除了addArc比较特殊，style为stoke的时候是一个弧线)
3. xxTo添加线，其中forceMoveTo表示是否抬笔移动
4. close方法用于封闭图形
5. 如果Paint的style为fill会默认进行封闭
6. path有个填充类型FillType:EVEN_ODD、WINDING 、INVERSE_EVEN_ODD、INVERSE_WINDING。

```java
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    Paint paint = new Paint();
    paint.setStyle(Paint.Style.FILL);
    Path path = new Path(); // 初始化 Path 对象
    path.addArc(200, 200, 400, 400, -225, 225);
    path.arcTo(400, 200, 600, 400, -180, 225, false);
    path.lineTo(400, 542);
    canvas.drawPath(path, paint); // 绘制出 path 描述的图形（心形），大功告成
}

```

### drawText & drawBitmap

## View绘制的一些辅助

### shader使用

### canvas变换

## View绘制顺序

### draw绘制逻辑
view的绘制逻辑在draw(Canvas canvas)方法中

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
其中1、3、4、6是主要环节，2、5根据需要决定是否执行
1. 首先绘制背景 //drawBackground(canvas) private方法不能重写
2. canvas保存准备进行fading的实现 // if necessary
3. 绘制自身内容 //onDraw(canvas)
4. 绘制内部的子项  //dispatchDraw(canvas)
5. fading实现以及canvas的恢复 // if necessary 对应2
6. 绘制点缀，一些点缀比如前景、蒙版、滚动条 //onDrawForeground(canvas)

当然draw方法也可以重写，自定义，可以在super后添加一些特效，完全重写draw方法比较复杂了，因为这个逻辑框架是既定设计好的，最好只在内部一些流程上进行重写。

### setWillNotDraw-是否执行完整绘制流程

```java
/**
* If this view doesn't do any drawing on its own, set this flag to
* allow further optimizations. By default, this flag is not set on
* View, but could be set on some View subclasses such as ViewGroup.
*
* Typically, if you override {@link #onDraw(android.graphics.Canvas)}
* you should clear this flag.
*
* @param willNotDraw whether or not this View draw on its own
*/
public void setWillNotDraw(boolean willNotDraw) {
    setFlags(willNotDraw ? WILL_NOT_DRAW : 0, DRAW_MASK);
}
```
### onDraw重绘优化
`下述文字取自hencoder系列[https://hencoder.com/ui-1-5/]:`
有的时候，一段绘制代码写在不同的绘制方法中效果是一样的，这时你可以选一个自己喜欢或者习惯的绘制方法来重写。但有一个例外：如果绘制代码既可以写在 onDraw() 里，也可以写在其他绘制方法里，那么优先写在 onDraw() ，因为 Android 有相关的优化，可以在不需要重绘的时候自动跳过  onDraw() 的重复执行，以提升开发效率。享受这种优化的只有 onDraw() 一个方法。

### view的forground属性
6.0之后添加的属性，方便地实现蒙版效果。对应执行过程在onDrawForeground中触发。

## 如何查看某一个控件的所有属性

比如查看View的所有属性，去系统资源文件"data/res/values/attrs.xml".

View构造函数中有如下：
```java
final TypedArray a = context.obtainStyledAttributes(
        attrs, com.android.internal.R.styleable.View, defStyleAttr, defStyleRes);
```
com.android.internal.R.styleable.View对应attrs.xml中的如下内容段

```xml
<declare-styleable name="View">
    <attr name="id" format="reference" />
    <attr name="tag" format="string" />
    ....
    
    <!-- Defines the drawable to draw over the content. This can be used as an overlay.
        The foreground drawable participates in the padding of the content if the gravity
        is set to fill. -->
    <attr name="foreground" format="reference|color" />
    ...
</declare-styleable>
```
## 属性动画

1. ViewPropertyAnimator(只对view的一些属性进行变换，有局限性)
2. ObjectAnimator(可以是任意的属性，需要设置gettter/setter其中setter添加invalidate操作，根据需要结合setEvaluator进行数值的转换)
3. PropertyValuesHolder配合ObjectAnimator进行多属性配置
4. AnimatorSet对ObjectAnimator进行组合和编排。

## android中像素、英寸、dpi、dip、DisplayMetrics

```java
public static float dpToPixel(float dp) {
    DisplayMetrics metrics = Resources.getSystem().getDisplayMetrics();
    return dp * metrics.density;
}
```

## android中字体大小的含义

## string.xml使用格式化参数

```xml
    <string name="image_size">尺寸：%1$d x %2$d</string>
    <string name="image_size2" formatted="false">尺寸：%d x %d</string>
    <string name="image_size3">尺寸：%%d x %%d</string>
```

## 控件源码

## 布局源码

## centerInside和centerCrop等模式

## 傻瓜式修正尺寸-resolveSize

## onAttachedToWindow

## 触摸反馈

## ViewStub

## 扩展
1. 触摸按下
2. listview item按下后viewgroup滑动的实现
3. listview item长摁拖动排序的实现