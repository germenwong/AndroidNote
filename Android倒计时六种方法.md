### 实现倒计时六种方法



#### 1、CountDownTimer的实现

android.os包下面的 CountDownTimer 类的使用。内部实现使用了 Handler 进行封装。

```kotlin
//倒计时的方式一
fun countDownTimer() {
    var num = 60
    
    timer = object : CountDownTimer((num + 1) * 1000L, 1000L) {
        override fun onTick(millisUntilFinished: Long) {
            YYLogUtils.w("当时计数：" + num)
            if (num == 0) {
                YYLogUtils.w("重新开始")
                num = 60
            } else {
                num--
            }
        }

        override fun onFinish() {
            YYLogUtils.w("倒计时结束了..." + num)
        }
    }
    timer?.start()//启动
}


private var timer: CountDownTimer? = null

//销毁时关闭，以免内存泄漏
override fun onDestroy() {
    super.onDestroy()
    timer?.cancel()
}
```

#### 2、Handler的实现

我们可以直接使用Handler的延时发送消息实现倒计时。

当然另一种做法是使用 Runnable 来实现。

```kotlin
private var handlerNum = 60

private val mHandler = object : Handler(Looper.getMainLooper()) {
    override fun handleMessage(msg: Message) {
        when (msg.what) {
            1 -> {
                if (handlerNum > 0) {
                    handlerNum--
                    YYLogUtils.w("当时计数：" + handlerNum)

                    countDownHander()
                } else {
                    stopCountDownHander()
                }
            }
        }
    }
}

fun countDownHander() {
    mHandler.sendEmptyMessageDelayed(1, 1000)
}

fun stopCountDownHander() {
    mHandler.removeCallbacksAndMessages(null)
}

override fun onDestroy() {
    super.onDestroy()
    stopCountDownHander()
}
```

```java
Handler handler = new Handler();

Runnable runnable = new Runnable() {
    @Override
    public void run() {
        recLen++;
        txtView.setText("" + recLen);
        handler.postDelayed(this, 1000);
    }

public void test(){
    handler.postDelayed(runnable, 1000);
}
```

#### 3、Time、TimeTask的实现

以上是Android的倒计时方案，其实Java的Api也是支持倒计时实现的，比如 Timer 配合 TimerTask 就可以实现简单的倒计时。

```kotlin
fun countDownTimer2() {
	var num = 60
	val timer = Timer()
	val timeTask = object : TimerTask() {
	    override fun run() {
	        num--
	        YYLogUtils.w("当时计数：" + num)
	        if (num < 0) {
	            timer.cancel()
	        }
	    }
	}
	
	timer.schedule(timeTask, 1000, 1000)
}
```

#### 4、Theard

我们可以通过Thread的sleep方法来实现倒计时，不过==由于是子线程我们不能更新UI，所以还是需要配合Handler实现==。

```kotlin
private var mThread: Thread = Thread(this)
private var mflag = false
private var mThreadNum = 60

override fun run() {
    while (mflag && mThreadNum >= 0) {
        try {
            Thread.sleep(1000)
        } catch (e: InterruptedException) {
            e.printStackTrace()
        }

        val message = Message.obtain()
        message.what = 1
        message.arg1 = mThreadNum
        handler.sendMessage(message)
        mThreadNum--
    }
}


private val handler = Handler(Looper.getMainLooper()) { msg ->
    if (msg.what == 1) {
        val num = msg.arg1
        //由于需要主线程显示UI，这里使用Handler通信
        YYLogUtils.w("当时计数：" + num)
    }
    true
}


//开启倒计时
fun countDownThread() {
    if (!mThread.isAlive) {

        mflag = true

        if (mThread.state == Thread.State.TERMINATED) {
            mThread = Thread(this@DemoCountDwonActivity)
            if (mThreadNum == -1) mThreadNum = 60
            mThread.start()
        } else {
            mThread.start()
        }
    } else {
        mflag = false
    }

}

override fun onDestroy() {
    super.onDestroy()
    mflag = false
}
```

这里的销毁线程我没有使用stop方法，已经不推荐我们使用，我们使用flag来判断即可。

#### 5、框架RxJava

这样的线程并不是我们想要的，我们通常并不会直接new Thread 来进行一些逻辑操作，比如我们可能使用RxJava框架，通过操作符的方式来进行倒计时。

比我们倒计时4秒之后跳转页面的实现：

```kotlin
val SHOTDOWN_TIME = 4
val mDisposables : Disposable? = null
  Observable.interval(0, 1, TimeUnit.SECONDS)
          .take(SHOTDOWN_TIME.toLong())
          .map {
              return@map SHOTDOWN_TIME - it
          }
          .observeOn(AndroidSchedulers.mainThread())
          .subscribe({
              LogUtil.e(it.toString())
          }, {
              it.printStackTrace()
          }, {
              checkJump()
          }, {
              mDisposable = it
          })

override fun onDestroy() {
    super.onDestroy()
    mDisposable?.dispose()
}
```

注意我们还是需要通过mDisposable对象在页面销毁的时候释放，以免内存泄露，有没有简单一点方式？

#### 6、Kotlin Flow 的实现

上面的方法都需要销毁资源，好麻烦，能不能自动取消？协程不就行了。

是的 lifecycleScope 根据生命周期自动取消的协程作用域，配合Flow的操作符完成倒计时岂不是完美。

好吧，你是自动倒计时了。结束之后取消协程，销毁也能取消协程，那如果我想手动的取消倒计时怎么办？比如倒计时60秒我就要在第50秒的时候强制取消协程怎么办？

launch方法返回的不就是Job 对象吗？根据此上下文对象不就可以取消协程了吗？

看看灵活的Flow倒计时如何实现。

定义一个扩展方法：

```kotlin
/**
 * 倒计时的实现
 */
@ExperimentalCoroutinesApi
fun FragmentActivity.countDown(
    time: Int = 5,
    start: (scop: CoroutineScope) -> Unit,
    end: () -> Unit,
    next: (time: Int) -> Unit
) {

    lifecycleScope.launch {
        // 在这个范围内启动的协程会在Lifecycle被销毁的时候自动取消
        flow {
            (time downTo 0).forEach {
                delay(1000)
                emit(it)
            }
        }.onStart {
            // 倒计时开始 ，在这里可以让Button 禁止点击状态
            start(this@launch)
        }.onCompletion {
            // 倒计时结束 ，在这里可以让Button 恢复点击状态
            end()
        }.catch {
            //错误
            YYLogUtils.e(it.message ?: "Unkown Error")
        }.collect {
            // 在这里 更新值来显示到UI
            next(it)
        }
    }
}
```

使用：

```kotlin
fun startCountDown() {
var timeDownScope: CoroutineScope? = null

countDown(
    time = 60,
    start = {
        timeDownScope = it
        YYLogUtils.e("开始")
    },
    end = {
        YYLogUtils.e("结速倒计时")
        toast("结速倒计时")
    },
    next = {
        YYLogUtils.w("当时计数：" + it)
        if (it == 50) {
            timeDownScope?.cancel()
        }
    })
}
```

无需onDestory中销毁资源，如果想自由手动的控制倒计时，我们在start的高阶函数中接收父协程的上下文对象即可自动控制。使用起来也是超级简单。