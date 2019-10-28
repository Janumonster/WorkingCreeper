# Android_Animation整理

Android中提供了两种animation，一种是View Animation（视图动画）和Property Animation（属性动画），这里先学习视图动画。

View Animation包括两种，补间动画（`Tween Animation`）和帧(`Frame Animation`)动画。

### Tween Animation

所谓补间动画就是给定起始和最终状态，有系统生成中间动画。

Tween Animation有以下四种属性：

| 名称       | 标签      | 子类               | 效果             |
| ---------- | --------- | ------------------ | ---------------- |
| 平移动画   | translate | TranslateAnimation | 移动View         |
| 缩放动画   | scale     | ScaleAnimation     | 放大或缩小View   |
| 旋转动画   | rotate    | RotateAnimation    | 旋转View         |
| 透明度动画 | alpha     | AlphaAnimation     | 改变View的透明度 |

#### 创建过程

通过创建动态XML文件，路径res/anim/filename.xml

```java
//res/anim/filename.xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="true"
    android:duration="300"
    android:fillAfter="true"
    android:repeatMode="reverse"
    >
    <!--属性
     shareInterpolator:表示集合中的动画是否和集合共享同一个插值器。如果集合不指定插值器，那么子动画就需要单独制定所需的插值器或者使用默认值
     duration:动画持续时间
     fillAfater:动画结束以后View是否停留在结束位置，true表示View停留在结束位置，false则不停留
     android:repeatMode  动画重复的Mode
     -->

    <alpha
        android:fromAlpha="0.1"
        android:toAlpha="1"></alpha>
    <!--透明度动画
         fromAlpha表示透明度起始值,
         toAlpha表示透明度结束值-->


    <scale
        android:fromXScale="0.5"
        android:fromYScale="0.5"
        android:pivotX="20"
        android:pivotY="20"
        android:toXScale="1.2"
        android:toYScale="1"
        ></scale>
    <!--缩放动画
        android:fromXScale-水平方向缩放的起始值
        android:toXScale-水平方向缩放结束值
        android:fromYScale-竖直方向缩放的起始值
        android:toScale-竖直方向缩放结束值
        android:pivotX 缩放轴点的x坐标
        android:pivotY缩放轴点的y坐标-->

    <translate
        android:fromXDelta="0"
        android:fromYDelta="0"
        android:toXDelta="100"
        android:toYDelta="100"></translate>
    <!-- 平移动画
        android:fromXDelta-表示x的起始值
        android:toXDelta-表示x的结束值
        android:fromYDelta-表示y的起始值
        android:toYDelta-表示y的结束值
    -->

    <rotate
        android:fromDegrees="0"
        android:pivotX="10"
        android:pivotY="10"
        android:toDegrees="180"></rotate>
    <!--旋转动画
        android:fromXDelta-旋转开始的角度
        android:toDegrees="180"-旋转结束的角度
        pivotX 缩放轴点的x坐标
        pivotY缩放轴点的y坐标
     	默认情况下轴点是View的中心点-->  
</set>
```



**差值器**

插值器定义了动画变化的速率，提供不同的函数定义变化值相对于时间的变化规则，可以定义各种各样的非线性变化函数，比如加速、减速等。

常见的差值器有：

| Interpolator对象                 | 资源id                                            | 功能作用                               |
| -------------------------------- | ------------------------------------------------- | -------------------------------------- |
| AccelerateDecelerateInterpolator | @android:anim/accelerate_ decelerate_interpolator | 先加速再减速                           |
| AccelerateInterpolator           | @android:anim/accelerate_ interpolator            | 加速                                   |
| AnticipateInterpolator           | @android:anim/anticipate_ interpolator            | 先回退一小步然后加速前进               |
| AnticipateOvershootInterpolator  | @android:anim/anticipate_ overshoot_interpolator  | 在上一个基础上超出终点一小步再回到终点 |
| BounceInterpolator               | @android:anim/bounce_ interpolator                | 最后阶段弹球效果                       |
| CycleInterpolator                | @android:anim/cycle_interpolator                  | 周期运动                               |
| DecelerateInterpolator           | @android:anim/decelerate_ interpolator            | 减速                                   |
| LinearInterpolator               | @android:anim/linear_interpolator                 | 匀速                                   |
| OvershootInterpolator            | @android:anim/overshoot_ interpolator             | 快速到达终点并超出一小步最后回到终点   |

```
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:shareInterpolator="true"
    android:interpolator="@android:anim/accelerate_interpolator"
    //使用加速插值器
```

设置动画：

```java
	img=findViewById(R.id.img);
	Animation animation=AnimationUtils.loadAnimation(MainActivity.this,R.anim.view_animation);
	img.startAnimation(animation);
```



也可以直接使用java代码实现，说白了就是new一个Animation对象。并设置一系列属性：

```java
	AlphaAnimation alphaAnimation = new AlphaAnimation(0, 1);
    alphaAnimation.setDuration(300);
    mImg.startAnimation(alphaAnimation);
    //创建一个透明度动画，将img透明度在300ms内由0变成1

    TranslateAnimation translateAnimation = new TranslateAnimation(0,100,0,100);
    mImg.startAnimation(translateAnimation);
    ...
```



