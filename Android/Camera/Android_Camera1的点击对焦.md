# Android_Camera1的点击对焦

> 本文针对AndroidCamera的相机点击对焦做学习分析，Camera2请移步。
>
> 开发工具：AndroidStudio
>
> 测试手机：小米 MIX 2s

前半部分将设置预览区域，后半部分讲计算区域的问题，以及遇到的坑

[TOC]

相信很多人第一次打开Camera的startPreview是一脸懵逼的，我们看到下面这个画面。

![](assets\Screenshot__com.wbo.camera.png)

可以看到，我是直立手机拍摄的，原来应该横向的的“笔”怎么就竖直了呢？

其实我们的Camera设备和我们的屏幕的坐标是不一样的，虽然我也不知道为什么不做成一样的，可能技术上有限制，这就需要我们去手动矫正。分析一下这个画面，![IMG_20180823_101100](assets\IMG_20180823_101100.jpg)

画面被逆时针旋转了90度，好，那我转回来。

```java
   camera.setDisplayOrientation(90);
 	/**
      * 这里为什么是90呢，以为要顺时针转90度
      * 那其他机型怎么办呢?
      * 其实大部分都是90，但也有一些是不一样的，不确定可以使用官方给的旋转代码下面给出
      */


	private static void setCameraDisplayOrientation(Activity activity,int cameraId, Camera camera) {
        android.hardware.Camera.CameraInfo info = new android.hardware.Camera.CameraInfo();
        android.hardware.Camera.getCameraInfo(cameraId, info);
        int rotation = activity.getWindowManager().getDefaultDisplay()
                .getRotation();
        int degrees = 0;
        switch (rotation) {
            case Surface.ROTATION_0:
                degrees = 0;
                break;
            case Surface.ROTATION_90:
                degrees = 90;
                break;
            case Surface.ROTATION_180:
                degrees = 180;
                break;
            case Surface.ROTATION_270:
                degrees = 270;
                break;
        }

        int result;
        if (info.facing == Camera.CameraInfo.CAMERA_FACING_FRONT) {
            result = (info.orientation + degrees) % 360;
            result = (360 - result) % 360;
        } else {
            result = (info.orientation - degrees + 360) % 360;
        }
        //最终也是调用设个函数，不过事先帮你计算了角度
        camera.setDisplayOrientation(result);
    }
	
```

好，旋转完，我们再看看画面![Screenshot_306_com.wbo.camera](assets\Screenshot_306_com.wbo.camera.png)

好了，画面正常了。接下来我们看点击对焦：

我也在各种地方找了很多博文，大多一个版本，抄来抄去没意思，还没说清楚，干脆自己写一篇。

我们先从设置预览点说起：

要设置对焦区域我们需要用到Camera的一个内部类Area，`new Camera.Area(rect,1000)`我们需要传入一个Rect和一个权重值，权重就相当于优先级吧，比较重要。把这个新的Area加入一个List，最后再将这List设置到parameters的FocusAreas。具体代码如下：

```java
//判断是否有camera实例
if (isCamreaNotNull()){
    //将对焦模式设为自动对焦
    parameters.setFocusMode(Camera.Parameters.FOCUS_MODE_AUTO);
	//判断可对焦区域是否不为0，为0说明该摄像头不支持对焦
    if (parameters.getMaxNumFocusAreas() > 0){
        //创建List
        List<Camera.Area> focuseAreas = new ArrayList<Camera.Area>();
        //将新的对焦区域加入List
        focuseAreas.add(new Camera.Area(rect,1000));
        //设置对焦区域属性
        parameters.setFocusAreas(focuseAreas);
    }
    //取消所有自动对焦，防止对对焦区域干扰（这是个人理解）
    camera.cancelAutoFocus();
    //为相机设置属性
    camera.setParameters(parameters);
}
```



# 点击区域的计算

先看看原始坐标：

![IMG_20180823_104604](assets\IMG_20180823_104604.jpg)

左上角是(-1000,-1000)，中间是(0,0)，在右下角是(1000,1000)，我们可以知道这个坐标系是一个2000\*2000的正方形。我之前看了很多博文，他们是直接获取点击位置的x,y然后构建一个Rect，之后将Rect的left，top，right，bottom全部转化为新的坐标系。我按照他们的代码试了一下，发现对焦的地方并不是我点击的地方，为什么呢？很简单啊，你旋转了啊！

![IMG_20180823_105743](assets\IMG_20180823_105743.jpg)

左边是旋转前，右边是旋转后，然后对旋转后的X处点击，实际上是对3区域进行点击，这时候我们就需要考虑要怎么变换了。为了让点击区域和对焦区域一致，我就把屏幕坐标也进行旋转，这个变换不是简单的x=y，y=width-x。这么简单。旋转90度，我们将会对横坐标进行放大，纵坐标缩小。这个缩放比例是要根据具体的预览的View的大小的width和hight就是view的宽和高。看代码：

```java
public void calculatePostion(float x, float y, int width, int hight){

        //坐标系旋转变化，并做缩放变换，防止y过大越界
        int cX = (int) (y*width/hight);
        int cY = (int) (x*hight/width);

        //这是后两个坐标角度一致，可以之接变换了，计算对焦区域的边界
        int left = (cX-50)*2000/width - 1000;
        int top = (cY-50)*2000/hight - 1000;
        int right = (cX+50)*2000/width - 1000;
        int bottom = (cY+50)*2000/hight - 1000;

        //防止越界
        left = left < -1000 ? -1000 : left;
        top = top <-1000 ? -1000 : top;
        right = right > 1000 ? 1000 : right;
        bottom = bottom > 1000 ? 1000 : bottom;

//        Log.d(TAG, "left:"+left+" top:"+top+" right:"+right+" bottom:"+bottom);

        newfocusAreas(new Rect(left,top,right,bottom));
    }
```

之后我再试了一下，没问题了，不过没测试其他机型。