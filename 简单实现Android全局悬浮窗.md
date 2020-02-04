# 简单实现Android全局悬浮窗

## 一、目的

在负责的项目中，有一个需要实现全局悬浮窗的功能，悬浮窗支持拖动，并可响应点击事件。

## 二、实现思路

悬浮窗在Android主要是通过WindowManager实现，即添加一个View，并设置相关参数，使View实现想要的效果。

```java
public class XFWindowView extend FrameLayout{
    private Context mContext;
    private WindowManager.LayoutParams mParams;
    private WindowManager mWindowManager;
    
    private int screenWidth = 0;
    private int screenHeight = 0;
    
    public XFWindowView(@NonNull final Context context){
        super(context);
        mContext = context;
        // 这里的布局的View可以自定义
        View view = LayoutInflater.from(context).inflater(R.layout.xf_windows_layout, null);
        this.addView(view);
        mWindowManager = (WindowManager) context.getSystemService(Context.WINDOW_SERVICE);
        // 获取屏幕宽高
        screenWidth = getScreenWidthPixels();
        screenHeight = getScreenHeightPixels();
    }
    
    // 展示window
    public void show(){
        mParams = new WindowManager.LayoutParams();
        mParams.gravity = Gravity.START | Gravity.TOP;
        mParams.x = screenWidth;
        mParams.y = screenHeight - 200;
        // type指定了悬浮窗的类型，也显示了悬浮窗的可现实范围，之前有使用TYPE_SYSTEM_ALERT，但之后这个类型被抛弃了，可以使用功能一下这个代替。
        mParams.type = WindowManager.LayoutParams.TYPE_APPLICATION_OVERLAY;
        // 各种Flag，主要用于描述Layout的一些属性
        mParams.flags = WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE | 
            WindowManager.LayoutParams.FLAG_NOT_TOUCH_MODAL |
            WindowManager.LayoutParams.FLAG_ALT_FOCUSABLE_IM |
            WindowManager.LayoutParams.FLAG_LAYOUT_IN_SCREEN |    
            WindowManager.LayoutParams.FLAG_LAYOUT_INSET_DECOR |
            WindowManager.LayoutParams.FLAG_WATCH_OUTSIDE_TOUCH;
        mParams.format = PixelFormat.RGBA_8888;
        mParams.width = WindowManager.LayoutParams.WRAP_CONTENT;
        mParams.height = WindowManager.LayoutParams.WRAP_CONTENT;
        mWindowManager.addView(this, mParams);
    }
    
    // 移除悬浮窗
    public void hide(){
        mWindowManager.removeView(this);
    }

    // 更新悬浮窗
    public void update(){
        mWindowManager.updateView(this, mParams);
    }
}
```

以上就是主要实现，这样子的话不出意外就能够在测试样例中看到悬浮窗了。为了处理拖动事件，重写了dispatchTouchEvent方法。

```java
@Override
    public boolean dispatchTouchEvent(MotionEvent event) {
        // 获取相对屏幕的坐标，屏幕左上角为原点
        int x = (int) event.getRawX();
        int y = (int) event.getRawY();

        int action = event.getAction();
        switch (action) {
            case MotionEvent.ACTION_DOWN:
                startTime = System.currentTimeMillis();
                mTouchStartX = event.getX();
                mTouchStartY = event.getY();
                break;
            case MotionEvent.ACTION_MOVE:
                //图标移动的逻辑在这里
                float mMoveStartX = event.getX();
                float mMoveStartY = event.getY();
                // 如果移动量大于3才移动
                if (Math.abs(mTouchStartX - mMoveStartX) > 3
                        && Math.abs(mTouchStartY - mMoveStartY) > 3) {
                    // 更新浮动窗口位置参数
                    mParams.x = (int) (x - mTouchStartX);
                    mParams.y = (int) (y - mTouchStartY);
                    update(); // 更新位置
                    return false;
                }
                break;
            case MotionEvent.ACTION_UP:
                if ((float) screenWidth / 2.0 < event.getRawX()) {
                    mParams.x = screenWidth - 100;
                } else {
                    mParams.x = 0;
                }
                update(); // 更新位置
                endTime = System.currentTimeMillis();
                //当从点击到弹起小于半秒的时候,则判断为点击,如果超过则不响应点击事件
                boolean isClick = !((endTime - startTime) > 0.1 * 1000L);
                if (!isClick) {
                    // 非点击事件，直接消耗掉event
                    return true;
                }

                return super.dispatchTouchEvent(event);
        }
        return super.dispatchTouchEvent(event);
    }
```

## 三、总结

实现悬浮窗主要是对于WindowManager的操作，我们只需要将我们设计的布局添加到WindowManager中并设置好相关参数，展示出来即可。其中主要是处理好点击事件的分发和拦截。