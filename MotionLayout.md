## MotionLayout

### 简介

`MotionLayout`是一个 Google 官方出品用于制作 Android 中的过渡动画的框架。用来它就能轻松的做出一些较为复杂的动画效果。属于`ConstraintLayout`的子类

### 开始使用

***添加依赖***

```java
dependencies {
	implementation 'androidx.constraintlayout:constraintlayout:2.0.0-beta1'
}
```

由于 MotionLayout 类继承自 ConstraintLayout 类，因此可以在布局中使用 MotionLayout ==替换掉== ConstraintLayout
***MotionLayout 与 ConstraintLayout 不同的是:***
MotionLayout 需要链接到一个`MotionScene` 文件。使用 MotionLayout 的 `app:layoutDescription`属性将 MotionLayout 链接到一个`MotionScene` 文件。

为了将布局信息与运动描述分开，每个 `MotionLayout` 都引用一个==单独==的 `MotionScene`。请注意，`MotionScene` 中的定义优先于 `MotionLayout` 中的任何类似定义。

------

首先创建两个场景的布局存放在`layout`文件里，然后在`res/xml`目录下创建`MotionScene` 文件，在里面创建一个`activity_motion_scene.xml`文件。

***1、创建场景一的布局文件代码如下：***

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.motion.widget.MotionLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/motionLayout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:showPaths="true"
    app:layoutDescription="@xml/activity_motion_scene">

    <ImageView
        android:id="@+id/image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:src="@drawable/a"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />


</androidx.constraintlayout.motion.widget.MotionLayout>
```

***2、创建场景二的布局文件代码如下：***

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.motion.widget.MotionLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/motionLayout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:showPaths="true"
    app:layoutDescription="@xml/activity_motion_scene">

    <ImageView
        android:id="@+id/image"
        android:layout_width="48dp"
        android:layout_height="48dp"
        android:src="@drawable/a"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.motion.widget.MotionLayout>
```

***3.创建activity_motion_scene.xml代码如下：***

```xml
<?xml version="1.0" encoding="utf-8"?>
<MotionScene xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <Transition
        app:constraintSetStart="@layout/activity_main_scene1"
        app:constraintSetEnd="@layout/activity_main_scene2"
        app:duration="1000">

        <OnClick
            app:clickAction="toggle"
            app:targetId="@id/image" />

    </Transition>
</MotionScene>
```

效果如下：

<img src="https://upload-images.jianshu.io/upload_images/16627472-1e5ca686d2aee8e6.gif" />



### MotionScene的标签属性

***1、< Transition > 包含运动的基本定义***

| 属性名                    | 说明           |
| ------------------------- | -------------- |
| motion:constraintSetStart | 运动端点的起点 |
| motion:constraintSetEnd   | 运动端点的终点 |
| motion:duration           | 运动的时间     |

***1.1、< OnClick > 点击触发运动***

| 属性名          | 说明                    |
| --------------- | ----------------------- |
| app:targetId    | 设置触发过渡的控件的 ID |
| app:clickAction | 设置点击时执行的动作    |

>  **clickAction **里共有（五个）属性可选
>
> `toggle`：在 Start 场景和 End 场景之间循环的切换
>  `transitionToEnd`：过渡到 End 场景
>  `transitionToStart`：过渡到 Start 场景
>  `jumpToEnd`：跳到 End 场景（不执行过渡动画）
>  `jumpToStart`：跳到 Start 场景（不执行过渡动画）

***1.2、< OnSwipe > 拖拽来控制运动***

| 属性名                 | 说明                                                         |
| ---------------------- | ------------------------------------------------------------ |
| motion:touchAnchorId   | 设置拖动操作要关联到的对象，让触摸操作看起来像是在拖动，这个对象的由 `app:touchAnchorSide` 属性指定的那个边。 |
| motion:touchAnchorSide | 设置触摸操作将会拖动对象的哪一边 `top,left,right,bottom`     |
| motion:dragDirection   | 设置拖动的方向（只有设置了 `app:touchAnchorId` 属性后该属性才有效）`dragUp，dragDown，dragLeft，dragRight` |
| app:maxVelocity        | 【浮点值】设置动画在拖动时的最大速度（单位：像素每秒 px/s）  |
| app:maxAcceleration    | 【浮点值】设置动画在拖动时的最大加速度（单位：像素每二次方秒 px/s^2） |

***注意:***可以同时设置 <OnClick> 与 <OnSwipe> ，或者都不设置，而是使用代码来触发过渡。还可以在 <Transition> 元素中设置多个 <OnClick>，每个  <OnClick> 都可以关联到一个不同的控件上。虽然 <Transition> 元素中也可以设置多个 <OnSwipe>，但是后面的 <OnSwipe> 会替换掉前面的 <OnSwipe>，最终使用的是最后一个 <OnSwipe>。



***2、< ConstraintSet >***

子元素定义一个场景约束集，并在 <ConstraintSet> 元素中使用 <Constraint> 元素定义单个 View 的属性约束。

| 属性名     | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| android:id | 设置当前约束集的 id。这个 id 值可被 <Transition> 元素的 app:constraintSetStart 或者 app:constraintSetEnd 引用 |

***2.1、< Constraint >*** 

| 属性名                   | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| android:id               | 当前约束关联到的那个 View 的 id                              |
| app:transitionEasing     | 定义动画到此点时使用的缓动曲线，该值可以是一个字符串（例如 "curve(1.0,0,0,1.0)"）或者（standard：标准，accelerate：加速，decelerate：减速，linear：线性） |
| app:transitionPathRotate | 【浮点值】相对于所采用的路径旋转对象（弧形路径/关键帧 相关） |
| app:drawPath             | 绘制过渡动画路径（调试用，关键帧相关）                       |
| app:progress             | 【浮点值】在关联的 View 上调用 setProgress(float) 方法（用于与嵌套的 ConstraintLayout 交互） |



### 自定义属性

`MotionLayout` 控件只会检测标准属性和`ConstraintLayout` 属性这类==布局相关的属性变动==，对于其他的属性变动，如 View 的背景颜色变动是无法检测出来的，因此就需要使用自定义属性

在 `<Constraint>`元素中使用 `<CustomAttribute>` 子元素来指定==自定义属性==

***< CustomAttribute >元素属性说明：***

`app:attributeName` 属性用来指定自定义属性的名字（例如 "`backgroundColor`"）

***注意：***关联的 View 必须要有一对与这个名字相关的 `getter/setter`方法（例如 getBackgroundColor()/setBackgroundColor(int color)）

| 属性名                       | 说明           |
| ---------------------------- | -------------- |
| app:customColorValu          | （颜色类型）   |
| app:customColorDrawableValue | （颜色类型）   |
| app:customIntegerValue       | （整数类型）   |
| app:customFloatValu          | （浮点类型）   |
| app:customStringValue        | （字符串类型） |
| app:customDimensio           | （尺寸类型）   |
| app:customPixelDimension     | （尺寸类型）   |
| app:customBoolean            | （布尔类型）   |

