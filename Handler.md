## Handler

### 简介

Handler是Android提供用于==更新ui==的一套==消息处理机制==<br>主要作用有：1、在新启动的线程中发送消息<br>						2、在主线程中获取，处理消息

------





### 常用类

* ***Handler：***负责发送消息、处理消息，实现线程间通信
* ***Message：***消息载体，存放信息
* ***MessageQueue：***消息队列、先进先出
* ***Looper：***负责循环地从队列中获取消息交给 Handler

------





### 简单使用

```java
//1、实例化Handler
private Handler handler=new Handler(){
	//3、由Handler对象接收消息，并且处理
	@Override
    public void handlerMessage(@NonNull Message msg){
		super.handlerMessage(msg);
        //一般在这里进行更新ui的操作
        txt.setText(str);
    }
};


//2、在子线程里面发送消息
new Thread(){
    @Override
    public void run(){
        //网络请求的操作...
        
        handler.sendEmptyMessage(1);
    }
}.start();
```

------





### Message

Message是在线程之间传递的消息，它可以在内部携带少量的信息，用于在不同线程之间交换数据。

***常用属性：***

* what属性：用于区分Handler发送消息的线程来源
* arg1属性：用于子线程需要向主线程传递整型数据
* obj属性：可传任意对象（Object）

***属性使用：***

```java
private Handler handler=new Handler(){
	@Override
    public void handlerMessage(@NonNull Message msg){
		super.handlerMessage(msg);
		if(msg.what==1){
			//...
        }else if(msg.arg1==111){
            //....
        }else if(msg.obj==student){
            //....
        }
    }
};


new Thread(){
    @Override
    public void run(){
		Message msg=new Message();
        msg.what=1;
        msg.arg1=111;
        msg.obj=student;
    }
}.start();
```

------





### MessageQueue

MessageQueue就是消息队列的意思，主要用于存放所有通过Handler发送过来的消息。这部分消息会一直存放于消息队列中，等待被处理。每个线程中只会有一个MessageQueue对象。

------





### Looper

Looper是每个线程中的MessogeQueue的管家，调用Looper的`loop()`方法后，就会进入到一个无限循环当中，然后每当MessogeQueue中存在一条消息,Looper就会将这条消息取出，并将它传递到Handler的`handleMessoge()`方法中。每个线程只有一个Looper对象。

```java
new Thread(){
    @Override
    public void run(){
        super.run();
		Looper.prepare();//准备，手动开启消息循环。如果是主线程，系统会自动开启消息循环
        handler2=new Handler();
        @Override
    	public void handlerMessage(@NonNull Message msg){
			super.handlerMessage(msg);
			Log.d("TAG","这是由主线程传递过来的Message"+msg.what);
    	}
    }
}.start();
```

------





### send方法

* ***sendEmptyMessage(int)***：发送一个空的消息

```java
handler.sendEmptyMessage(0);
```

* ***sendMessage(Message)：***将有数据的Message对象到队列中，等待处理

```java
Message message = new Message();
message.what = 1;
message.obj = "hgm";
handler.sendMessage(message);
```

* ***sendMessageAtTime(Message，long)：***在特定的时间消息队列

```java
Message message = new Message();
message.what = 1;
message.obj = "hgm";
handler.sendMessageAtTime(message, 100000);
```

* ***sendMessageDelayed(Message，long)：***延时发送消息队列

```java
Message message = new Message();
message.what = 1;
message.obj = "hgm";
handler.sendMessageDelayed(message, 3000);
```

------



### post方法

* ***post(Runnable)：***在Runnable做的任务发送到主线程中

```java
handler.post(new Runnable() {
    @Override
    public void run() {
        Log.e("TAG", "running");
    }
});
```

* ***postAtTime(Runnable，long)：***特定时间在主线程中执行Runnable中的任务

```java
handler.postAtTime(new Runnable() {
    @Override
    public void run() {
        Log.e("TAG", "running");
    }
}, 1000000);
```

* ***postDelayed(Runnable，long)：***延时在主线程中执行Runnable中的任务

```java
handler.postDelayed(new Runnable() {
    @Override
    public void run() {
        Log.e("TAG", "running");
    }
}, 3000);
```

------





### 运行机制

> Handler 发送消息(Message) 到消息队列中(MessageQueue) ，Looper 通过无限循环的方式不断向消息队列中获取新添加的消息 然后交给 Handler ，最终消息回到 Handler 中被处理

![image-20220122193305828](C:/Users/Hello word/Downloads/android-note-master/消息处理机制/Handler消息处理.assets/image-20220122193305828.png)
