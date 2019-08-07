## View绘制

## Paint

## Canvas

## Path

## drawColor
绘制一个纯色画布
```java
protected void onDraw(Canvas canvas) {
    super.onDraw(canvas);
    canvas.drawColor(Color.YELLOW);
}
```
## drawCircle
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
## drawRect
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

## drawPoint
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

## drawOval
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
## drawLine/drawLines
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

## drawRoundRect
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

## drawArc
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

## drawPath
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

## drawText & drawBitmap


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

## setWillNotDraw

## onDraw重绘优化

## 6.0之后view的forground设置

## centerInside和centerCrop等模式

## 傻瓜式修正尺寸-resolveSize

## onAttachedToWindow

## 触摸反馈

## ViewStub

## 扩展
1. 触摸按下
2. listview item按下后viewgroup滑动的实现
3. listview item长摁拖动排序的实现