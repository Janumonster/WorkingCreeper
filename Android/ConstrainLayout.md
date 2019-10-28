# ConstrainLayout

实现布局最大程度扁平化，即减少布局的嵌套。实现布局的边平化有助于提升应用的性能。

用于确定控件的位置的属性有：

> layout_constraintLeft_toLeftOf
> layout_constraintLeft_toRightOf
> layout_constraintRight_toLeftOf
> layout_constraintRight_toRightOf
> layout_constraintTop_toTopOf
> layout_constraintTop_toBottomOf
> layout_constraintBottom_toTopOf
> layout_constraintBottom_toBottomOf
> layout_constraintBaseline_toBaselineOf
> layout_constraintStart_toEndOf
> layout_constraintStart_toStartOf
> layout_constraintEnd_toStartOf
> layout_constraintEnd_toEndOf

其中中间的位置（拿第一个`layout_constraintLeft_toLeftOf`举例），中间的constrainLeft表示的是该控件的左侧，toLeftof的用法和RelativeLayout的用法一样，是说明在谁的左边，那这个属性的意思就是，该控件的左边界在某某的左边。

所有属性都可以自由组合，可以自己尝试，我这边就说明一下一种情况，有时候我们的控件宽度不一致，而我们又想让它们居中对齐，那么可以将layout_constraintLeft_toLeftOf和layout_constraintRight_toRightOf都进行设置，及其宽度不一致，也可以实现居中。（注意属性中位置的组合是不同的）。

同样实现在布局中居中只需要同样适用上面两个属性，只不过是相对于parent。

### bias

偏移，在设置了居中之后，进行左右比重的偏移，在设置了相对于父布局的属性后，在布局预览处会出现类似弹簧的提示，bias默认是0.5，也就是不偏移，当然偏移也分水平和竖直，在使用该属性时要注意一定要先把位置属性先确定一下。而且这个是相对于父布局的！！

### ratio

用于控制控件的宽高比例，所以空间中至少有一个宽高属性为0dp，格式为 宽:高 ，也可以在之前加H或者W，字母用于指定比较的前者是什么属性H高，W宽。默认是W。

### chain

当出现一个横向互相制约的控件（至少两个时，可以设置chainstyle，来设置一些简单样式），只要第一个设置就行。下列style中出了weight可以不需要宽度，其他都应该给出宽度。

![](assets\15fccd91b5f13c8e.jpg)

### GuideLine

可以给出一条虚线，实际运行时时看不见的，可用于辅助调整空间位置。