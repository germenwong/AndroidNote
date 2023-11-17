## 什么是广播？

Android 系统会在发生各种系统事件时发送广播，例如系统启动或设备开始充电时。再比如，应用可以发送自定义广播来通知所有同意接收相关事件的应用。说白广播就是（系统跟应用）或者（应用跟应用）的一种通信机制。

广播的类型：



------



## 如何注册广播接收器？

注册类型分为两种：

* 静态注册：在清单文件里面声明广播接收器，可以理解为交给系统去处理，不需要手动注销在运行的整个过程都会一直存在。
* 动态注册：一般写在 `Activity` 内部，`onCreate()` 方法进行注册，`onDestroy()` 方法进行反注册，当注册的 `Activity` 销毁后，它也会随即销毁。

------



## 示例（系统->应用）

通过注册飞行模式接收器接收系统发出的广播事件，当用户对飞行模式进行开关，我们也能实时监听到。

> 此示例是介绍系统与应用之间通信

### 1. 创建广播接收器

在 `onReceive()` 方法中过滤意图类型，当接收到飞行模式变更的意图时，通过全局设置获取当前的飞行模式的状态，然后在控制台打印。

```kotlin
class AirPlaneModeReceiver : BroadcastReceiver() {
	override fun onReceive(context: Context?, intent: Intent?) {
		if (intent?.action == Intent.ACTION_AIRPLANE_MODE_CHANGED) {
			val isTurnOn = Settings.Global.getInt(
				context?.contentResolver,
				Settings.Global.AIRPLANE_MODE_ON
			) != 0
			println("飞行模式是否开启？ $isTurnOn")
		}
	}
}
```

### 2. 注册广播接收器

创建完成广播接收器后是没有任何作用的，我们还需要在应用中注册它。

```kotlin
class MainActivity : ComponentActivity() {
    //飞行模式广播接收器（动态）
	private val airPlaneModeReceiver = AirPlaneModeReceiver()
    
    override fun onCreate(savedInstanceState: Bundle?) {
		super.onCreate(savedInstanceState)
		//注册接收器（接收器、意图类型）
		registerReceiver(
			airPlaneModeReceiver,
		    IntentFilter(Intent.ACTION_AIRPLANE_MODE_CHANGED)
		)
        
        ...
    }
        
        
	override fun onDestroy() {
		super.onDestroy()
		//反注册接收器
		unregisterReceiver(airPlaneModeReceiver)
	}
}
```

<img src="Broadcasts & BroadcastReceivers/飞行模式.gif" alt="飞行模式" style="zoom: 67%;" />

------



## 示例（应用->应用）

由于需要两个应用，我们需要另外新建应用用于发起广播。

>  此示例是介绍应用与应用之间通信

### 1. 自定义广播

在新的应用项目中创建自定义广播，然后通过按钮发送出去。

```kotlin
class MainActivity : ComponentActivity() {
	override fun onCreate(savedInstanceState: Bundle?) {
	    super.onCreate(savedInstanceState)
	    setContent {
			SendReceiverBroadcastReceiverTheme {
				Column(
				      modifier = Modifier.fillMaxSize(),
				      horizontalAlignment = Alignment.CenterHorizontally,
				      verticalArrangement = Arrangement.Center
				) {
					//发送一个自定义广播，让注册了 CUSTOM_ACTION 的接收器接收消息
					Button(onClick = {
						sendBroadcast(Intent("CUSTOM_ACTION"))
					}) {
						Text(text = "发送广播")
					}
				}
			}
		}
    }
}
```

### 2. 创建广播接收器

检查接收到的意图是否与发送的相同

```kotlin
class CustomReceiver : BroadcastReceiver() {
	override fun onReceive(context: Context?, intent: Intent?) {
		if (intent?.action == "CUSTOM_ACTION") {
			println("接收到了来自 CUSTOM_ACTION 的广播")
		}
	}
}
```

### 3. 注册广播接收器

与上面的方法一样，在 `Activity` 中动态注册广播。

```kotlin
class MainActivity : ComponentActivity() {
	private val customReceiver = CustomReceiver()
    
    override fun onCreate(savedInstanceState: Bundle?) {
		super.onCreate(savedInstanceState)
        //注册接收器
		registerReceiver(
			customReceiver,
		    IntentFilter("CUSTOM_ACTION")
		)
        
        ...
    }
        
        
	override fun onDestroy() {
		super.onDestroy()
		//反注册接收器
		unregisterReceiver(customReceiver)
	}
}
```

<img src="Broadcasts & BroadcastReceivers/自定义广播.gif" alt="自定义广播" style="zoom:67%;" />
