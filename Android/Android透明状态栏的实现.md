# Android透明状态栏的实现

> 之前有简单写过透明状态栏的实现，但好像过期了，这里重新实现一下。

## 先说一句

​	这个风潮好像也是苹果带出来的，很多UI设计Android都在向IOS靠近，不过现在是两者在互相借鉴。这个透明状态栏也是这其中的产物。对于透明状态栏更多的人喜欢称之为“沉浸式状态栏”，主要是颜色会随着app的顶部的颜色而改变。其实也就是把状态栏设置为透明，然后让布局延伸到状态栏就可以了。之前也做过总结，但比较混乱，这里在仔细梳理一遍，并不断完善理解。



## Android 4.4 KITKAT

透明状态栏Android是在API 19引入的，这是很早的版本了，现在都8102年了，已经很少见比这个版本还早的版本了，完全不用担心不能实现，但出于对于程序的严谨，还是需要判断一下VERSION（保不齐还真有老古董呢）。实现透明状态栏的方式有两种，一种是直接更改theme，还有一种是在代码中去更改。接下来仔细说说这两个方法。



## 通过Theme实现透明状态栏

在Android 4.4 style中有一个`android:windowTranslucentStatus`把这个设为ture就行了。

按下面的方法更改styles.xml：

```java
<resources>
    <!-- Base application theme. -->
    <style name="BaseTheme" parent="Theme.AppCompat.Light.NoActionBar">
        <!-- Customize your theme here. -->
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
    </style>

    <style name="AppThem" parent="BaseTheme"/>
</resources>

```

在values同级目录下新建一个values-v19，这样做的话如果是4.4 级以上会自动读取v19的文件，省了版本判断。

```java
<resources>

    <style name="AppTheme" parent="BaseTheme">
        <item name="android:windowTranslucentStatus">true</item>
    </style>

</resources>
```

看看效果：

![](assets\windowStatus_4_4.png)

![](assets\windowStatus_6_0+.png)

第一张图为Android 4.4版本，第二章为Android 5.0+版本。可见我们已经使得状态栏透明了，但最顶部的View顶了上去。先不急，我们先看看用代码怎么实现：

```java
public class MainActivity extends AppCompatActivity {
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT){
            //设置状态栏透明
            getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
        }
        setContentView(R.layout.activity_main);
        
    }
}
```

简单吧，效果和之前展示的一样，就不再展示了。

但我们发现5.0+之后的状态栏是半透明的，还有一层黑黑的东西很不舒服，好在5.0之后支持设置状态栏的背景颜色，所以我们可以手动设置背景颜色为透明，然后再将布局延伸到状态栏。修改一下吧：

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT){
            //设置状态栏透明
            getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP){
                //首先保证5.0以上
                Window window = getWindow();
                //把之前设置的状态清除掉
                window.clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
               //设置这个属性，具体作用之后再补充，先会用
                window.getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN|View.SYSTEM_UI_FLAG_LAYOUT_STABLE);
                //新加一个画背景的标签
               window.addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS);
                //把背景设置为透明
                window.setStatusBarColor(Color.TRANSPARENT);
            }
        }
        setContentView(R.layout.activity_main);
        //设个设置和setContentView的位置没啥关系，也可以放在之后
    }
}
```

再看看之后的效果：

![5.0+after](assets\5.0+after.png)

哎哟不错哦。但这样高度不太对。这时我们需要使用到一个属性：

`android:fitsSystemWindows="true"`有了这个属性就可以实现了，但这个属性也不是随便乱用的，这个属性实际上也是采用了padding的方法，所以用的时候要小心。其实知道原理之后也可以不使用属性，自己动态去设置padding也是可以的。甚至还能完成更出色的效果。这里就先不说了，先按简单的来：

修改布局：

```java
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">


    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        android:fitsSystemWindows="true"
        android:background="#f76969">

        <android.support.v7.widget.Toolbar
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:orientation="vertical">

        </android.support.v7.widget.Toolbar>
    </LinearLayout>

    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"/>


</RelativeLayout>
```

为什么我在Toolbar外面在添加了一层呢，就因为padding这个属性，如果我在`RelativeLayout`中或者`Toolbar`中设置，最后的结果就是状态栏现实的是最底层布局的背景颜色，而不是Toolbar的颜色，按理说应该是可行的，但我在实际操作中发现如果将`android:fitsSystemWindows="true"`设置在ToolBar中，无效果，所以我在外部再套了一层`LinearLayout`通过对这个设置，来达到效果。margin是外部padding是内部。有博主是可行的，但我怎么试不行。看看效果：

![Screenshot_1533190404](assets\Screenshot_1533190404.png)

说道这里差不多就全说了，最后再补充一个，如果背景颜色是浅色，那么字体就应该变黑，这个在6.0之后也可以实现。

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M){
 	//两个属性配合使用，若只使用前一个，还会出现白色状态栏 
  window.getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR|View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN);
}
```



## 再说一句

后面这些都采用5.0+版本的图片，4.4也是差不多的。这个其实不难，主要是理解其中的具体实现。这里还有很多没说清楚，学代码，谁不是现抄再理解的，等我回了哪些在更新。![525270921E5100F8FD598AF44006094FEDE64AC04](assets\525270921E5100F8FD598AF44006094FEDE64AC04.jpg)

