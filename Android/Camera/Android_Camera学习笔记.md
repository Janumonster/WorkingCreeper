# Android_Camera学习笔记

​	这是在美图实习的第三周，我收到了一个简易的相机的项目，这个项目的目的就是为了让我们实习生能够尽快的熟练Camera的基本内容，为此也看了很多网上的博主写的博文，现在我自己也是这总结一下我这一周所学到的东西。主要还是针对Camera1的内容，Camera2的内容还未学习。



我很想将这个项目使用MVC进行划分，但显然我的实力还没有达到那种程度，思路比较凌乱。



首先，创建一个新项目，我们都知道相机是手机的一个硬件，我们的app想要使用这个部件，是需要申请权限的，这里就涉及到权限的申请问题，Android为了用户的权限安全，在Android 6.0开始，改变了权限申请的策略，在AndroidManifest.xml中写了权限仍不够，在需要动态申请，静态在5.0或更低版本已改还能生效。静态申请如下：

```java
<uses-permission android:name="android.permission.CAMERA" />
<uses-feature android:name="android.hardware.camera" android:required="true"/>
<uses-feature android:name="android.hardware.camera.autofocus" android:required="false"/>
```

第一行为摄像头的使用权限，后面是特性。动态申请可以使用Google官方推出的一个动态权限申请的SDK：Easypermissions，具体使用方法可以在github上直接搜索。

```java
implementation 'pub.devrel:easypermissions:1.2.0'
```



要实现一个相机，第一步就是要实现他的预览界面，android中有SurfaceView和TextureView两个控件用于相机的预览也只能使用这两个，这篇文章采用的是SurfaceView。你可以在Xml布局文件中直接添加这个控件，也可以使用Java代码添加，个人比较喜欢使用代码添加，这样更具有灵活性，但其实用Xml也很灵活，主要看个人习惯。

```java
public class CameraPreview extends SurfaceView implements SurfaceHolder.Callback {

    private SurfaceHolder mHolder;

    private Camera mCamera;

    public CameraPreview(Context context, Camera camera) {
        super(context);
        mCamera = camera;
        mHolder = getHolder();
        mHolder.addCallback(this);
        mHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
    }

    @Override
    public void surfaceCreated(SurfaceHolder surfaceHolder) {
        //在这里你可以设置一些初始的相机的状态
        try {
            mCamera.setPreviewDisplay(surfaceHolder);
            mCamera.startPreview();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    @Override
    public void surfaceChanged(SurfaceHolder surfaceHolder, int i, int i1, int i2) {

        if (mHolder.getSurface() == null){
            return;
        }
        try {
            mCamera.stopPreview();
        }catch (Exception e){
            e.printStackTrace();
        }


        try {
            mCamera.setPreviewDisplay(surfaceHolder);
            mCamera.startPreview();
        } catch (IOException e) {
            e.printStackTrace();
        }

    }

    @Override
    public void surfaceDestroyed(SurfaceHolder surfaceHolder) {

    }
}

```

以上就是我所使用的，这个代码和各大博主的一样，因为真的就差不多是这样。



在第一次实践的时候，我将Camera直接在activity中new出来，几乎所有的相机操作都在activity中，导致代码冗长，不易于阅读，所以我将Camera单独放在一个类中，相机的有关才做也都放在这里，包括预览界面于摄像头的绑定我也都放在了我的这个类中，activity通过这个类获得预览的view。

下面是布局文件：

```java
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <FrameLayout
        android:id="@+id/camera_preview"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</RelativeLayout>
```

这布局比较简单就一个FrameLayout，用于放置SurfaceView，之后可以根据自己的需要和设计进行界面设计，这里就先让Camera预览出现就行。

下面是Activity：

