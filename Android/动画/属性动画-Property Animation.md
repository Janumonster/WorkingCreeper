# 属性动画-Property Animator 

## 1 视图动画

- 逐帧动画（`Frame Animation`）
- 补间动画（ `Tween animation` ）  

只能对View进行操作，其中有些甚至只是视觉效果并没有改变View本身的任何东西。

而且有一点就是视图动画，顾名思义就是对视图进行操作，他的操作对象是VIew，对于非View的东西就无法操作了，比如说改变某个空间的宽度。你可能会说可以用`ScaleAnimation`，但并不是所有的都可用，有时候我们需要一些精确地值得时候就无法使用`ScaleAnimation`了。而且我们如果使用`ScaleAnimation`视觉上我们是改变了控件的大小，但实际上我们并没有改变控件的大小。而且对于有监听事件的控件来说，动画结束后监听事件的位置没有改变。

> 你有一个左上角的button，现在通过动画将它移动到右下角，之后fillafter为true（该属性为true时，动画结束后会停留在结束的位置），你会发现你点击右下角不会有监听事件，以为这时候监听事件还在左上角。

而且，Animation的动画效果太少了，只有平移，旋转，缩放和透明度。再组合也就这几种。



## 2 属性动画(PropertyAnimator)

> 为了让动画更丰富，再Android 3.0	API 11	增加了新的动画`PropertyAnimator` 。
>
> 默认时长：300ms			10ms/帧

这个可就牛逼了，它的作用范围是：**任意对象**，对就是任意对象，设置没有对象动画也可以执行。

与其说是动画，不如说是一个差值器和估值器，这个动画可以提供一个“值“到另一个”值“的差值变化过程。所以可以独立存在。

我们通过不断的获取这个值，然后不断去刷新你的View来达到动画的效果。

> p.s. 如果想全兼容的话可以使用`nineoldandroids`，这个库，具体可自行google；



### 2.1 差值器、估值器

**差值器**，英文名：`TimeInterpolator`，全名：时间差值器。

它会根据你给的时长和两个值之间的差值进行计算，根据时间流逝的百分比计算出当前属性的百分比。就是获你得从他这里获得你现在改变的百分比。

系统的差值器有：匀速：`LinearInterpolator`、两头慢中间快 `AccelerateDecelerateInterpolator` 、减速`DecelerateInterpolator`，等等 

**估值器**，英文名：`TypeEvaluator`，全名：类型估值器。

这个就是承接差值器的东西，通过差值器计算的百分比，被用于计算目前百分比的具体的值。根据不同需求有不同的估值器，甚至你可以自定义你的估值器。

- `IntEvaluator`： 针对整型
- `FloatEvalusstor` ：针对浮点型
- `ArbgEvaluator` ：针对Color



## 3 ValueAnimator&ObjectAnimator

- `ValueAnimator`
- `ObjectAnimator`

这两个是属性动画最主要的类，其中ValueAnimator是核心，ObjectAnimator是继承自ValueAnimator的。

### 3.1 ValueAnimator(核心)

原理：通过不断更新属性的值，达到动画的目的。

三个重要的方法：

```java
	ValueAnimator.ofInt(int ... value);
	ValueAnimator.ofFloat(float ...value);
	/**
	 * 上面两个比较简单就是传入一些值，如果多余两个就一个一个变下去
	 * 根据类型自动使用系统预置的估值器
	 */

	ValueAnimator.ofObject(int ..value);//这个相对较麻烦，需要自己写估值器
```

和视图动画一样，属性动画也可以使用XML文件或者直接Java代码编写。建议Java编写。

话不多说，直接上例子：

