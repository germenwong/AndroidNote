



### ViewModel

**简介** ：

[`ViewModel`](https://developer.android.google.cn/reference/androidx/lifecycle/ViewModel?hl=zh-cn) 类旨在以注重生命周期的方式存储和管理界面相关数据。[`ViewModel`](https://developer.android.google.cn/reference/androidx/lifecycle/ViewModel?hl=zh-cn) 类让数据可在发生屏幕旋转等配置更改后继续留存。

**依赖库** ：

> implementation ("androidx.lifecycle:lifecycle-viewmodel-ktx:2.2.0") 
>
> implementation ("androidx.fragment:fragment-ktx:1.3.6")

**生命周期** ：

[`ViewModel`](https://developer.android.google.cn/reference/androidx/lifecycle/ViewModel?hl=zh-cn) 对象的作用域限定为获取 [`ViewModel`](https://developer.android.google.cn/reference/androidx/lifecycle/ViewModel?hl=zh-cn) 时传递给 [`ViewModelProvider`](https://developer.android.google.cn/reference/androidx/lifecycle/ViewModelProvider?hl=zh-cn) 的 [`ViewModelStoreOwner`](https://developer.android.google.cn/reference/kotlin/androidx/lifecycle/ViewModelStoreOwner?hl=zh-cn) 的 [`Lifecycle`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle?hl=zh-cn)。[`ViewModel`](https://developer.android.google.cn/reference/androidx/lifecycle/ViewModel?hl=zh-cn) 将一直留在内存中，直到其作用域 `ViewModelStoreOwner` 永久消失：

- 对于 activity，是在 activity 完成时。
- 对于 fragment，是在 fragment 分离时。
- 对于 Navigation 条目，是在 Navigation 条目从返回堆栈中移除时。![说明 ViewModel 随着 Activity 状态的改变而经历的生命周期。](https://developer.android.google.cn/static/images/topic/libraries/architecture/viewmodel-lifecycle.png?hl=zh-cn)

**使用场景** ：

1. 手机屏幕横竖切换时，数据的保存（demo：ViewModel）
2. Activity中Fragment之间共享数据（demo：ViewModel2）

------







### LiveData

**简介** ：

[`LiveData`](https://developer.android.google.cn/reference/androidx/lifecycle/LiveData?hl=zh-cn) 是一种可观察的数据存储器类。与常规的可观察类不同，LiveData ==具有生命周期感知能力==，意指它遵循其他应用组件（如  activity、fragment 或 service）的生命周期。这种感知能力可确保 LiveData  仅更新处于活跃生命周期状态的应用组件观察者。

**依赖库** ：

> implementation ("androidx.lifecycle:lifecycle-livedata-ktx:2.4.0-alpha02")

**使用优势** ：

* 确保界面符合数据状态
* 不会发生内存泄漏
* 不会因 Activity 停止而导致崩溃
* 不再需要手动处理生命周期
* 数据始终保持最新状态
* 适当的配置更改
* 共享资源

**使用步骤** ：

1. 创建 `LiveData` 的实例以存储某种类型的数据。这通常在 [`ViewModel`](https://developer.android.google.cn/reference/androidx/lifecycle/ViewModel?hl=zh-cn) 类中完成
2. 创建可定义 [`onChanged()`](https://developer.android.google.cn/reference/androidx/lifecycle/Observer?hl=zh-cn#onChanged(T)) 方法的 [`Observer`](https://developer.android.google.cn/reference/androidx/lifecycle/Observer?hl=zh-cn) 对象，该方法可以控制当 `LiveData` 对象存储的数据更改时会发生什么。通常在 `Activity` 或 `Fragment` 中创建 `Observer` 对象。
3. 使用 [`observe()`](https://developer.android.google.cn/reference/androidx/lifecycle/LiveData?hl=zh-cn#observe(android.arch.lifecycle.LifecycleOwner, android.arch.lifecycle.Observer)) 方法将 `Observer` 对象附加到 `LiveData` 对象。`observe()` 方法会采用 [`LifecycleOwner`](https://developer.android.google.cn/reference/androidx/lifecycle/LifecycleOwner?hl=zh-cn) 对象。这样会使 `Observer` 对象订阅 `LiveData` 对象，以使其收到有关更改的通知。通常在 `Activity` 或 `Fragment` 中附加 `Observer` 对象。

```kotlin
class LiveDataViewModel : ViewModel() {
    //创建LiveData对象
    val nameLd: MutableLiveData<String> by lazy {
        MutableLiveData<String>()
    }
    
    //UI线程更新
	fun updateName(name: String) {
	      nameLd.value = name
	}
	
	//子线程更新
	fun updateNameThread(name: String) {
	      nameLd.postValue(name)
	}
}
```

```kotlin
class MainActivity : AppCompatActivity() {

	private lateinit var tvName: TextView
	private lateinit var btnChange: Button
    //通过属性委托获取viewModel对象
	private val model: LiveDataViewModel by viewModels()
	
	override fun onCreate(savedInstanceState: Bundle?) {
		super.onCreate(savedInstanceState)
		setContentView(R.layout.activity_main)
		
		tvName = findViewById(R.id.tv_name)
		btnChange = findViewById(R.id.btn_change)
		
		//创建观察者
		val nameObserver = Observer<String> {
			tvName.text = it
		}
		//观察LiveData对象
		model.nameLd.observe(this, nameObserver)
		//更新LiveData对象
		btnChange.setOnClickListener {
			model.updateName("在UI线程")
            model.updateNameThread("在子线程")
		}
	}
}
```

------







### ViewBinding

**简介** ：

通过视图绑定功能，您可以更轻松地编写可与视图交互的代码。在模块中启用视图绑定之后，系统会为该模块中的每个 XML 布局文件生成一个绑定类。绑定类的实例包含对在相应布局中具有 ID 的所有视图的直接引用。

在大多数情况下，视图绑定会替代 `findViewById`。

**配置** ：将 `viewBinding` 元素添加到其 `build.gradle` 文件

```kotlin
android{
    ....
	buildFeatures{
		viewBinding=true
	}
}
```

如果您希望在生成绑定类时忽略某个布局文件，请将 `tools:viewBindingIgnore="true"` 属性添加到相应布局文件的根视图中：

```xml
<LinearLayout
	...
	tools:viewBindingIgnore="true" >

</LinearLayout>
```

**在Activity使用** ：

```kotlin
class MainActivity : AppCompatActivity() {
    private lateinit var binding: ActivityMainBinding
    
	override fun onCreate(savedInstanceState: Bundle?) {
		super.onCreate(savedInstanceState)
		binding = ActivityMainBinding.inflate(layoutInflater)
		val view = binding.root
		setContentView(view)
	}
}
```

**在Fragment使用** ：

```kotlin
class FragmentOne : Fragment() {
    
    private var _binding: FragmentOneBinding? = null
    private val binding get() = _binding!!

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        _binding = FragmentOneBinding.inflate(inflater, container, false)
        val view = binding.root
        return view
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }

}
```

**与 findViewById 的区别** :

- **Null 安全**：由于视图绑定会创建对视图的直接引用，因此不存在因视图 ID 无效而引发 Null 指针异常的风险。此外，如果视图仅出现在布局的某些配置中，则绑定类中包含其引用的字段会使用 `@Nullable` 标记。
- **类型安全**：每个绑定类中的字段均具有与它们在 XML 文件中引用的视图相匹配的类型。这意味着不存在发生类转换异常的风险。

**与数据绑定的对比** :

- **更快的编译速度**：视图绑定不需要处理注释，因此编译时间更短。
- **易于使用**：视图绑定不需要特别标记的 XML 布局文件，因此在应用中采用速度更快。在模块中启用视图绑定后，它会自动应用于该模块的所有布局。

------







### DataBinding

**简介** ：

数据绑定库是一种支持库，借助该库，您可以使用声明性格式（而非程序化地）将布局中的界面组件绑定到应用中的数据源。

**使用方法** ：

```kotlin
//获取 binding 对象的两种方式
val binding = ActivityMainBinding.inflate(layoutInflater)
val binding = DataBindingUtil.setContentView<ActivityMainBinding>(this, R.layout.activity_main)

binding.name = "Anthony"
binding.age = 18


val user = User("Kai", 28)
binding.user = user


var list = listOf(12, 42, 17)
var map = mapOf(0 to "jack", 1 to "may")
binding.list = list
binding.map = map


val clickHandle = ClickHandle()
binding.click = clickHandle
```

xml布局

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>
        <variable
            name="name"
            type="String" />

        <variable
            name="age"
            type="int" />

        <variable
            name="user"
            type="com.hgm.databinding.User" />

        <import type="java.util.List" />

        <import type="java.util.Map" />

        <variable
            name="list"
            type="List&lt;Integer>" />

        <variable
            name="map"
            type="Map&lt;Integer,String>" />
        <variable
            name="click"
            type="com.hgm.databinding.ClickHandle" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        tools:context=".MainActivity">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content">

            <TextView
                android:id="@+id/tv_name"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:gravity="center"
                android:text="@{name}"
                android:textSize="23sp" />

            <TextView
                android:id="@+id/tv_age"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:gravity="center"
                android:text="@{String.valueOf(age)}"
                android:textSize="23sp" />
        </LinearLayout>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content">

            <TextView
                android:id="@+id/tv_uname"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:gravity="center"
                android:text="@{user.name}"
                android:textSize="23sp" />

            <TextView
                android:id="@+id/tv_uage"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:gravity="center"
                android:text="@{String.valueOf(user.age)}"
                android:textSize="23sp" />
        </LinearLayout>

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content">

            <TextView
                android:id="@+id/tv_list"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:gravity="center"
                android:text="@{String.valueOf(list[0])}"
                android:textSize="23sp" />

            <TextView
                android:id="@+id/tv_map"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:gravity="center"
                android:text="@{map[1]}"
                android:textSize="23sp" />
        </LinearLayout>

        <Button
            android:id="@+id/btn_click"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:layout_marginTop="50dp"
            android:text="click"
            android:onClick="@{(v)->click.onClick(v)}"/>

    </LinearLayout>
</layout>
```

**双向绑定** ：

之前的使用的 databinding 仅仅是单向的绑定，通过改变数据源来实现 ui 数据变化。双向绑定不仅修改数据源来实现变化，还可以通过ui控件来实现变化

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>
        <variable
            name="model"
            type="com.hgm.databinding2.RememberMe" />
    </data>

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context=".MainActivity">


        <Button
            android:id="@+id/button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="72dp"
            android:text="Button"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintHorizontal_bias="0.498"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toBottomOf="@+id/checkBox" />

        <CheckBox
            android:id="@+id/checkBox"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="记住密码"
            android:checked="@={model.rememberMe}"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintHorizontal_bias="0.498"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintVertical_bias="0.133" />
    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```

```kotlin
class MainActivity : AppCompatActivity() {

    private val model:RememberMe by lazy { RememberMe() }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val binding= DataBindingUtil.setContentView<ActivityMainBinding>(this,R.layout.activity_main)

        binding.button.setOnClickListener{
            var isChecked = model.getRememberMe()
            if (isChecked){
                model.setRememberMe(false)
            }else{
                model.setRememberMe(true)
            }
        }

        binding.model=model
    }
}
```

```kotlin
class RememberMe : BaseObservable() {

    var flag: Boolean = false

    @Bindable
    fun getRememberMe(): Boolean {
        return flag
    }

    fun setRememberMe(value: Boolean) {
        Log.e("hgm", "setRememberMe: $value")
        if (flag != value) {
            flag = value
            notifyPropertyChanged(BR.rememberMe)
        }
    }
}
```

------







### Lifecycle

**简介** ：

[`Lifecycle`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle?hl=zh-cn) 是一个类，用于存储有关组件（如 Activity 或 Fragment）的生命周期状态的信息，并允许其他对象观察此状态。

- 事件

  从框架和 [`Lifecycle`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle?hl=zh-cn) 类分派的生命周期事件。这些事件映射到 activity 和 fragment 中的回调事件。

- 状态

  由 [`Lifecycle`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle?hl=zh-cn) 对象跟踪的组件的当前状态。

![生命周期状态示意图](https://developer.android.google.cn/static/images/topic/libraries/architecture/lifecycle-states.svg?hl=zh-cn)

```kotlin
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        //添加观察者
        lifecycle.addObserver(MyLifecycleObserver())

        //判断当前Activity的状态，有助于减少内存泄露
        lifecycle.currentState.isAtLeast(Lifecycle.State.CREATED)
    }
}
```

类通过实现 [`DefaultLifecycleObserver`](https://developer.android.google.cn/reference/androidx/lifecycle/DefaultLifecycleObserver?hl=zh-cn) 并替换相应的方法（如 `onCreate` 和 `onStart` 等）来监控组件的生命周期状态。然后通过调用 [`Lifecycle`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle?hl=zh-cn) 类的 [`addObserver()`](https://developer.android.google.cn/reference/androidx/lifecycle/Lifecycle?hl=zh-cn#addObserver(androidx.lifecycle.LifecycleObserver)) 方法并传递观察器的实例来添加观察器，如下例所示：

```kotlin
class MyLifecycleObserver :LifecycleObserver{

    @OnLifecycleEvent(Lifecycle.Event.ON_CREATE)
    fun onCreate(){
        Log.e("hgm", "onCreate: ")
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_START)
    fun onStart(){
        Log.e("hgm", "onStart: ")
    }

    @OnLifecycleEvent(Lifecycle.Event.ON_RESUME)
    fun onResume(){
        Log.e("hgm", "onResume: ")
    }
}
```