```java
public class CameraActivity extends AppCompatActivity implements
        View.OnClickListener,
        EasyPermissions.PermissionCallbacks{
        
        private RawCamera mRawCamera = null;
         @BindView(R.id.camera_preview)
    	public FrameLayout previewLayout;

    //相机所需的权限
    private String[] permissions = {Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE};

    @SuppressLint("ClickableViewAccessibility")
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
       	//设置全屏显示
        getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN, WindowManager.LayoutParams.FLAG_FULLSCREEN);
        //设置布局
        setContentView(R.layout.activity_main);
        //这里使用了一个BindView工具，简化代码，具体请百度:)
        ButterKnife.bind(this);
 		//判断是否授权
        if (!EasyPermissions.hasPermissions(this,permissions)){
            EasyPermissions.requestPermissions(CameraActivity.this,"应用需要相关权限才能正常工作",0,permissions);
        }

        previewLayout = findViewById(R.id.camera_preview);
    }
    
    //权限请求
    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
        EasyPermissions.onRequestPermissionsResult(requestCode, permissions, grantResults);
    }

    @Override
    public void onPermissionsGranted(int requestCode, @NonNull List<String> perms) {
    }

    @Override
    public void onPermissionsDenied(int requestCode, @NonNull List<String> perms) {
        for (String s : perms) {
            Log.d(TAG, "onPermissionsDenied : " + requestCode + "/" + s);
        }
        StringBuffer sb = new StringBuffer();
        for (String str : perms) {
            sb.append(str);
            sb.append("\n");
        }
        sb.replace(sb.length() - 2, sb.length(), "");
        if (EasyPermissions.somePermissionPermanentlyDenied(this, perms)) {
            Toast.makeText(this, "已拒绝权限" + sb + "并不再询问", Toast.LENGTH_SHORT).show();
            new AppSettingsDialog
                    .Builder(this)
                    .setRationale("此功能需要{" + sb + "}权限，否则无法正常使用，是否打开设置")
                    .setPositiveButton("确认")
                    .setNegativeButton("取消")
                    .build()
                    .show();
        }
    }

    //activity生命周期重写内容
    @Override
    protected void onResume(){
        super.onResume();
        previewLayout.removeAllViews();
        if (EasyPermissions.hasPermissions(this,Manifest.permission.CAMERA)){
            Log.d(TAG, "onResume: ");
            mRawCamera = new RawCamera(this);
            previewLayout.addView(mRawCamera.getCameraPreview());
        }
    }
}
```

如果不出意外的话，是可以运行的，代码我并没有运行过，只是基于我已经写好的一个项目进行删改后的结果，请不要复制黏贴。关于权限的设置产不多就是上面代码所示。

Camera和surfaceview的绑定很简单，就是将你目前的camera和context传入自己写的Preview就可以了。

接下来主要是一些Camera的feature，包括camera的一些我有用到的接口的介绍。只会写到一部分我遇到的问题，具体接口请查阅Android SDK文档。

### Camera.Open()

camera的获取使用的是camera.open();可以有参数，参数为int型，camera的id。传入参数可以获得相应的相机，前提是你有这个camera设备。否则会报错或者有些手机系统会回null给你。这个需要注意！

在确定

### setDisplayOrientation

摄像头开启后，摄像头的坐标和屏幕的坐标是不一样的，你可能看到的画面是横着的，这就需要我们自己去调整preview的方向，google为了方便开发者也提供了可以适配大部分机型的旋转代码：

```java
 private static void setCameraDisplayOrientation(Activity activity, int cameraId, Camera camera) {
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
        camera.setDisplayOrientation(result);
    }

```

使用这段代码基本上可以解决这类问题。

在初始化相机的时候我们需要用到Parameters这个东西，我们通过它对相机的参数进行修改，每个摄像头都有自己的parameter，所以当你切换摄像头的时候记得将这个实例也更新一下。

在旋转摄像头的时候，每个摄像头的需安装角度可能不一致，但我目前所接触到的开发机每个摄像头都是90度，不知道是怎么回事，这些厂家都统一标标准了？？？

切换摄像头的时候，如果你是将surfaceView填充到Framelayout中的要记得先清空framelayout中的view，否则画面将停留在上一个摄像机的最后一刻，因为在你切换摄像机时将会有一个新的surfaceview；但如果你直接将surfaceview写在xml中，那一改就没这个烦恼了。