```java
public class MainActivity extends AppCompatActivity {

    private Button button;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        button=findViewById(R.id.btn_anim);
        //两个值分别是button的宽度，和最终宽度
        final ValueAnimator animator = ValueAnimator.ofInt(button.getLayoutParams().width,500);
        //这里要注意，用button.getwidth(),获得的不是button的宽度，我这里设置为100dp，不要使用warp_content容易出错，除非迫不得已，是最大宽度，要小心
        
        //接下来和视图动画类似，设置时长和动画属性
        animator.setDuration(2000);
        //传入这个参数就是说无限循环
        animator.setRepeatCount(ValueAnimator.INFINITE);
        //循环模式，可以有REVERSE，或者RESTART。
        animator.setRepeatMode(ValueAnimator.REVERSE);
        
        //这个数对值的变化的监听
        animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
            @Override
            public void onAnimationUpdate(ValueAnimator valueAnimator) {
                //获取变化的新的值
                int currentValue = (int) valueAnimator.getAnimatedValue();
                //手动设置button的宽度
                button.getLayoutParams().width = currentValue;
                //刷新布局
                button.requestLayout();
            }
        });
        
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                //开始动画
                animator.start();
            }
        });
    }
}

```

效果如图：（布局啥的就不说了）

![](assets\button_width_sample.gif)

ValueAnimator.ofFloat(float ...value);的效果和int是一样的，所以就不再重复解释了，主要的区别就在于差值器是float型的，按理说变化会更加柔和。

那么这个效果用XML怎么实现，em。。。。实现不了，是说单纯的使用我们上面的这些事实现不了的，必须要有ObjectAnimator参与，不然完全不知道你要改变什么属性就无法实现动画效果。很多博客上都没有提到：

```java
<?xml version="1.0" encoding="utf-8"?>
<animator xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="2000"
    android:valueFrom="100"
    android:valueTo="500"
    android:valueType="intType"
    android:repeatCount="infinite"
    android:repeatMode="reverse">

</animator>
```

如果只是用animator标签是不行的！！！！！

```java
 final Animator animator = AnimatorInflater.loadAnimator(this,R.animator.button_width);
        animator.setTarget(button);
        animmtor.start();
```

这样做不会有任何作用，可能是我太蠢，但这也是个提醒，不要和我一样蠢。所以能不用XML就不用吧。但如果是组合动画的话在考虑用xml吧。接下来我就不说xml了，真烦！

ValueAnimator.ofObject(int ...value);这个就比较麻烦了。这是作用于对象的，可以同时操作多个属性，因为要同时操作多个属性，那就只能由我们自己来写估值器了。

接下来我就来实现一个自定义控件：

![](assets\file.png)

说明一下结构，MainActivity这个没啥好说的，Point是一个类，里面包含两个属性x，y，用于确定这个point的坐标。PointEvaluator就是估值器，PointView是自定义View。我们再具体看一下内容：

```java
//Point.java

public class Point {

    private float x;
    private float y;

    Point(float x,float y){
        this.x=x;
        this.y=y;
    }

    public float getX() {
        return x;
    }

    public float getY() {
        return y;
    }
}

```

很简单就是一个类，保存两个属性。并有两个get用于获取x/y。之后再看看估值器：

```java
//PointEvaluator
public class PointEvaluator implements TypeEvaluator {

    @Override
    public Object evaluate(float v, Object startObject, Object endObject) {
		//这里的v就是差值器计算出的改变的百分比。
        Point startPoint = (Point) startObject;
        Point endPoint = (Point) endObject;
		//根据百分比计算出变化后x、y的值。
        float x = startPoint.getX()+v*(endPoint.getX()-startPoint.getX());
        float y = startPoint.getY()+v*(endPoint.getY()-startPoint.getY());
        
        return new Point(x,y);
    }
}
```

准备工作都做好了就可以开始写我们的自定义view了。

