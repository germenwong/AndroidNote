### 简介

经常可以在app上面看到许多的上下滚动textview，Android官方给我们提供了 `TextSwitcher` 文本切换器

TextSwitcher集成了ViewSwitcher,  因此它具有与ViewSwitcher相同的特性：可以在切换View组件时使用动画效果。与ImageSwitcher相似的是，使用TextSwitcher也需要设置一个ViewFactory。与ImageSwitcher不同的是，TextSwitcher所需要的ViewFactory的makeView()方法必须返回一个TextView组件

实现效果如下：

<img src="https://img-blog.csdn.net/20180222173310240?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZzc3NzUyMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"/>

------





### 使用方法

xml布局：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_margin="20dp">
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="公告栏："
            android:textSize="15sp"
            android:textColor="@color/black"/>

        <TextSwitcher
            android:id="@+id/ts"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:inAnimation="@anim/anim_in"
            android:outAnimation="@anim/anim_out" />
    </LinearLayout>

</LinearLayout>
```

上下滚动的动画：anim_in、anim_out

```xml
<?xml version="1.0" encoding="utf-8"?>
<translate xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="1000"
    android:fromYDelta="100%p"
    android:toYDelta="0">
</translate>
```

```xml
<?xml version="1.0" encoding="utf-8"?>
<translate xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="1000"
    android:fromYDelta="0"
    android:toYDelta="-100%p">
</translate>
```

activity中调用：

```kotlin
class MainActivity : AppCompatActivity() {

    private var curStr = 0
    private lateinit var ts: TextSwitcher
    private val textList = arrayListOf(
        "Android开发者",
        "Kotlin成为Android第一语言",
        "Java",
        "Jetpack全新组件",
        "Jetpack-compose声明式UI框架"
    )

    private val handler = object : Handler() {
        override fun handleMessage(msg: Message) {
            super.handleMessage(msg)
            next()
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        ts = findViewById(R.id.ts)

        //设置工厂类，用于生产TextView
        ts.setFactory {
            var text = TextView(this@MainActivity)
            text.textSize = 15F
            return@setFactory text
        }
        ts.setText(textList[0])

        //重复循环下一个内容
        thread {
            while (true) {
                handler.sendEmptyMessage(0)
                Thread.sleep(3000)
            }
        }
    }

    fun next() {
        if (textList.size == 0) return
        ts.setText(textList[curStr++ % textList.size])
    }
}
```

![image-20221019154508942](TextSwitcher/image-20221019154508942.png)![image-20221019154524457](TextSwitcher/image-20221019154524457.png)