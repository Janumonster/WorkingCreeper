# Android_设置控件可见和不可见

通常控件的可见与不可见分为三种情况。

第一种    gone         表示不可见并且不占用空间

第二种    visible       表示可见

第三种    invisible    表示不可见但是占用空间

```
android:visibility="gone"  
android:visibility="visible"  
android:visibility="invisible"  
```

```
setVisibility(View.Gone);  
setVisibility(View.VISIBLE);  
setVisibility(View.INVISIBLE); 
```

