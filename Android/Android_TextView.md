# Android_TextView

- **id：**为TextView设置一个组件id，根据id，我们可以在Java代码中通过findViewById()的方法获取到该对象，然后进行相关属性的设置，又或者使用RelativeLayout时，参考组件用的也是id！
- **layout_width：**组件的宽度，一般写：**wrap_content**或者**match_parent(fill_parent)**，前者是控件显示的内容多大，控件就多大，而后者会填满该控件所在的父容器；当然也可以设置成特定的大小，比如我这里为了显示效果，设置成了200dp。
- **layout_height：**组件的宽度，内容同上。
- **gravity：**设置控件中内容的对齐方向，TextView中是文字，ImageView中是图片等等。
- **text：**设置显示的文本内容，一般我们是把字符串写到string.xml文件中，然后通过@String/xxx取得对应的字符串内容的，这里为了方便我直接就写到""里，不建议这样写！！！
- **textColor：**设置字体颜色，同上，通过colors.xml资源来引用，别直接这样写！
- **textStyle：**设置字体风格，三个可选值：**normal**(无效果)，**bold**(加粗)，**italic**(斜体)
- **textSize：**字体大小，单位一般是用sp！
- **background：**控件的背景颜色，可以理解为填充整个控件的颜色，可以是图片哦！



### 带阴影的TextView

涉及到的几个属性：

- **android:shadowColor:**设置阴影颜色,需要与shadowRadius一起使用哦!
- **android:shadowRadius:**设置阴影的模糊程度,设为0.1就变成字体颜色了,建议使用3.0
- **android:shadowDx:**设置阴影在水平方向的偏移,就是水平方向阴影开始的横坐标位置
- **android:shadowDy:**设置阴影在竖直方向的偏移,就是竖直方向阴影开始的纵坐标位置



###  自动换行

> 这个已经废弃，不建议使用

自动换行通过 **android:singleLine** 设置，默认为 false。

如需要自动换行，可以用：

```
android:singleLine = "false"
```

如果要在一行显示完，不换行，可以用：

```
android:singleLine = "true"
```



### 使用autoLink属性识别链接类型

当文字中出现了URL，E-Mail，电话号码，地图的时候，我们可以通过设置autoLink属性；当我们点击 文字中对应部分的文字，即可跳转至某默认APP，比如一串号码，点击后跳转至拨号界面！



### 图片(drawableXxx)的TextView：

设置图片的核心其实就是:**drawableXxx**;可以设置四个方向的图片:**drawableTop**(上),**drawableButtom**(下),**drawableLeft**(左),**drawableRight**(右) 另外,你也可以使用**drawablePadding**来设置图片与文字间的间距！