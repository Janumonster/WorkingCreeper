# 1	View基础知识

> View的位置参数、MotionEvent和TouchSlop对象，VelocityTracker、GestureDetector和Scroller对象。



### 1.1	什么是View

View是所有控件的基类，是一种界面层控件的抽象，代表一个控件。ViewGroup就是包含一组View的控件，而被包含的子控件也可以有自己的ViewGroup和View，可以嵌套。

------



### 1.2	View的位置参数

**四个属性：top（左上角纵坐标）、left（左上角横坐标）、right（右下角横坐标）、bottom（右下角纵坐标）。**

> 这些坐标都是相对于父亲容器。

![](assets\View_location.png)

参数的获取：

- **Left = getLeft();** 
- **Right = getRight();**
- **Top = getTop();**
- **Bottom = getBottom();**

这些参数在onMeasure中是拿不到的，在测量阶段是无法得到的。

Android 3.0 开始，增加x，y，translationX和translationY。x，y对应左上角的坐标，这个坐标是相对于屏幕的；而translationX和translationY是View左上角相对于父容器的偏移量默认为0。

**x = left + translationX**

**y = top + translationY**

在view进行平移时，left和top不会改变，只会改变x、y和translationX和translationY的值。

所以在平移后使用getX()；获得的就是平移后左上角的坐标。

------



### 1.3	MotionEvent和TouchSlop

#### 1.3.1	MotionEvent

手指触摸屏幕发生的事件：

- **ACTION_DOWN**
- **ACTION_MOVE**
- **ACTION_UP**

通过MotionEvent对象可以获取点击事发生的位置的x和y。系统提供两种方法：getX/getY和getRawX/getRawY，

前者是相对于View的左上角的x和y，后者是相对于屏幕的x和y。

------



#### 1.3.2	TouchSlop

> TouchSlop是系统所能是别的最小滑动距离，小于这个距离系统将不认为用户在进行滑动操作。和设备相关，不同设备可能不同。

**获取方式：ViewConfiguration.get(getContent()).getScaledTouchSlop().**

------



### 1.4	VelocityTracker、GestureDetector和Scroller

#### 1.4.1	VelocityTracker

> 速度追踪

首先，在View的onTouchEvent方法中追踪当前点击事件的速度：

```java
	VelocityTracker velocitytracker=VelocityTracker.obtain();
	velocitytracker.addMovement(event);
```

获得当前速度

```java
	//这里设置的是时间，1000，也就是，每秒
	velocitytracker.computeCurrentVelocity(1000);
	int xVelocity = (int)velocitytracker.getXvelocity();
	int yVelocity = (int)velocitytracker.getYvelocity();
```

> 注意点：
>
> 1.获取速度前必须向计算速度，即computeCurrentVelocity必须在getXvelocity和getYvelocity之前调用。
>
> 2.这里的速度指一段时间内画过的像素数，左往右为正，右往左为负。

当不需要时调用clear方法并回收内存：

```java
	velocitytracker.clear();
	velocitytracker.recycle();
```

------



#### 1.4.2	GestureDetector

> 手势检测，用于辅助检测单击、双击、滑动、长按等行为。

首先创建一个GestureDetector对象，并实现OnGestureListener接口，根据需要我们还可以实现OnDoubleTapListener从而能够监听双击行为：

```java
	GestureDetector mGestureDetector = new GestureDetector(this);
	//解决长按屏幕后无法拖动的现象
	mGestureDetector.setIsLongpressEnabled(false);
```

接管View的onTouchEvent方法，在待监听的View的ontouchEvent方法中添加入下实现：

```java
	boolean consume = mGestureDetector.onTouchEvent(event);
	return consume;
```

选择形式实现OnGestureListener和OnDoubleTapListener，两个接口中的方法如下：

| 方法名               | 描述                                                         | 所属接口            |
| -------------------- | ------------------------------------------------------------ | ------------------- |
| onDown               | 手指触摸屏幕的一瞬间，由1个ACTION_DOWN触发                   | OnGestureListener   |
| onShowPress          | 手指触摸屏幕，**尚未发生松开或拖动**，由1个ACTION_DOWN触发   | OnGestureListener   |
| onSingleTapUp        | 手指（轻轻触摸屏幕后）松开，伴随着一个MotionEvent ACTION_UP而触发，这是单击行为 | OnGestureListener   |
| onScroll             | 按下屏幕并拖动，1 * ACTION_DOWN+n \* ACTION_MOVE，拖动行为   | OnGestureListener   |
| onLongPress          | 用户长久按着屏幕不放，即长按                                 | OnGestureListener   |
| onFIing              | 用户按下触摸屏、快速滑动后松开，1 * ACTION_DOWN + n * ACTION_MOVE + 1 * ACTION_UP触发，快速滑动行为 | OnGestureListener   |
| onDoubleTap          | 双击，两次单击组成，不能和onSingleTapConfirmed共存           | OnDoubleTapListener |
| onSingleTapConfirmed | 严格单击行为                                                 | OnDoubleTapListener |
| onDoubleTapEvent     | 发生双击行为，双击期间回调ACTION_DOWN、ACTION_MOVE、ACTION_UP | OnDoubleTapListener |

常用：onSingleTapUp、onFIing、onScroll、onLongPress、onDoubleTap

实际开发中也可以不用GestureDetector，可以再View的onTouchEvent中实现，若只监听滑动相关的，建议不适用GestureDetector，双击用GestureDetector。

