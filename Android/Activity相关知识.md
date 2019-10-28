# Activity
>平台：win10       工具：AndroidStudio
****
## 1.1 活动是什么

一种可以包含用户界面的组件，主要用于和用户的交互。是android的四大组件之一（activity、service、content provider和broadcast receiver）。
​     

## 1.2 布局的加载
采用下列函数,设置布局。     
```
  setContentView(R.layout.id);
```

## 1.3 在AndroidMainfest中注册
在\<application\>\</application\>标签中添加注册声明：
```
  <activity android:name=".Mainactivity"></activity>
```
在\<activity\>中添加\<intent-filter\>标签，并在其中添加\<action android:name="andoird.intent.action.MAIN"/\>和\<category android：name="android.intent.category.LAUNCHER"/\>    
这两句声明即可间将活动设置为默认启动活动。
```
  <mainfest xmls:andoid="http://shemas.android.com/apk/res/andoird"
    package="com.example.activitytest">
      <application
        ···>
        <activity android:name=".Mainactivity"
          android:lable="This is Mainactivity">
          <intent-filter>
              <action android:name="andoird.intent.action.MAIN"/>
              <category android：name="android.intent.category.LAUNCHER"/>
          </intent-filter>
        </activity>
     </application>
  </mainfest>
```

## 活动的生命周期

### 2.1返回栈
每当启动一个新的活动，就将改活动加入栈，并出处于栈顶位置，当调用活动的finish()；或按下back时，处于栈顶的活动就出栈，之前的活动就处于栈顶。

<br><div align="center"> <img src="pics/返回栈.png"/> </div><br>



### 2.2活动状态

##### 2.2.1 运行状态
  	处于栈顶的活动就输处于活动状态，系统最不愿意回收，会带来很差的用户体验。

##### 2.2.2 暂停状态
  	活动不处于栈顶，但仍然可见，就是暂停状态。例如对话框的活动在栈顶时，背后的活动仍可见，但没有交互功能。

##### 2.2.3 停止状态
  	活动不在栈顶，且完全不可见。系统仍会保留改活动的成员变量和状态，但并不完全可靠，当其他地方需要内存时，处于停止状态的活动可能会被回收。

##### 2.2.4 销毁状态
  	活动从返回栈中移除后就变成了销毁状态。系统会最倾向于回收处于这种状态的活动，以保证内存充足。

### 2.3活动的生命周期

<br><div align="center"> <img src="pics/life_stream.jpg"/> </div><br>

***onCreate（）:*** 表示***activity***正在被创建，这是输入你改名周期的第一个方法。每个活动中该函数都会被重写，创建该活动是被调用，完成活动的初始化。***setContentView***（）会在这个方法中被调用。

***onStart（）：*** 表示**activity**正在被启，这是**activity**为可见状态，但还未在前台，不能和用户进行交互；

***onResume（）：*** 表示**activity**已经可见，可以和用户进行交互，**activity**在前台运行。活动位于栈顶，处于运行状态；

***onPause（）：*** 表示**activity**正在停止，一般情况下回调用**onStop** 马上会被调用。在特殊情况下，如用户很快点击返回键，想要回到原**activity** 那么**onResume** 会被调用，但实际操作很难达到这个速度。

 ***onStop（）：*** 表示活动即将停止，可以处理一下轻量级的回收工作，**不能耗时！！！**活动完全不可见时调用。

***onDestroy（）：*** 活动被销毁之前调用。做一些回收工作和资源释放。

***onRestart（）：*** 活动从不可见状态转换为可见状态是被调用，当执行了**onPause**和**onStop**后重新回到该activity时执行。



> 在两个activity交替启动时先调用前一个activity的onPause，在的调用新activity的onCreate.



### activity的异常生命周期

##### 情况一：资源相关的系统配置发生改变导致activity被杀死并重新创建

