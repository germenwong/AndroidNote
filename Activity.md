## Activity

### 必知必会

**Activity**是Android的四大组件之一，Activity是一种能够显示用户界面的组件，用户通过和Activity交互完成相关操作。 

一个应用中可以包含0个或多个 Activity，但不包含任何 Activity 的应用程序是无法被用户看见的。

> Tips
>
> **1.** Activity用于显示用户界面，用户通过Activity交互完成相关操作
>
> **2.** 一个App允许有多个Activity

<img src="https://songyubao.com/book/primary/activity/Activity%E5%BF%85%E7%9F%A5%E5%BF%85%E4%BC%9A.png"/>



### Activity生命周期

<img src="https://songyubao.com/book/primary/activity/android_activity_lifecycle.png"/>

#### onCreate()

该方法会在 Activity 第一次创建时进行调用，在这个方法中通常会做 Activity 初始化相关的操作，例如：加载布局、绑定事件等。

#### onStart()

这个方法会在 Activity 由不可见变为可见的时候调用，但是还不能和用户进行交互。

#### onResume()

表示Activity已经启动完成，进入到了前台，可以同用户进行交互了。

#### onPause()

这个方法在系统准备去启动另一个 Activity 的时候调用。可以在这里释放系统资源，动画的停止，不宜在此做耗时操作。

#### onStop()

当Activity不可见的时候回调此方法。需要在这里释放全部用户使用不到的资源。可以做较重量级的工作，如对注册广播的解注册，对一些状态数据的存储。此时Activity还不会被销毁掉，而是保持在内存中，但随时都会被回收。通常发生在启动另一个Activity或切换到后台时

#### onDestroy()

Activity即将被销毁。此时必须主动释放掉所有占用的资源。

#### onRestart()

这个方法在 Activity 由停止状态变为运行状态之前调用，也就是 Activity 被重新启动了（APP切到后台会进入onStop(), 再切换到前台时会触发onRestart()方法）



### Activity组件注册

四大组件需要在AndroidManifest文件中配置否则无法使用，类似Activity无法启动，

一般情况下： 在新建一个activity后，为了使intent可以调用此活动，我们要在`androidManifest.xml`文件中添加一个标签，标签的一般格式如下：

```xml
<activity
	android:name=".MainActivity"
	android:label="@string/app_name">
	<intent-filter>
	    <action android:name="android.intent.action.MAIN" />
	    <category android:name="android.intent.category.LAUNCHER" />
	</intent-filter>
</activity>
```

- android:name是对应Activity的类名称
- android:label是Activity标题栏显示的内容. 现已不推荐使用
- 是意图过滤器. 常用语隐式跳转
- 是动作名称，是指intent要执行的动作
- 是过滤器的类别 一般情况下，每个 中都要显示指定一个默认的类别名称，即`<category android:name="android.intent.category.DEFAULT" />`

但是上面的代码中没有指定默认类别名称，这是一个例外情况，因为其 中的是"android.intent.action.MAIN"，意思是这个Activity是应用程序的入口点，这种情况下可以不加默认类别名称。



### Activity启动与参数传递

> Tips 
>
> 在Android中我们可以通过下面两种方式来启动一个新的Activity,注意这里是怎么启动，分为显示启动和隐式启动！

**1. 显式启动：通过包名来启动，写法如下：**

**最常见的：**startActivity

```kotlin
//常规跳转
val intent = Intent(MainActivity.this,SecondActivity.class)
startActivity(intent);

//携带参数启动新Activity
val intent = Intent(MainActivity.this,SecondActivity.class)
intent.putExtra("extra_data", "extra_data")
intent.putExtra("extra_int_data", 100)
startActivity(intent);
```

**期待从目标页获取数据**：startActivityForResult---->比如启动相册获取图片

```kotlin
//假设从A--->B页面，以startActivityForResult方式启动
val intent = Intent(MainActivity.this,SecondActivity.class)
startActivityForResult(intent,100);

//如果B页面返回时，调用了 
setResult(Activity.RESULT_OK,resultIntent)
finish()

//则A页面会回调下面的方法,在该回调里可以拿到返回的数据data
onActivityResult(requestCode: Int, resultCode: Int, data: Intent?)
```

**2. 隐式启动**

隐式 Intent 要比显示 Intent 含蓄的多，他并不明确指定要启动哪个 Activity，而是通过指定 `action` 和 `category` 的信息，让系统去分析这个 `Intent`，并找出合适的 Activity 去启动。

```xml
 <activity android:name=".SecondActivity">
     <intent-filter>
         <action android:name="com.example.firstapp.action.SecondActivity" />
         <category android:name="com.example.firstapp.category.SecondActivity" />
         <category android:name="android.intent.category.DEFAULT" /> // 一定要有
     </intent-filter>
</activity>
```

```kotlin
val intent = Intent()
intent.setAction("com.example.firstapp.action.SecondActivity")
intent.setCategory("com.example.firstapp.category.SecondActivity")

intent.putExtra("extra_data", "extra_data")
intent.putExtra("extra_int_data", 100)
startActivity(intent);

startActivityForResult(intent,100);
```



### 系统提供常见Activity

#### 拨打电话

给移动客服10086拨打电话

```kotlin
Uri uri = Uri.parse("tel:10086");
Intent intent = new Intent(Intent.ACTION_DIAL, uri);
startActivity(intent);
```

#### 发送短信

 给10086发送内容为“Hello”的短信

