# 如何避免EditText获取焦点
  很多时候，我们在使用界面上的EditText时，并不希望马上弹出软键盘，弹出软键盘可能会遮住某些数据，不利于浏览。
  为了避免EditText在显示界面时就获取焦点，可以在根布局添加以下两个属性：
  ```
    android:focusable="true"
    android:focusableInTouchMode="true"
  ```
  ## Done!
