# 自定义View笔记

> 这份笔记是学习，凯哥大佬的，地址：https://hencoder.com/，虽然这篇文章是对于高级工程师的提升，而我现在还是一个实习生，奈何实在是对自定义view刚兴趣，就学习了一下。

​	这次的学习将是从头开始实现一个自定义view，所以所继承的就是view，不会对其他空间进行继承。

## onDraw

自定义view 中一定会接触到的一个函数，负责将view画出来，我们对其进行重写，但是还是会有`super.onDraw();`。

- drawColor：这是最基本的，画颜色

- drawRGB/drawARGB：这两个也是画颜色

- drawCircle：画圆

- drawRect：画矩形

- drawPoint/drawPoints：画点，画很多点

- drawOval：画椭圆

- drawLine/drawLines：画线

- drawRoundRect：画圆角矩形

- drawArc：画弧度

- drawPath：画路径