​	如当activity出于竖屏是，突然改变屏幕使之变成横屏，默认情况下，activit会被销毁并重建，也可以通过设置阻止重建。

​	当系统配置发生改变，activity会被销毁，由于是在异常情况下终止的，系统会调用onSaveInstanceState来保存当前activity的状态，调用时机在onStop之前，在onPause左右，或前或后没有一定的顺序。onSaveInstanceState这个犯法只会在异常终止activity是才会被调用，正常情况下不会调用。当activity被重新创建后，系统回调用onRestoreInstanceState，并且把activity销毁时的onSaveInstanceState方法保存的Bundle对象作为参数同时传递给onRestoreInstanceState和onCreate，onRestoreInstanceState调用时机在onStart之后。

**情况二：资源内存不足导致第优先级的activity被杀死** 

> 因为会出现这种情况，所以将一些后台工作放入Service中从而保证进程有一定的优先级，这样就不会轻易地被杀死。

系统配置中有很多内容，如果某项发生改变后，我们不希望重建activity，就可以给activity指定configChanges属性。如不想横屏时activity新建：

```
android：configChanges="orientation"
```

​	

| 项目                 | 含义                                       |
| ------------------ | :--------------------------------------- |
| mcc                | SIM卡唯一标识IMSI（国际移动用户标识码）中的国家代码，由三位数组成，中国为460。此项标识mcc代码发生改变 |
| mnc                | SIM卡唯一标识IMSI（国际移动用户标识码）中的运营商代码，由两位数组成，中国移动TD系统一为00，中国联通为01，中国电信为03。此项标识mcc代码发生改变 |
| locale             | 设备的本地位置发生改变，一般指切换了系统语言                   |
| touchscreen        | 触摸屏发生了改变，这个我也不懂啥意思，一般也用不到，可以忽略           |
| keyboard           | 键盘类型发生改变，如外接键盘                           |
| keyboardHidden     | 键盘可访问性发生了改变，用户调出键盘                       |
| navigation         | 系统导航方式发生改变                               |
| screenLayout       | 屏幕布局发生了改变，扩展了显示设备                        |
| fontScale          | 系统字体缩放比例发生了改变                            |
| uiMode             | 用户界面模式发生改变                               |
| orientation        | 屏幕方向发生改变（最常用）                            |
| screenSize         | 当屏幕尺寸信息发生改变，当屏幕旋转时，尺寸发生改变，这个选项比较特殊，和编译选项有关，当编译选项中的minSdkVersion和targetSdkversion均低于13时，此项不会导致activity重建。 |
| smallestScreenSize | 仅仅表示实际物理尺寸改变的时候发生，当编译选项中的minSdkVersion和targetSdkversion均低于13时，此项不会导致activity重建。 |
| layoutDirection    | 当布局方向发生变化，较少使用，以为一般不会去改变布局的layoutDirection属性 |



## 活动的启动模式
一共有4种启动模式：**standard、singleTop、singleTask、singleInstance** 。  
通过在AndroidMainfest中给<activity>标签中加入 **android：launchMode** 属性来实现。
```java
     <activity
         android:name=".Mainactivity"
    ---->android:launchMode:"*******"<-----
         android:lable="this is Mainactivity">
          <intent-filter>
               ···
          </intent-fliter>
      </activity>
```

**standard:** 默认启动模式，每次启动都会创建一个新活动，不会受返回栈中的活动影响。即：若该活动已经在返回栈中，每次启动该活动，都会建立新的活动，会出现返回几次都在相同活动的情况。

**singleTop：** **standard** 的改进，如果将要启动的新活动已经在栈顶时，不会创建新活动。但如果相间开启两个活动，依然会出现堆叠。

**singleTask：** 对**singleTop** 的改进，返回栈中只要存在该活动，就不会新建活动，系统会将该活动之上的所有活动出栈，使该活动位于栈顶。

**singleInstance：** 通过一个新的返回栈来管理，可以实现活动在应用程序间的共享。