## FrameAnimation

帧动画是顺序播放一组预先定义好的图片，类似于电影播放。和T问Animation不一样的是，系统提供了另外一个类AnimationDrawable来使用帧动画。帧动画的使用很简单，通过XML来定义一个Animation

```java
//res/drawable/frame_animation.xml
<?xml version="1.0" encoding="utf-8"?>
<animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false">

    <item
        android:drawable="@drawable/bga_refresh_loading01"
        android:duration="100" />
    <item
        android:drawable="@drawable/bga_refresh_loading02"
        android:duration="100" />
    <item
        android:drawable="@drawable/bga_refresh_loading03"
        android:duration="100" />

</animation-list>
```

然后将Drawable作为View的背景并通过AndroidDrawable来播放动画

```java
	mLoading = (ImageView) findViewById(R.id.frameImg);
    mLoading.setBackgroundResource(R.drawable.refresh_loading);
    AnimationDrawable drawable = (AnimationDrawable) mLoading.getBackground();
    drawable.start();
```

注意，帧动画的使用虽然很简单，但是比较容易引起OOM，所以在使用帧动画时应尽量避免使用尺寸较大的图片。



## View Animation的特殊使用场景

#### 通过LayoutAnimation实现ViewGroup子元素出场动画

LayoutAnimation作用于ViewGroup，为ViewGroup指定一个动画，当子元素加载时，都会具有这种动画效果，要经过如下步骤：

1、定义LayoutAnimation

```java
// res/anim/anim_layout
   <?xml version="1.0" encoding="utf-8"?>
   <layoutAnimation xmlns:android="http://schemas.android.com/apk/res/android"
       android:delay="0.5"//表示子元素开始动画的时间延迟，比如子元素入场动画的时间周期为300ms,那么0.5表示每个元素都要延迟150ms才能播放入场动画。比如第一个元素延迟150ms，第二个延迟300ms,以此类推。
       android:animation="@anim/anim_item"
           //表示子元素动画的顺序，有三种选项：normal、reverse和random，分别代表子元素出场按顺序显示、逆向显示和随机显示。
       android:animationOrder="normal">
   </layoutAnimation>
```

2、指定元素具体入场动画

```java
// res/anim/anim_item.xml
    <?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="300"
    android:interpolator="@android:anim/decelerate_interpolator"
    android:shareInterpolator="true">

    <alpha
        android:fromAlpha="0.0"
        android:toAlpha="1.0" />
    <scale
        android:fromYScale="0.5"
        android:toYScale="1.2" />

</set>
```

3、为ViewGroup指定android：LayoutAnimation属性。

```java
	<ListView
        android:id="@+id/lv"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layoutAnimation="@anim/anim_layout" />
            
     //在布局中指定动画，只能在初次加载时生效，后面使用setVisibility不会有动画效果
```

还可以：

```java
	mLv = (ListView) findViewById(R.id.lv);
    Animation animation = AnimationUtils.loadAnimation(this, R.anim.anim_item);
    LayoutAnimationController controller = new LayoutAnimationController(animation);
    controller.setDelay(0.5f);
    controller.setOrder(LayoutAnimationController.ORDER_NORMAL);
    mLv.setLayoutAnimation(controller);

	//或者
	LayoutAnimationController controller=AnimationUtils.loadlayoutanimation(Mainactivity.this,R.anim.anim_layout_animation);
	recyclerview.setLayoutAnimation(controller);
```



#### 实现Activity的切换动画

主要用到了overridePendingTransition(int enterAnim ,int exitAnim)这个方法，这个方法必须在startActivity或者finish之后被调用才能实现。

```java
//res/anim/enter_anim
  <?xml version="1.0" encoding="utf-8"?>
  <set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@android:anim/decelerate_interpolator">
    <scale
        android:duration="@android:integer/config_mediumAnimTime"
        android:fromXScale="2.0"
        android:fromYScale="2.0"
        android:pivotX="50%p"
        android:pivotY="50%p"
        android:toXScale="1.0"
        android:toYScale="1.0" />
</set>

```

```java
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
  android:interpolator="@android:anim/decelerate_interpolator"
  android:zAdjustment="top">
  <scale
      android:duration="@android:integer/config_mediumAnimTime"
      android:fromXScale="1.0"
      android:fromYScale="1.0"
      android:pivotX="50%p"
      android:pivotY="50%p"
      android:toXScale=".5"
      android:toYScale=".5" />
  <alpha
      android:duration="@android:integer/config_mediumAnimTime"
      android:fromAlpha="1.0"
      android:toAlpha="0" />
</set>
```

当启动一个Activity时,可以按如下方式添加自定义切换效果

```java
    Intent intent = new Intent(this,SecondActivity.class); startActivity(intent); 				overridePendingTransition(R.anim.enter_anim,R.anim.exit_anim);
```

当Activity退出时，也可以为其指定自己的切换效果，如下所示

```java
     @Override
      public void finish() {
        super.finish();
        overridePendingTransition(R.anim.enter_anim,R.anim.exit_anim);
      }
```

注意overridePendingTransition这个方法必须位于startActivity或者finish的后面，否则动画效果无效。


