# SpannableString

​	TextView是Android开发中的一个十分简单的控件，至少你第一个接触的HelloWorld就是用TextView显示的。一般来说TextView已经为我们提供了丰富的接口，可以设置挺多东西的，但这些操作都是对TextView的整体进行操作，无法进行随意组合。

	为了完成TextView的超进化，我们需要借助进化器--SpannableStringBuilder和SpannableString，这两个东西和StringBuilder以及String是类似的，但更丰富。

我们先来看看效果：

![Screenshot_1533871941](assets\Screenshot_1533871941.png)

这个Text是只使用一个TextView实现的哦。

看一下代码：

```java
//这里直接使用SpannableStringBuilder来，后面有SpannableString
SpannableStringBuilder spannableStringBuilder =new SpannableStringBuilder();
//往builder中加一个TEST
spannableStringBuilder.append("TEST");

//通过这个属性设置前景颜色，当然也有背景颜色
ForegroundColorSpan colorSpan = new ForegroundColorSpan(Color.BLUE);
//为Builder设置Span，第一个参数是span，第二个是字段开始位置，第三个参数是结束位置，最后一个是属性，之后有表
spannableStringBuilder.setSpan(colorSpan,0,spannableStringBuilder.length(), Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
//可以直接添加文字
spannableStringBuilder.append("TEST");
//也可以使用SpannableString添加，对SpannableString的span设置同样也可以加到builder中。
SpannableString spannableString = new SpannableString("fuxxk");
ForegroundColorSpan foregroundColorSpan = new ForegroundColorSpan(Color.RED);
spannableString.setSpan(foregroundColorSpan,0,spannableString.length(),Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);

//使用图片替换文字位置，就是上面呢个圆形图标
ImageSpan imageSpan = new ImageSpan(getContext(),R.mipmap.ic_launcher);
//setSpan可以重复使用很多次
spannableStringBuilder.append(spannableString);
spannableStringBuilder.setSpan(imageSpan,3,5,Spanned.SPAN_EXCLUSIVE_EXCLUSIVE);
textView.setText(spannableStringBuilder);
```

用法就这么简单，接下来介绍一些有哪些方法可以用来修改textview的样式：

#### 主要的方法

`SpannableStringBuilder`和`SpannableString`主要通过使用`setSpan(Object what, int start, int end, int flags)`改变文本样式。
 对应的参数：

- start： 指定Span的开始位置
- end：  指定Span的结束位置，并不包括这个位置。
- flags：取值有如下四个
-  `Spannable. SPAN_INCLUSIVE_EXCLUSIVE`：前面包括，后面不包括，即在文本前插入新的文本会应用该样式，而在文本后插入新文本不会应用该样式
-  `Spannable. SPAN_INCLUSIVE_INCLUSIVE`：前面包括，后面包括，即在文本前插入新的文本会应用该样式，而在文本后插入新文本也会应用该样式
-  `Spannable. SPAN_EXCLUSIVE_EXCLUSIVE`：前面不包括，后面不包括
-  `Spannable. SPAN_EXCLUSIVE_INCLUSIVE`：前面不包括，后面包括
- what： 对应的各种Span，不同的Span对应不同的样式。已知的可用类有：
-  `BackgroundColorSpan` : 文本背景色
-  `ForegroundColorSpan` : 文本颜色
-  `MaskFilterSpan` : 修饰效果，如模糊(BlurMaskFilter)浮雕
-  `RasterizerSpan` : 光栅效果
-  `StrikethroughSpan` : 删除线
-  `SuggestionSpan` : 相当于占位符
-  `UnderlineSpan` : 下划线
-  `AbsoluteSizeSpan` : 文本字体（绝对大小）
-  `DynamicDrawableSpan` : 设置图片，基于文本基线或底部对齐。
-  `ImageSpan` : 图片
-  `RelativeSizeSpan` : 相对大小（文本字体）
-  `ScaleXSpan` : 基于x轴缩放
-  `StyleSpan` : 字体样式：粗体、斜体等
-  `SubscriptSpan` : 下标（数学公式会用到）
-  `SuperscriptSpan` : 上标（数学公式会用到）
-  `TextAppearanceSpan` : 文本外貌（包括字体、大小、样式和颜色）
-  `TypefaceSpan` : 文本字体
-  `URLSpan` : 文本超链接
-  `ClickableSpan` : 点击事件

 

 

 

 

 