```java
//PointView.java
import android.animation.ValueAnimator;
import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Color;
import android.graphics.Paint;
import android.util.AttributeSet;
import android.view.View;

public class PointView extends View {

	//设置Point的半径
    public static final float RADIUS = 70f;
    //目前Point的坐标
    private Point currentPoint;
    //画笔
    private Paint mPaint;

    public PointView(Context context, AttributeSet attrs) {
        super(context,attrs);
        //具体参考Paint的教程
        mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);
        //小绿点
        mPaint.setColor(Color.GREEN);
    }

    @Override
    protected void onDraw(Canvas canvas){
    	//首次Draw要确定初始点后结束点
        if (currentPoint == null){
            currentPoint =new Point(RADIUS,RADIUS);
            
            float x=currentPoint.getX();
            float y=currentPoint.getY();

            canvas.drawCircle(x,y,RADIUS,mPaint);
           
            Point startPoint = new Point(RADIUS,RADIUS);
            Point endPoint = new Point(700,1000);
			//估值器在这里使用
            final ValueAnimator animator = ValueAnimator.ofObject(new PointEvaluator(),startPoint,endPoint);
            animator.setDuration(5000);
            //无限循环
            animator.setRepeatCount(ValueAnimator.INFINITE);
            //往复
            animator.setRepeatMode(ValueAnimator.REVERSE);
            //这里也可以设置差值器，注意如果是使用系统预置的差值器或者估值器，都要new出来！！
            //nimator.setInterpolator(new AccelerateInterpolator());
				

            animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
                @Override
                public void onAnimationUpdate(ValueAnimator valueAnimator) {
                    currentPoint= (Point) animator.getAnimatedValue();
                    //每次获得最新位置并刷新
                    invalidate();
                }
            });
            animator.start();
        }else {
            //不是第一次就直接画点就行了，监听函数仍是有效的，currentPoint会不断更新。
            float x=currentPoint.getX();
            float y=currentPoint.getY();
            canvas.drawCircle(x,y,RADIUS,mPaint);
        }
    }
}

```

之后再布局文件中加入，铺满全屏，然后啥属性也不要管。MainActivity也不需要啥代码。直接运行！

![](assets\pointView.gif)

为什么会黑一下，这个是只做gif出现的问题，在机器上正常运行。好了，到这边差不多ValueAnimator就这样了。



### 3.2 ObjectAnimator

> `ObjectAnimator` 继承自`ValueAnimator`所以后者有的前者也有。

如果说`ValueAnimator`是手动赋值，那么`ObjectAnimator`就是自动赋值的。

直接上例子：

```java
public class MainActivity extends AppCompatActivity {

    private Button button;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        button=findViewById(R.id.btn_anim);

        ObjectAnimator animator = ObjectAnimator.ofFloat(button,"alpha",1f,0f);
        animator.setDuration(5000);
        animator.setRepeatCount(ValueAnimator.INFINITE);
        animator.setRepeatMode(ValueAnimator.REVERSE);

        animator.start();
    }
}

```

设置一个透明度变化的例子，效果如下：

![](assets\object_alpha.gif)

我这里为什么不用“width”属性呢，因为button的width属性是设置控件的最大宽度（height也是一个道理），不会改变button的宽度，除非你设置wrap_content，或许还可能实现，但不推荐这样做。

有一些属性是view都会有的：

| 属性         | 作用                       | 数值类型 |
| ------------ | -------------------------- | -------- |
| Alpha        | 控制View的透明度           | float    |
| TranslationX | 控制X方向的位移            | float    |
| TranslationY | 控制Y方向的位移            | float    |
| ScaleX       | 控制X方向的缩放倍数        | float    |
| ScaleY       | 控制Y方向的缩放倍数        | float    |
| Rotation     | 控制屏幕方向为轴的旋转角度 | float    |
| RotationX    | 控制以X轴为轴的旋转角度    | float    |
| RotationY    | 控制以Y轴为轴的旋转角度    | float    |

这些是基本动画，ofFloat()的第二个属性可以为任意属性，Object为我们做的就是去找该属性的get和set，如果没有初始值，找get，改变值，找set，也就是说Object的属性必须有get和set方法。至少得有set方法！！但如果没有怎么办？

解决方法：

1. 通过继承原始类，为他加上get、set方法。当然get、set中的具体逻辑要我们自己实现。

2. 通过包装该objec，通过包装类的get、set去改变具体的值。

   > 比如说，我写了一个包装类去包装一个button，然后在包装类中有setWidth和getWidth，那么我就可以通过包装类来改变button的宽度了，当然包装类中依然要获得button的getLayoutParams()来改变button的宽度。

ofInt()的具体操作和ofFloat()差不多就不多说了，然后object也和ValueAnimator相差无几

