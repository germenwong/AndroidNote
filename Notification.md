## Notification

### 简介

Android系统中比较有特色的一个功能，当某个应用程序希望向用户发出一些提示消息，而该应用程序又不再前台运行时，可以借助通知来实现。

首先会表现为一个图标的形式，当用户向下滑动时，展示除通知具体的内容



### 通知渠道

就是==每条通知都要属于一个对应的渠道==。每个应用程序都可以自由地创建当前应用拥有哪些通知渠道，但是这些通知渠道的控制权掌握在用户手上。用户可以自由地选择这些通知渠道的==重要程度==，是否响铃、是否震动或者是否要关闭这个渠道的通知。

首先需要一个NotificationManager来管理通知，可以调用Context中getSystemService()方法获取。getSystemService()方法接收一个字符串参数，用于确定获取系统的哪个服务。传入Context.NOTIFICATION_SERVICE即可

```java
NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
```

***如何创建通知渠道，代码如下：***

接下来要使用`NotificationChannel`类构建一个通知渠道，并调用`NotificationManager.createNotificationChannel()`方法完成创建。由于`NotificationChannel`类和`NotificationManager.createNotificationChannel()`方法都是Android8.0新增的API，因此我们使用中需要版本判断。

```java
// 创建渠道
if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.O) {
	//参数一：渠道ID
	//参数二：渠道名称
	//参数三：重要等级
	//IMPORTANCE_NONE：关闭通知
	//IMPORTANCE_MIN：开启通知, 不会弹出, 没有提示音, 状态栏中无显示
	//IMPORTANCE_LOW：开启通知, 不会弹出, 不发出提示音, 状态栏中显示
	//IMPORTANCE_DEFAULT：开启通知, 不会弹出, 发出提示音, 状态栏中显示
	//IMPORTANCE_HIGH：开启通知, 会弹出, 发出提示音, 状态栏中显示
	NotificationChannel channel = new NotificationChannel("channelId", "渠道1", 			NotificationManager.IMPORTANCE_DEFAULT);
	notificationManager.createNotificationChannel(channel);
}
```



### 基本用法

通知的用法比较灵活，可以在Activity里创建，也可以在广播接收器里创建，也可以在服务中创建。相比于广播接收器和服务，在Activity中创建通知的场景比较少，因为一般只有当程序进入到后台的时候才需要通知。

```java
 Notification notification = new NotificationCompat.Builder(MainActivity.this, "channelId")
	.setContentTitle("这是标题")
	.setContentText("这是通知内容...........")
	.setSmallIcon(R.drawable.ic_launcher_foreground)
	.setLargeIcon(BitmapFactory.decodeResource(getResources(), 			         R.drawable.ic_launcher_background))
	.build();

notificationManager.notify(1, notification);
```

`NotificationCompat.Builder()`构造器中接收两个参数：

- ***第一个参数：***Context
- ***第二个参数：***渠道ID，需要和我们创建渠道时指定的渠道ID相匹配才行

| 属性名             | 说明                   |
| ------------------ | ---------------------- |
| .setContentTitle() | 用于指定通知的标题     |
| .setContentText()  | 用于指定通知的正文内容 |
| .setSmallIcon()    | 用于设置通知的小图标   |
| .setLargeIcon()    | 用于设置通知的大图标   |

最终只需要调用`NotificationManager`的`notify()`方法就可以让通知显示出来，`notify()`方法接收两个参数：

- ***第一个参数：***id，要保证每个通知指定的id都是不同的
- ***第二个参数：***Notification对象



### 通知的详细信息

***1、如何解决点击消息没有任何反应，方法如下：***

如果想要实现点击后的效果，还需要使用`PendingIntent`来进行相应的代码设置。

`PendingIntent`和`Intent`有些类似，都可以指明某一个==意图==，都可以用来启动Activity、启动Service以及发送广播等。不同的是，`Intent`倾向于立即执行某个操作，而`PendingIntent`倾向于某个合适的时机执行某个操作，所以可以将`PengdingIntent`理解为==延迟执行==的`Intent`

主要提供几个静态方法用于获取`PendingIntent`的实例，例如：getActivity()方法、getBroadcast()方法和getService()方法。这几个方法接收的参数是相同的

- ***第一个参数：***Context

- ***第二个参数：***一般用不到，传入0即可

- ***第三个参数：***Intent对象，可以通过这个对象构建`PendingIntent`意图

- ***第四个参数：***用于确定PendingIntent的行为

  * ***FLAG_ONE_SHOT：*** 表示返回的PendingIntent仅能执行一次，执行完后自动消失

  - ***FLAG_NO_CREATE：*** 表示如果描述的PendingIntent不存在，并不创建相应的PendingIntent，而是返回NULL
  - ***FLAG_CANCEL_CURRENT：*** 表示相应的PendingIntent已经存在，则取消前者，然后创建新的PendingIntent
  - ***FLAG_UPDATE_CURRENT：*** 表示更新的PendingIntent，如果构建的PendingIntent已经存在，则替换它【常用】

并且`NotificationCompat.Builder()`这个构造器还可以连缀一个`setContentIntent()`方法，接收的参数就是`PendingIntent`对象，当用户点击这条通知时就会执行相应的逻辑

***2、点击消息后仍然没有消失，方法如下：***

- 第一种就是在`NotificationCompat.Builder()`这个构造器上在连缀一个`setAutoCancel()`方法
- 另外一种就是显示调用`NotificationManager.cancel()`方法将它取消

***方法 1：***

```java
Notification notification = new NotificationCompat.Builder(MainActivity.this, "channelId")
    .setContentTitle("This is content title")
    .setContentText("This is content text")
    .setSmallIcon(R.drawable.ic_launcher_background)
    .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.ic_launcher_background))
    .setContentIntent(pendingIntent)
    .setAutoCancel(true)    //true表示当点击这个通知的时候，通知自动取消
    .build();
```

***方法 2：***

```java
public class NotificationActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_notification);

        NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
        //这里传入的1和我们发送通知的1要一致
        notificationManager.cancel(1);
    }
}
```

***3、开启悬浮通知权限***

即允许在屏幕上方弹出通知，在AndroidManifest.xml文件中添加权限，权限代码如下：

```xml
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
```

