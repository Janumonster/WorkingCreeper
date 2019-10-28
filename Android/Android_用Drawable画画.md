# Android_用Drawable画画



## shape

话不多说看代码：

```java
<shape
    xmlns:android="http://schemas.android.com/apk/res/android"
    //默认为rectangle
    android:shape=["rectangle"|"oval"|"line"|"ring"]>
    <corners //当shape="rectangle"时使用
        //半径，会被后面的单个半径属性覆盖
        android:radius="integer"
        android:topLeftRadius="integer"
        android:topRightRadius="integer"
        android:bottomLegtRadius="integer"
        android:bottomRightRadius="integer"/>
    <gradient   //渐变
        android:angle="integer"
        android:centerX="integer"
        android:centerY="integer"
        android:centerColor="color"
        android:endColor="color"
        android:gradientRadius="integer"
        android:startColor="color"
        android:type=["linear"|"radial"|"sweep"]
        android:useCenter=["true"|"false"]/>
    <padding
        android:left="integer"
        android:top="integer"
        android:right="integer"
        android:bottom="integer"/>
    <size //指定大小，一般用在ImageView配合scaleType属性使用
        android:width="integer"
        android:height="integer"/>
    <solid  //填充颜色
        android:color="color"/>
    <stroke //指定边框
        android:width="integer"
        android:color="color"
        //虚线宽度
        android:dashWidth="integer"
        //虚线间隔宽度
        android:dashGap="integer"/>
</shape>
```

**solid**: 设置形状填充的颜色，只有android:color一个属性

**padding**: 设置内容与形状边界的内间距，可分别设置左右上下的距离

**gradient**: 设置形状的渐变颜色，可以是线性渐变、辐射渐变、扫描性渐变

```java
android:type 渐变的类型
    linear 线性渐变，默认的渐变类型
    radial 放射渐变，设置该项时，android:gradientRadius也必须设置
    sweep 扫描性渐变
android:startColor 渐变开始的颜色
android:endColor 渐变结束的颜色
android:centerColor 渐变中间的颜色
android:angle 渐变的角度，线性渐变时才有效，必须是45的倍数，0表示从左到右，90表示从下到上
android:centerX 渐变中心的相对X坐标，放射渐变时才有效，在0.0到1.0之间，默认为0.5，表示在正中间
android:centerY 渐变中心的相对X坐标，放射渐变时才有效，在0.0到1.0之间，默认为0.5，表示在正中间
android:gradientRadius 渐变的半径，只有渐变类型为radial时才使用
android:useLevel 如果为true，则可在LevelListDrawable中使用
```

**corners**: 设置圆角，只适用于rectangle类型，可分别设置四个角不同半径的圆角，当设置的圆角半径很大时，比如200dp，就可变成弧形边了

```
android:radius 圆角半径，会被下面每个特定的圆角属性重写
android:topLeftRadius 左上角的半径
android:topRightRadius 右上角的半径
android:bottomLeftRadius 左下角的半径
android:bottomRightRadius 右下角的半径
```

**stroke**: 设置描边，可描成实线或虚线。

```
android:color 描边的颜色
android:width 描边的宽度
android:dashWidth 设置虚线时的横线长度
android:dashGap 设置虚线时的横线之间的距离
```

2018-5-9 20:49:12 整理，未完待续。。。