使用方法：

在view中实例化GestureDetector，之后设置监听事件，最后在view的onTouchEvent中去，将点击事件传递给gesturedetector处理。

------



#### 1.4.3	Scroller

> 弹性滑动对象

```java
	Scroller scroller = new Scroller(mContext);

	//缓慢滑动到指定位置
	private void smoothScrollTo(int destX,int destY){
        int scrollX = getScrollX();
        int delta = destX - scrollX;
        //1000ms内滑动destX，效果就是缓慢滑动
        mScroller.startScroll(scrollX,0,delta,0,1000);
        invalidate();
	}

	@Override
	public void computeScroll(){
        if(mScroller.computeScrollOffset()){
            scrollTo(mScroller.getCurrX(),mScroller.getCurrY());
            postInvalidate();
        }
	}
```

------



## 2	View的滑动

通过三种方法实现：第一种是通过View本身提供的scrollTo和scrollBy方法实现滑动；第二种就是通过动画给View施加平移效果来实现滑动；第三种是通过改变View的LayoutParams是View重新布局从而实现滑动。

------



### 2.1	使用scrollTo和scrollBy

```java
    public void scrollTo(int x,int y){
        if(mScrollX!=x||mScrollY!=y){
            int oldX=mScrollX;
            int oldY=mScrollY;
            mScrollX=x;
            mScrollY=y;
            invalidateParentCaches();
            onScrollChanged(mScrollX,mScrollY,oldX,oldY);
            if(!awakenScrollBars()){
                postInvalifateOnAnimation();
            }
        }
    }

    public void scrollBy(int x,int y){
        scrollTo(mScrollX+x,mScrollY+y);
    }
```

scrollTo/scrollBy只能**改变View内容的位置**，而不能改变View的布局位置。

mScrollX/mScrollY单位为像素，mScrollX总值等于View的左边缘加上View内容的左边缘在水平方向上的距离。

以View内容的左上角为坐标原点，mScrollX为VIew左边缘位置，mScrollY为View上边缘位置。

------



### 2.2	使用动画

主要是操作View的translationX和translationY属性，既可以采用传统的View动画，也可以采用属性动画。

> 若采用属性动画，为了兼容3.0以下版本，需要开源动画库nineoldandroids(http://nineoldandroids.com)。

采用View动画来改变View的位置，系统不会认为该控件的位置发生改变，比如一个Button移动后点击是没有onClick事件的。只是影像而已。可以采用预设按钮在通过隐藏、可见操作进行正对性解决。

------



### 2.3	改变布局参数（仓鼠(￣▽￣)~*）

如果要把一个Button向右平移100px，通过Button的LayoutParams力的marginLeft参数的值增加100px即可；也可以在buttom旁边设置一个空View，通过改变空View的宽度改变Button的位置。

```java
    MarginLeftParams params = (MarginLeftParams)mButton.getLayoutParams();
    params.width += 100;
    params.leftMargin += 100;
    mButton.setLayoutParams(prarms);
    //或者mButton。setLayoutParams(params);
```

可以用第二种方法实现滑动效果。

------



### 2.4	滑动总结对比

| 方法              | 优点                                                         | 缺点                                                |
| ----------------- | ------------------------------------------------------------ | --------------------------------------------------- |
| scrollTo/scrollBy | 专门用于View的滑动，不影响内部元素的单击事件，操作简单       | 是能滑动View的内容，不能滑动View本身                |
| 动画              | 3.0以上：采用属性动画，无明显缺点(〃'▽'〃)； 使用没有交互的View和实现复杂的动画效果。 | 若使用View动画或者3.0以下，均不能改变View本身属性。 |
| 改变布局参数      | 适用于由用户交互                                             | 操作复杂                                            |



## 3	弹性滑动

> 将一次大的滑动分割成很多小的滑动并在一定时间内完成



#### 3.1	使用Scroller

与1.4.3中的Scroller一样，invalidate方法会导致重绘，在View的draw方法中又会去调用computeScroll方法，computeScroll方法在View中是一个空实现，因此需要实现。当View重绘后在draw中又会无调用computeScroll，而computeScroll又会去向Scroller获取当前的scrollX和scrollY；让后通过scrollTo实现滑动；接着又调用postInvalidate方法来进行第二次重绘，这一次重绘和第一次一样还会调用computeScroll方法，继续获取当前位置，一直重复上述过程直到滑动结束。

------



#### 3.2	通过动画

```java
    final int startX=0;
    final int deltaX=100;

    ValueAnimator animator = ValueAnimator.ofInt(0,1).setDuration(1000);
    animator.addUpdateListener(new AnimatorUpdateListener(){
        @Override
        public void onAnimationUpdate(ValueAnimiator animator){
            float fraction = animator.getAnimatedFraction();
            mButton.scrollTo(startX+(int)(deltaX*fraction),0);
        }  
    });
    animator.start();
```

滑动天然具有弹性效果，我们可以在动画的每一帧获得完成比例，然后根据比例计算出当前View要滑动的距离。针对View的内容，不针对View本身。通过改变一个百分比配合scrollTo完成View的滑动。采用这种方法还可以实现其他动画效果，可以再onAnimationUpdate中加上自己的其他操作。

------



#### 3.3	采用延时策略

通过发送一系列的延时消息从而达到渐近式效果。可以采用Handler或View的postDelayed方法，或者线程的sleep方法；



2018年4月6日14:32:04



## 4 View的工作原理

