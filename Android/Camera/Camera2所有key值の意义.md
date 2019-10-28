# Camera2所有key值の意义

**查询Camera2的支持情况：**

通过Characteristics获取，

```java
Integer deviceLevel = characteristics.get(CameraCharacteristics.INFO_SUPPORTED_HARDWARE_LEVEL);
```

返回的deviceLevel的值有：

LEVEL_LEGACY: 向后兼容模式, 如果是此等级, 基本没有额外功能, HAL层大概率就是HAL1(我遇到过的都是)

LEVEL_LIMITED: 有最基本的功能, 还支持一些额外的高级功能, 这些高级功能是LEVEL_FULL的子集

LEVEL_FULL: 支持对每一帧数据进行控制,还支持高速率的图片拍摄

LEVEL_3: 支持YUV后处理和Raw格式图片拍摄, 还支持额外的输出流配置

LEVEL_EXTERNAL: API28中加入的, 应该是外接的摄像头, 功能和LIMITED类似

出了最后一个，从上至下功能逐渐加强。



```
/**
* android.hardware.camera2.CaptureRequest.COLOR_CORRECTION_MODE
* android.hardware.camera2.CaptureRequest.COLOR_CORRECTION_TRANSFORM
* android.hardware.camera2.CaptureRequest.COLOR_CORRECTION_GAINS
* android.hardware.camera2.CaptureRequest.COLOR_CORRECTION_ABERRATION_MODE

* android.hardware.camera2.CaptureRequest.CONTROL_AE_ANTIBANDING_MODE
* android.hardware.camera2.CaptureRequest.CONTROL_AE_EXPOSURE_COMPENSATION
* android.hardware.camera2.CaptureRequest.CONTROL_AE_LOCK
* android.hardware.camera2.CaptureRequest.CONTROL_AE_MODE
* android.hardware.camera2.CaptureRequest.CONTROL_AE_REGIONS
* android.hardware.camera2.CaptureRequest.CONTROL_AE_TARGET_FPS_RANGE
* android.hardware.camera2.CaptureRequest.CONTROL_AE_TARGET_FPS_RANGE.new TypeReference() {...}
* android.hardware.camera2.CaptureRequest.CONTROL_AE_PRECAPTURE_TRIGGER

* android.hardware.camera2.CaptureRequest.CONTROL_AF_MODE
* android.hardware.camera2.CaptureRequest.CONTROL_AF_REGIONS
* android.hardware.camera2.CaptureRequest.CONTROL_AF_TRIGGER

* android.hardware.camera2.CaptureRequest.CONTROL_AWB_LOCK
* android.hardware.camera2.CaptureRequest.CONTROL_AWB_MODE
* android.hardware.camera2.CaptureRequest.CONTROL_AWB_REGIONS
* android.hardware.camera2.CaptureRequest.CONTROL_CAPTURE_INTENT
* android.hardware.camera2.CaptureRequest.CONTROL_EFFECT_MODE
* android.hardware.camera2.CaptureRequest.CONTROL_MODE
* android.hardware.camera2.CaptureRequest.CONTROL_SCENE_MODE
* android.hardware.camera2.CaptureRequest.CONTROL_VIDEO_STABILIZATION_MODE

* android.hardware.camera2.CaptureRequest.EDGE_MODE
* android.hardware.camera2.CaptureRequest.FLASH_MODE
* android.hardware.camera2.CaptureRequest.HOT_PIXEL_MODE

* android.hardware.camera2.CaptureRequest.JPEG_GPS_LOCATION
* android.hardware.camera2.CaptureRequest.JPEG_GPS_COORDINATES
* android.hardware.camera2.CaptureRequest.JPEG_GPS_PROCESSING_METHOD
* android.hardware.camera2.CaptureRequest.JPEG_GPS_TIMESTAMP
* android.hardware.camera2.CaptureRequest.JPEG_ORIENTATION
* android.hardware.camera2.CaptureRequest.JPEG_QUALITY
* android.hardware.camera2.CaptureRequest.JPEG_THUMBNAIL_QUALITY
* android.hardware.camera2.CaptureRequest.JPEG_THUMBNAIL_SIZE

* android.hardware.camera2.CaptureRequest.LENS_APERTURE
* android.hardware.camera2.CaptureRequest.LENS_FILTER_DENSITY
* android.hardware.camera2.CaptureRequest.LENS_FOCAL_LENGTH
* android.hardware.camera2.CaptureRequest.LENS_FOCUS_DISTANCE
* android.hardware.camera2.CaptureRequest.LENS_OPTICAL_STABILIZATION_MODE

* android.hardware.camera2.CaptureRequest.NOISE_REDUCTION_MODE
* android.hardware.camera2.CaptureRequest.REQUEST_ID
* android.hardware.camera2.CaptureRequest.SCALER_CROP_REGION

* android.hardware.camera2.CaptureRequest.SENSOR_EXPOSURE_TIME
* android.hardware.camera2.CaptureRequest.SENSOR_FRAME_DURATION
* android.hardware.camera2.CaptureRequest.SENSOR_SENSITIVITY
* android.hardware.camera2.CaptureRequest.SENSOR_TEST_PATTERN_DATA
* android.hardware.camera2.CaptureRequest.SENSOR_TEST_PATTERN_MODE

* android.hardware.camera2.CaptureRequest.SHADING_MODE

* android.hardware.camera2.CaptureRequest.STATISTICS_FACE_DETECT_MODE
* android.hardware.camera2.CaptureRequest.STATISTICS_HOT_PIXEL_MAP_MODE
* android.hardware.camera2.CaptureRequest.STATISTICS_LENS_SHADING_MAP_MODE

* android.hardware.camera2.CaptureRequest.TONEMAP_CURVE_BLUE
* android.hardware.camera2.CaptureRequest.TONEMAP_CURVE_GREEN
* android.hardware.camera2.CaptureRequest.TONEMAP_CURVE_RED
* android.hardware.camera2.CaptureRequest.TONEMAP_CURVE
* android.hardware.camera2.CaptureRequest.TONEMAP_MODE
* android.hardware.camera2.CaptureRequest.TONEMAP_GAMMA
* android.hardware.camera2.CaptureRequest.TONEMAP_PRESET_CURVE

* android.hardware.camera2.CaptureRequest.LED_TRANSMIT
* android.hardware.camera2.CaptureRequest.BLACK_LEVEL_LOCK
* android.hardware.camera2.CaptureRequest.REPROCESS_EFFECTIVE_EXPOSURE_FACTOR
*/
```