# Android_PopupWindow

 它的构造方法有：

```java
public PopupWindow(View contentView, int width, int height, boolean focusable)
    //contentView为要显示的view，width和height为宽和高，值为像素值，也可以是MATCHT_PARENT和WRAP_CONTENT。最后一个参数为是否可以获得焦点
public PopupWindow (Context context)
    
public PopupWindow(View contentView, int width, int height)
    
public PopupWindow(View contentView)
```

填popupwindow的view可以再构造函数是填装，也可以通过setContentView(View contentView)；来设置。

------

**显示PopupWindow：**

- showAsDropDown(View anchor)：相对某个控件的位置（正左下方），无偏移 
- showAsDropDown(View anchor, int xoff, int yoff)：相对某个控件的位置，有偏移
- showAtLocation(View parent, int gravity, int x, int y)：相对于父控件的位置（例如正中央Gravity.CENTER，下方Gravity.BOTTOM等），可以设置偏移或无偏移



其实showAtLocation的parent参数可以很随意，只要是activity中的view都可以。



设置大小可以通过构造函数，也可以通过setHight(LayoutParams.WRAP_CONTENT);和setWidth(LayoutParams.WRAP_CONTENT);来设置。

```java
    PopupWindow popupWindow = new PopupWindow(contentview);
    popupWindow.setWidth(LayoutParams.WRAP_CONTENT);           
    popupWindow.setHeight(LayoutParams.WRAP_CONTENT);
```

这里的WRAP_CONTENT可以换成fill_parent 也可以是具体的数值，它是指PopupWindow的大小，也就是contentview的大小，注意popupwindow根据这个大小显示你的View，如果你的View本身是从xml得到的，那么xml的第一层view的大小属性将被忽略。相当于popupWindow的width和height属性直接和第一层View相对应。



**PopUpWindow的焦点：**

setFocusable设置PopupWindow的焦点，一般资料对此的解释都是：是否让Popupwindow可以点击但是这揭示了本质却与直观现象不符。实际上，

如果：

**setFocusable(true);**

则PopUpWindow本身可以看作一个类似于模态对话框的东西（但有区别），PopupWindow弹出后，所有的触屏和物理按键都有PopupWindows处理。其他任何事件的响应都必须发生在PopupWindow消失之后， （home  等系统层面的事件除外）。比如这样一个PopupWindow出现的时候，按back键首先是让PopupWindow消失，第二次按才是退出activity，准确的说是想退出activity你得首先让PopupWindow消失，因为不并是任何情况下按back  PopupWindow都会消失，必须在PopupWindow设置了背景的情况下 。

如果PopupWindow中有Editor的话，focusable要为true。

而setFocusable(false);

则PopUpWindow只是一个浮现在当前界面上的view而已，不影响当前界面的任何操作。

是一个“没有存在感”的东西。

一般情况下setFocusable(true);



**点击空白处的时候让PopupWindow消失**

关于PopupWindow最搞笑的地方是setOutsideTouchable方法，原本以为如果你setOutsideTouchable(true)则点击PopupWindow之外的地方PopupWindow会消失，其实这玩意儿好像一点用都没有。

要让点击PopupWindow之外的地方PopupWindow消失你需要调用setBackgroundDrawable(new BitmapDrawable());

设置背景，为了不影响样式，这个背景是空的。还可以这样写，觉得这样要保险些：

setBackgroundDrawable(new ColorDrawable(0x00000000));

背景不为空但是完全透明。如此设置还能让PopupWindow在点击back的时候消失。

原因：

> 如果有背景，则会在contentView外面包一层**PopupViewContainer**之后作为mPopupView，如果没有背景，则直接用contentView作为mPopupView。
>
> 而这个PopupViewContainer是一个内部私有类，它继承了FrameLayout，在其中重写了Key和Touch事件的分发处理



具体代码：

```java
    LayoutInflater inflater = (LayoutInflater)getSystemService(Context.LAYOUT_INFLATER_SERVICE);
    View contentview = inflater.inflate(R.layout.popup_process, null);
    contentview.setFocusable(true); // 这个很重要
    contentview.setFocusableInTouchMode(true);
    final PopupWindow popupWindow = new PopupWindow(contentview,LayoutParams.WRAP_CONTENT,LayoutParams.WRAP_CONTENT);
    popupWindow.setFocusable(true);
    popupWindow.setOutsideTouchable(false);
    contentview.setOnKeyListener(new OnKeyListener() {
        @Override
        public boolean onKey(View v, int keyCode, KeyEvent event) {
            if (keyCode == KeyEvent.KEYCODE_BACK) {
                popupWindow.dismiss();                                     
                return true;
            }
            return false;
        }
    });
    popupWindow.showAtLocation(view,  Gravity.CENTER|Gravity.CENTER_HORIZONTAL, 0, 0);
```

这里的inflater的getSystemService必须在activity中才有，如果你是在某个fragment或者adaptor中设置popupWindow记得传入activity！！

```
LayoutInflater inflater= (LayoutInflater) activity.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
        View contentView=inflater.inflate(R.layout.view_pop_window,null);
        contentView.setFocusable(true);
        contentView.setFocusableInTouchMode(true);
        popupWindow=new PopupWindow(contentView, ViewGroup.LayoutParams.MATCH_PARENT,ViewGroup.LayoutParams.WRAP_CONTENT);
        popupWindow.setFocusable(true);
        popupWindow.setOutsideTouchable(false);

        contentView.setOnKeyListener(new View.OnKeyListener() {
            @Override
            public boolean onKey(View v, int keyCode, KeyEvent event) {
                if (keyCode==KeyEvent.KEYCODE_BACK){
                    popupWindow.dismiss();
                    return true;
                }
                return false;
            }
        });

```



为了弹出平滑可以再设置动画：

设置动画的方法：

```
public void setAnimationStyle(int animationStyle)
```

在工程res下新建anim文件夹，在anim文件夹先新建两个xml文件

menu_bottombar_in.xml

```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <translate
        android:duration="250"
        android:fromYDelta="100.0%"
        android:toYDelta="0.0" />
</set>
```

menu_bottombar_out.xml

```
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android" >
    <translate
        android:duration="250"
        android:fromYDelta="0.0"
        android:toYDelta="100.0%" />
</set>
```

mPopupWindow.setAnimationStyle(R.style.menu_anim_bottombar);



该笔记为摘抄，原作：http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2014/0702/1627.html