```kotlin
Uri uri = Uri.parse("smsto:10086");
Intent intent = new Intent(Intent.ACTION_SENDTO, uri);
intent.putExtra("sms_body", "Hello");
startActivity(intent);
```

#### 打开浏览器:

打开baidu主页

```kotlin
Uri uri = Uri.parse("http://www.baidu.com");
Intent intent  = new Intent(Intent.ACTION_VIEW, uri);
startActivity(intent);
```

#### 多媒体播放:

```kotlin
Intent intent = new Intent(Intent.ACTION_VIEW);
Uri uri = Uri.parse("file:///sdcard/foo.mp3");
intent.setDataAndType(uri, "audio/mp3");
startActivity(intent);
```

#### 打开摄像头拍照:

```kotlin
Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE); 
startActivityForResult(intent, 0);

//在Activity的onActivityResult方法回调中取出照片数据
Bundle extras = intent.getExtras(); 
Bitmap bitmap = (Bitmap) extras.get("data");
```

#### 从图库选图并剪切

```kotlin
// 获取并剪切图片
Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
intent.setType("image/*");
intent.putExtra("crop", "true"); // 开启剪切
intent.putExtra("aspectX", 1); // 剪切的宽高比为1：2
intent.putExtra("aspectY", 2);
intent.putExtra("outputX", 20); // 保存图片的宽和高
intent.putExtra("outputY", 40); 
intent.putExtra("output", Uri.fromFile(new File("/mnt/sdcard/temp"))); // 保存路径
intent.putExtra("outputFormat", "JPEG");// 返回格式
startActivityForResult(intent, 0);

>>>>  在Activity的onActivityResult方法中去读取保存的文件
```

#### 剪切指定图片文件

```kotlin
Intent intent = new Intent("com.android.camera.action.CROP"); 
intent.setClassName("com.android.camera", "com.android.camera.CropImage"); 
intent.setData(Uri.fromFile(new File("/mnt/sdcard/temp"))); 
intent.putExtra("outputX", 1); // 剪切的宽高比为1：2
intent.putExtra("outputY", 2);
intent.putExtra("aspectX", 20); // 保存图片的宽和高
intent.putExtra("aspectY", 40);
intent.putExtra("scale", true);
intent.putExtra("noFaceDetection", true); 
intent.putExtra("output", Uri.parse("file:///mnt/sdcard/temp")); 
startActivityForResult(intent, 0);

>>>>  在Activity的onActivityResult方法中去读取保存的文件
```

#### 进入手机的无线网络设置页面

```kotlin
// 进入无线网络设置界面（其它可以举一反三）  
Intent intent = new Intent(android.provider.Settings.ACTION_WIRELESS_SETTINGS);  
startActivityForResult(intent, 0);
```



### Activity四种启动模式

#### standard

默认值，多实例模式。每启动一次，都会创建一个新的Activity实例。

启动的生命周期为：onCreate()->onStart()->onResume()

<img src="https://songyubao.com/book/primary/activity/standard.png">

#### singleTop

栈顶复用模式

如果任务栈顶已经存在需要启动的目标Activity，则直接启动，并会回调onNewIntent()方法，生命周期顺序为： onPause() ->onNewIntent()->onResume()

如果任务栈上顶没有需要启动的目标Activity，则创建新的实例，此时生命周期顺序为： onCreate()->onStart()->onResume()

两种情况如下图，从图中可以看出，此模式下还是会出现多实例，只要启动的目标Activity不在栈顶的话。

<img src="https://songyubao.com/book/primary/activity/singletop.png">

#### singleTask

栈内复用模式，一个任务栈只能有一个实例。

有几种情况：

- 当启动的Activity目标任务栈不存在时，则以此启动Activity为根Activity创建目标任务栈，并切换到前面
- D为singleTask模式

<img src="https://songyubao.com/book/primary/activity/singletask.png"/>

当启动的Activity存在时，则会直接切换到Activity所在的任务栈，并且任务栈中在Activity上面的所有其他Activity都出栈（调用destroy()），此时启动的Activity位于任务栈顶，并且会回调onNewIntent()方法。

<img src="https://songyubao.com/book/primary/activity/singletask2.png">

#### singleInstance

singleInstance名称是单例模式，即App运行时，该Activity只有一个实例。既然只有一个，那么也就说明很重要、很特殊，我们需要将其“保护起来”。单例模式的“保护措施”是**将其单独放到一个任务栈中**。。

<img src="https://songyubao.com/book/primary/activity/singletask.png">





### Intent FLag设定启动模式

除了可以在manifest中设置Activity的启动模式，也可以通过设置Intent的flag标识来设定Activity的启动模式。

常用的有：FLAG_ACTIVITY_NEW_TASK，FLAG_ACTIVITY_SINGLE_TOP，FLAG_ACTIVITY_CLEAR_TOP

#### FLAG_ACTIVITY_NEW_TASK

启动Activity时，如果不存在Activity的实例，则会以此Activity为根Activity创建新的任务栈，如果存在的话则直接切换到对应的Activity实例，并回调onNewIntent()方法。相当于“singleTask”启动模式。

#### FLAG_ACTIVITY_SINGLE_TOP

相当于“singleTop”模式

#### FLAG_ACTIVITY_CLEAR_TOP

设置此标识的Activity在启动时，如果当前的任务栈内存在此Activity实例，则跳转到此实例，并清除掉在此实例上面的所有Activity实例，此时此Activity实例位于任务栈的栈顶 