## 介绍

### SplashScreen

从 Android 12 开始，[`SplashScreen`](https://developer.android.com/reference/android/window/SplashScreen?hl=zh-cn) API 可让应用以动画形式启动，包括启动时的应用内动作、显示应用图标的启动画面，以及向应用本身的过渡。`SplashScreen` 是一个 [`Window`](https://developer.android.com/reference/android/view/Window?hl=zh-cn)，因此涵盖 [`Activity`](https://developer.android.com/reference/android/app/Activity?hl=zh-cn)。

### 启动页的工作原理

如果用户在应用进程未运行（[冷启动](https://developer.android.com/topic/performance/vitals/launch-time?hl=zh-cn#cold)）或未创建 `Activity`（[温启动](https://developer.android.com/topic/performance/vitals/launch-time?hl=zh-cn#warm)）的情况下启动应用，则会发生以下事件：

1. 系统使用您定义的主题和任何动画显示启动画面。
2. 当应用准备就绪后，系统会关闭启动画面并显示应用。

在[热启动](https://developer.android.com/topic/performance/vitals/launch-time?hl=zh-cn#hot)期间，启动画面从不显示。

------



## 使用教程

### 导入依赖库

```groovy
dependencies {
    implementation "androidx.core:core-splashscreen:1.0.0"
}
```

### 添加Logo以及配置动画

首先需要把我们的品牌 `Logo` 引入，这里我直接从官方资源 `Vector Asset` 随便引入一个，注意官方文档对于品牌`Logo` 是有说明的你们可以自行查看。

<img src="SplashScreen API/image-20231115232514232.png" alt="image-20231115232514232" style="zoom: 67%;" />

添加完成后在 `drawable` 目录下找到我们刚刚添加的 `Icon`，启动页面的 `Logo`可以是静态的也可以是动态，这里以最简单的旋转缩放动画为例，当应用启动时，`Logo` 旋转360°并且从0.0缩放到0.4。

因为有些 `Logo` 的设计它是不止一条 `path` 绘制的，我们需要把所有 `path` 包裹起来作为一个整体来设计动画，否则就会出现某一部分无运动。

```xml
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    ...
    android:viewportWidth="24"
    android:viewportHeight="24">

    <!--  整个logo设置为组  -->
    <group
        android:name="animationGroup"
        android:pivotX="12"
        android:pivotY="12">
        <path ... />
    </group>

</vector>

```

然后创建 `Animator` 资源文件，它相当于一个动画对象用来设置动画属性。

<img src="SplashScreen API/image-20231115233452671.png" alt="image-20231115233452671" style="zoom: 67%;" />

官方建议启动页的动画时长最后不要超过1000毫秒（1秒钟），示例代码中我设置了运行时长为1秒以及旋转和缩放动画，当启动时 `Logo` 从0°旋转360°，并且从 X轴 Y轴缩放到0.4倍。

```xml
<?xml version="1.0" encoding="utf-8"?>
<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="1000">
    <propertyValuesHolder
        android:propertyName="rotation"
        android:valueFrom="0.0"
        android:valueTo="360.0"
        android:valueType="floatType" />

    <propertyValuesHolder
        android:propertyName="scaleX"
        android:valueFrom="0.0"
        android:valueTo="0.4"
        android:valueType="floatType" />

    <propertyValuesHolder
        android:propertyName="scaleY"
        android:valueFrom="0.0"
        android:valueTo="0.4"
        android:valueType="floatType" />

</objectAnimator>
```

接下来 `Logo` 和 动画属性都有了，就需要动画矢量把他们组合到一起，官方也是很贴心在右侧可以播放动画来预览我们设置的效果。

```xml
<?xml version="1.0" encoding="utf-8"?>
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/logo">

    <!-- 设置目标值，引用logo和动画属性 -->
    <target
        android:animation="@animator/logo_animator"
        android:name="animationGroup"/>

</animated-vector>
```

//动画预览

打开 `values/theme.xml`文件，自定义启动主题，它用于替换掉 `MainActivity` 原有的默认主题并且在启动完成之后自动切换回我们默认的主题。

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <!-- 默认主题 -->
	<style 
		name="Theme.AnimationsSplashScreenApi"
        parent="android:Theme.Material.Light.NoActionBar" />
    
    
    <!-- 启动主题 -->
    <style name="Theme.App.Starting" parent="Theme.SplashScreen">
    	<item name="android:windowSplashScreenAnimatedIcon">
        	@drawable/animated_logo
        </item>
        <item name="postSplashScreenTheme">@style/Theme.AnimationsSplashScreenApi</item>
    </style>
</resources>
```

前往注册清单引用该主题

```xml
<application
    ...
    android:theme="@style/Theme.App.Starting"
    tools:targetApi="31">
    <activity
       ...
        android:theme="@style/Theme.App.Starting">
       ...
    </activity>
</application>
```

最后一步在 `MainActivity` 中调用 `installSplashScreen` 方法并配置相应的设置。注意：该方法一定要在 `onCreate()` 方法之前执行。

```kotlin
class MainActivity : ComponentActivity() {

      private val viewModel by viewModels<MainViewModel>()

      override fun onCreate(savedInstanceState: Bundle?) {
            super.onCreate(savedInstanceState)

            installSplashScreen().apply {
                  // 设置停留在闪屏页上的条件，当条件为false的时候会一直停留反之进入主界面
                  setKeepOnScreenCondition {
                        !viewModel.isReady.value
                  }
                  //设置退出动画监听器，实现动画缩小并进入主界面
                  setOnExitAnimationListener { screen ->
                        val zoomX = ObjectAnimator.ofFloat(
                              screen.iconView,
                              View.SCALE_X,
                              0.4f,
                              0.0f
                        ).apply {
                              interpolator = OvershootInterpolator()
                              duration = 500L
                              doOnEnd { screen.remove() }
                        }

                        val zoomY = ObjectAnimator.ofFloat(
                              screen.iconView,
                              View.SCALE_Y,
                              0.4f,
                              0.0f
                        ).apply {
                              interpolator = OvershootInterpolator()
                              duration = 1000L
                              doOnEnd { screen.remove() }
                        }

                        zoomX.start()
                        zoomY.start()
                  }
            }

            setContent {
                  AnimationsSplashScreenApiTheme {
                        Surface(
                              modifier = Modifier.fillMaxSize(),
                              color = MaterialTheme.colorScheme.background
                        ) {}
                  }
            }
      }
}
```