```java
//就是在Evaluator前面加入了objec和属性名称
ObjectAnimator anim = ObjectAnimator.ofObject(myView, "color", new ColorEvaluator(),
                "#0000FF", "#FF0000");
//这步操作之后就不需要啥操作了，系统自动根据你给的属性改值。
//如果没有这个属性，可能会崩溃，也可能没有动画
```

然后这个是可以用XML实现的：

```java
<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"  
    android:valueFrom="1"  
    android:valueTo="0"  
    android:valueType="floatType"  
    android:propertyName="alpha"
/>
```

然后在java中load：

```java
Animator animator = AnimatorInflater.loadAnimator(this, R.animator.view_animation);  
animator.setTarget(button);  
animator.start();  
```





### 3.3 组合动画AnimatorSet

单个动画很难做出惊人的效果，所以就要组合了，如果前面的那些动画都搞定了，那这个就简单了。

```java
AnimatorSet.play(Animator anim)   ：播放当前动画
AnimatorSet.after(long delay)   ：将现有动画延迟x毫秒后执行
AnimatorSet.with(Animator anim)   ：将现有动画和传入的动画同时执行
AnimatorSet.after(Animator anim)   ：将现有动画插入到传入的动画之后执行
AnimatorSet.before(Animator anim) ：  将现有动画插入到传入的动画之前执行
```

也可以在xml中使用set标签设置

```java

<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:ordering="sequentially">
    <!--ordering，指定该set标签中的动画顺序执行-->
 
    <!--同时执行-->
    <set android:ordering="together">
        
        <objectAnimator
            android:duration="2000"
            android:propertyName="translationX"
            android:valueFrom="0"
            android:valueTo="300"
            android:valueType="floatType"/>
        
        <objectAnimator
            android:duration="2000"
            android:propertyName="rotation"
            android:valueFrom="0"
            android:valueTo="300"
            android:valueType="floatType"/>

    </set>

    <!--顺序执行-->
    <set android:ordering="sequentially">

        <objectAnimator
            android:duration="2000"
            android:propertyName="translationX"
            android:valueFrom="0"
            android:valueTo="300"
            android:valueType="floatType"/>

        <objectAnimator
            android:duration="2000"
            android:propertyName="rotation"
            android:valueFrom="0"
            android:valueTo="300"
            android:valueType="floatType"/>

    </set>

</set>
```

load方式和之前的方式一样，这里就不再贴代码了。

还有一种简便写法：

```java
button.animate().alpha(0f).x(500).y(500).setDuration(5000).setInterpolator(new DecelerateInterpolator());
//让button变透明，再移动到（500,500）
```

对于动画来说，我个人觉得自动赋值的灵活度不高，还是Value的灵活度比较高，特别是配合Object来使用，自定义Evaluator。如果想要制作酷炫的动画，可以组合使用。



### 3.4 监听事件

这里直接看代码：

```java
animator.addListener(new Animator.AnimatorListener() {
            @Override
            public void onAnimationStart(Animator animator) {
                
            }

            @Override
            public void onAnimationEnd(Animator animator) {

            }

            @Override
            public void onAnimationCancel(Animator animator) {

            }

            @Override
            public void onAnimationRepeat(Animator animator) {

            }
        });
```

系统为我们提供了四个方法，看方法名就知道是干嘛的了。但每次都要去写这四个方法有时候是很麻烦的，所以系统还提供了一个适配器：

```java
animator.addListener(new AnimatorListenerAdapter() {
            @Override
            public void onAnimationCancel(Animator animation) {
                super.onAnimationCancel(animation);
            }
        });
```

这样我们就可以只使用其中的一些方法而无视其他方法了。



## 4 总结

属性动画，就是通过不断地修改属性的值来达到动画的目的，这些Animator多实用就会熟悉，或者可以自己歇一歇自定义控件来玩一玩。

| 类                  | 作用                                              |
| ------------------- | ------------------------------------------------- |
| ValueAnimator       | 先改变值，在根据值手动修改属性值，从而实现动画。  |
| ObjectAnimator      | 先改变值，再根据属性名，寻找set，自动修改属性值。 |
| AnimatorSet         | 实现组合动画                                      |
| ViewPropertAnimator | 采用对对象操作实现动画                            |
| addListener()       | 对动画的监听                                      |

