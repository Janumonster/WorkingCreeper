# Android事件分发机制

#### MotionEvent



### 点击事件传递规则

本质是对点击事件MotionEvent的传递，系统需要把这个事件传递给具体的View，这个传递过程就是分发过程。其中有三个方法很重要：

**public boolean dispatchTouchEvent(MotionEvent ev)** 

> 进行事件的分发，若事件能传递到当前的view就会调用该view的这个方法，结果受该View的TouchEvent方法和下级View的dispatchTouchEvent影响，表示是否消耗事件

**public boolean onInterceptTouchEvent(MotionEvent ev)**

> 判断是否拦截某个事件，同一个事件序列只会被调用一次

**public Boolean onTouchEvent(MotionEvent ev)**

> 处理点击事件，返回结果表示是否消耗当前事件，不消耗，则同一事件序列中，当前View无法再次接受事件



```java
public boolean public boolean dispatchTouchEvent(MotionEvent ev){
    if(ev.getAction()==MotionEvent.ACTION_DOWN){
        onUserInteraction();
    }	
    if(getWindow().superDispatchTouchEvent(ev)){
        return true;
    }
    return onTouchEvent(ev);
}
```

同一事件序列从手指触摸屏幕算起，包括一个down事件，一堆move事件和一个up事件。当这一系列的down事件（第一个事件）被某个View拦截小号的时候，后面的事件将都由这个view处理，也有例外情况，只消耗down事件，后面的全部抛弃。

![img](https://upload-images.jianshu.io/upload_images/966283-b9cb65aceea9219b.png?imageMogr2/auto-orient/)

（图片资源来自简书Kelin）



几乎每个view都含有上述方法，其中第二个方法只在ViewGroup中（例如LinearLayout之类的）， **dispatchTouchEvent(MotionEvent ev)**这个方法一定会被调用，以为这个方法就是判断当前的view是否要消耗事件，顶层的view的这个方法无论返回true还是false都表示事件被消耗，之后解释为什么。虽然返回的结果没有啥，但里面的方法还是被调用了，而且最骚的是，它会调用子view的这个方法，有点类似递归的操作，在它给出返回值之前就往下一层去了。一般来说顶层的view是activity，下一层就是viewGroup了。



现在我们（事件）进入了viewGroup层，同样的相对调用viewGroup的**dispatchTouchEvent(MotionEvent ev)**，但是，这个时候他的返回值就有意义了，返回true事件被消耗，返回false表示他的这个ViewGroup没有View消耗这个事件，所以这个事件将会交给个他这个事件的activity去处理，就会回到上一层，调用activity的**onTouchEvent(MotionEvent ev)**，这里也说明了之前activity 中的为什么没意义了，以为它没有上层了，事件就失去意义了，就相当于被消耗了。



我们回到viewGroup层，在这一层，我们会在**dispatchTouchEvent(MotionEvent ev)**中调用**onInterceptTouchEvent(MotionEvent ev)**用于判断这个viewGroup是否拦截事件，听上去这个功能和第一个函数重复了，但是，这个调用时在第一个函数中的啊，怎么会重复呢==，返回值很好理解，false不拦截交给他的子view，true拦截，调用自己的**onTouchEvent(MotionEvent ev)**，这个函数返回true表示事件被消耗，如果是false**那么事件就又将会交给上层的view处理**。

就这样了一层一层下去，中间可能会有多层的viewGroup，最底层就是一个简单的view了，view的过程结合上面就很好理解了。

（这篇纯粹是个人理解，我想尽可能通俗一点，奈何语言表达能力真的不好，努力改进中，图片理解好基本上就懂了一大半了，还不懂的可以去简书或者其他地方找大佬的看看）