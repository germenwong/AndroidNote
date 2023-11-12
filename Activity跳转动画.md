### 简介

统默认的Activity之间的切换都是带动画效果的（右进右出），但是有的时候我们不满足于系统的默认动画效果，就需要自定义属于我们自己的跳转动画。

**四种基本动画：**translate（平移动画），rotate（旋转动画），scale（缩放动画），alpha（透明度动画），因为Activity的切换动画无非就是这四种动画的简单组合





### 实现方式

自定义动画有两种实现方式：==java代码==和==xml配置==。这两种方式都可以实现Activity自定义切换动画的效果，如果所有的Activity都是一样的效果那么只要在xml中配置即可，但是如果有特殊的Activity需要特殊指定的话那么就在java代码中为其指定。

* 第一种：主要是 `overridePendingTransition()` 这个方法，如果想要自定义某个Activity切换的动画效果只需要在完成==Activity切换的代码之后==加上这一句代码，然后==传入两个动画效果的资源==即可。

> 注意:
>
> 1. 方法的第一个参数是跳转过程中将要显示的Activity的入场动画，第二个参数是即将隐藏的Activity的退场动画。
> 2. 这句代码一定要在跳转代码之后加入，一般写在 startActivity() 或者 finish() 调用之后。



* 第二种：在你的 `styles.xml` 文件中的主题中加入以下四项即可：

```xml
<item name="android:activityOpenEnterAnimation">@anim/anim_1</item>
<item name="android:activityOpenExitAnimation">@anim/anim_2</item>
<item name="android:activityCloseEnterAnimation">@anim/anim_3</item>
<item name="android:activityCloseExitAnimation">@anim/anim_4</item>
```

> 解释：
>
> 1. activityOpenEnterAnimation：新Activity入栈时的入场动画
> 2. activityOpenExitAnimation：旧Activity的退场动画
> 3. activityCloseEnterAnimation：旧Activity的入场动画
> 4. activityCloseExitAnimation：新Activity出栈时的退场动画