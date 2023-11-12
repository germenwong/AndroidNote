### 介绍

原理: viewpager2 内部实现原理是使用recycleview加LinearLayoutManager实现竖直滚动,其实可以理解为对recyclerview的二次封装

<img src="https://img-blog.csdnimg.cn/20210301225537256.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0pNVzE0MDc=,size_16,color_FFFFFF,t_70"/>

------



### 优势

- 支持RTL布局,
- 支持竖向滚动
- 支持notifyDataSetChanged

------



### api

* `FragmentStateAdapter`替换了原来的 `FragmentStatePagerAdapter`
* `RecyclerView.Adapter` 替换了原来的 `PagerAdapter`
* `registerOnPageChangeCallback` 替换了原来的 `addPageChangeListener`

------



### 与BottomNavigationView结合

***1、xml代码：**

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <androidx.viewpager2.widget.ViewPager2
        android:id="@+id/vp"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"/>

    <com.google.android.material.bottomnavigation.BottomNavigationView
        android:id="@+id/bnv"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        app:menu="@menu/menu"
        android:background="?android:attr/windowBackground"/>

</LinearLayout>
```

***2、添加一个Fragment***

***3、创建适配器***

```kotlin
class MyPagerAdapter(activity: MainActivity, private val fragments:List<Fragment>):FragmentStateAdapter(activity){
    override fun getItemCount(): Int {
        return fragments.size
    }

    override fun createFragment(position: Int): Fragment {
        return fragments[position] ?: error("请确保fragments数据源和 viewpager2的index匹配设置")
    }
}
```

***4、Activity里配置ViewPager2***

```kotlin
class MainActivity : AppCompatActivity() {
    private val binding:ActivityMainBinding by lazy {
        ActivityMainBinding.inflate(layoutInflater)
    }
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(binding.root)

        initView()
    }

    private fun initView() {
        //添加fragment到集合
        var fragments = ArrayList<Fragment>()
        fragments.add(BaseFragment())
        fragments.add(BaseFragment())
        fragments.add(BaseFragment())

        //初始化
        binding.apply {
            vp.adapter=MyPagerAdapter(this@MainActivity, fragments)//添加适配器

            BnvVp(binding.bnv,binding.vp){bnv,vp2 ->
                vp2.isUserInputEnabled=true//设置是否响应用户滑动
            }.attach()//实现关联

            vp.currentItem=0//设置当前页面
        }
    }
}
```







### 工具类

一句代码实现 BottomNavigationView 与 ViewPager2 的滑动关联，省略了两个控件的监听方法，使用方法如下：

```kotlin
BnvVp(binding.bnv,binding.vp){bnv,vp2 ->
    vp2.isUserInputEnabled=true//设置是否响应用户滑动
}.attach()//实现关联
```

```kotlin
/**
 * BottomNavigationViewView与ViewPager2【关联工具类】
 * 一句代码即可实现关联
 */
class BnvVp(
    private val bnv: BottomNavigationView,
    private val vp2: ViewPager2,
    private val config: ((BottomNavigationView,ViewPager2) -> Unit)? = null
) {

    // BottomNavigationView item 与 id 的对应关系
    private val map = mutableMapOf<MenuItem,Int>()

    init {
        bnv.menu.forEachIndexed { index, item ->
            map[item] = index
        }
    }

    //绑定
    fun attach(){
        config?.invoke(bnv,vp2)
        // viewpager 页面滑动监听: 动态绑定 BottomNavigationView 的selectedItemId 属性
        vp2.registerOnPageChangeCallback(object : ViewPager2.OnPageChangeCallback() {
            override fun onPageSelected(position: Int) {
                super.onPageSelected(position)

                // 绑定 position 位置的 BottomNavigationView 菜单Id
                bnv.selectedItemId = bnv.menu.getItem(position).itemId
            }
        })
        // BottomNavigationView 的菜单点击事件 动态改变 viewpager 切换的页面
        bnv.setOnNavigationItemSelectedListener { item ->
            vp2.currentItem = map[item] ?: error("Bnv 的 item 的 ID ${item.itemId} 没有对应的 viewpager2 的元素")
            true
        }
    }
}
```

