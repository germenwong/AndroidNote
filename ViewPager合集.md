## ViewPager

### 简介

实现滑动切换View效果的一种控件

<img src="https://img-blog.csdnimg.cn/20210301225537256.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0pNVzE0MDc=,size_16,color_FFFFFF,t_70"/>



#### 使用方法

***在xml里的配置，代码如下：***

```xml
<androidx.viewpager.widget.ViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
```

***创建适配器，代码如下：***

这里也可以继承 FragmentStatePagerAdapter

```java
public class ViewPagerAdapter extends PagerAdapter {
    
    //返回的数量
    @Override
    public int getCount() {
        return 0;
    }

    //为给定的位置创建页面
    @Override
    public boolean isViewFromObject(@NonNull View view, @NonNull Object object) {
        return false;
    }

    //显示View
    @NonNull
    @Override
    public Object instantiateItem(@NonNull ViewGroup container, int position) {
        return super.instantiateItem(container, position);
    }

    //销毁View
    @Override
    public void destroyItem(@NonNull ViewGroup container, int position, @NonNull Object object) {
        super.destroyItem(container, position, object);
    }
}
```

***设置适配器：***

```java
viewPager.setAdapter(new ViewPagerAdapter());
```

***设置监听器：***

```java
viewPager.addOnPageChangeListener(new ViewPager.OnPageChangeListener() {

            //页面滚动时
            @Override
            public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels) {

            }

            //页面被选中
            @Override
            public void onPageSelected(int position) {

            }

            //操作屏幕时发生的变化
            @Override
            public void onPageScrollStateChanged(int state) {

            }
        });
```

三个方法的执行顺序为：

最先执行一遍==onPageScrollStateChanged，然后不断执行onPageScrolled，

放手指的时候，直接立即执行一次onPageScrollStateChanged，然后立即执行一次onPageSelected，

然后再不断执行onPageScrollStateChanged，最后执行一次onPageScrollStateChanged。







## ViewPager2

### 简介

实现滑动切换View效果的一种控件



### 与ViewPager的区别

***可以设置垂直、水平方向***

```java
viewPager2.setOrientation(ViewPager2.ORIENTATION_VERTICAL);
```

ViewPage的监听器需要重写三个方法，ViewPager2可以重写三个方法中的其中一个，代码如下：

```java
viewPager2.registerOnPageChangeCallback(new ViewPager2.OnPageChangeCallback() {
    @Override
    public void onPageSelected(int position) {
        super.onPageSelected(position);
    }
});
```



### 使用方法

***xml代码如下：***

```xml
<androidx.viewpager2.widget.ViewPager2
	android:id="@+id/viewPager2"
	android:layout_width="match_parent"
	android:layout_height="match_parent"/>
```

***创建适配器，代码如下：***

```java
public class ViewPagerAdapter extends RecyclerView.Adapter<ViewPagerAdapter.MyViewHolder> {

    List<Integer> viewList;

    public ViewPagerAdapter(List<Integer> viewList) {
        this.viewList = viewList;
    }

    @NonNull
    @Override
    public ViewPagerAdapter.MyViewHolder onCreateViewHolder(@NonNull  ViewGroup parent, int viewType) {
        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_viewpager_1, parent, false);
        return new MyViewHolder(view);
    }

    @Override
    public void onBindViewHolder(@NonNull ViewPagerAdapter.MyViewHolder holder, int position) {
        Integer color = viewList.get(position);
        holder.itemViewPager2.setBackgroundColor(color);
    }

    @Override
    public int getItemCount() {
        return viewList.size();
    }

    static class MyViewHolder extends RecyclerView.ViewHolder{

        private LinearLayout itemViewPager2;

        public MyViewHolder(@NonNull View itemView) {
            super(itemView);

            itemViewPager2 = itemView.findViewById(R.id.item_viewPager2);
        }
    }
}
```

使用FragmentStateAdapter

```kotlin
class MyPagerAdapter(activity: MainActivity, private val 		   fragments:List<Fragment>):FragmentStateAdapter(activity){
    override fun getItemCount(): Int {
        return fragments.size
    }

    override fun createFragment(position: Int): Fragment {
        return fragments[position] ?: error("请确保fragments数据源和 viewpager2的index匹配设置")
    }
}
```

***设置适配器***

```java
 viewPager2.setAdapter(new ViewPagerAdapter(viewList));
```

