# PopoCircleView

这是一个十分简陋的圆形图片显示工具，这个工具只包含图片的显示，未对图片进行处理，所以使用前请确保图片不会造成内存溢出。

#### 怎么添加

**Step 1.** Add the JitPack repository to your build file

Add it in your root build.gradle at the end of repositories:

```css
	allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}
```

**Step 2.** Add the dependency

```css
	dependencies {
	        implementation 'com.github.Janumonster:PopoCirlceView:0.1.0'
	}
```



#### 怎么使用

```

```

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.zzy.popocircleview.PopoCircleView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:popo_border_width="10"
        app:popo_background_color="#fa7878"
        app:popo_border_color="#ce0101"
        app:popo_radius="200"/>

</android.support.constraint.ConstraintLayout>
```

可直接在xml文件中使用，下面四个属性可以设置边框的颜色和宽度，如果没有设置图片，也可以直接使用颜色。

![1540446549171](assets\1540446549171.png)

图片直接通过src属性设置即可：

![](assets\pic2.png)

目前还是初步开发，之后有空将继续添加新功能，比如添加头饰，认证标志等。