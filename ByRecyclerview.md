## ByRecyclerview（第三方库）

### 简介

**ByRecyclerView**是一个用来处理App中列表展示的框架。它最大的优点在于，`RecyclerView`和`Adatper`两者可以分开使用、自带下拉刷新也可配合SwipeRefreshLayout使用、不足一屏上拉才加载更多。

Github：[官网地址](https://github.com/youlookwhat/ByRecyclerView/wiki)

------

### 特点

- 支持 下拉刷新、加载更多
- 可随意切换 自带下拉刷新布局 / SwipeRefreshLayout
- 加载更多机制：手动上拉才执行加载更多
- 可设置自定义 下拉刷新布局 和 加载更多布局
- 添加/移除 HeaderView、FooterView
- 设置各种状态布局 EmptyView / LoadingView / ErrorView
- 添加item的点击/长按事件(防止重复点击)
- 优化过的BaseAdapter (RV/LV)，减少大量代码
- Adapter结合DataBinding使用 (RV/LV)
- 可添加万能分隔线（线性/宫格/瀑布流）
- 可设置粘性header，StickyView
- 可配置Skeleton骨架图
- item 局部刷新

------

### 使用教程

***1.1导入仓库和依赖***

```java
allprojects {
	repositories {
		...
		maven { url "https://jitpack.io" }
	}
}
```

```xml
dependencies {
    implementation 'com.github.youlookwhat:ByRecyclerView:1.3.2'//AndroidX版本引入
}
```

***1.2 xml布局使用***

```xml
<me.jingbin.library.ByRecyclerView
    android:id="@+id/recyclerView"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:layoutManager="LinearLayoutManager"
    tools:listitem="@layout/item_home" />
```

| 属性名              | 说明                           |
| ------------------- | ------------------------------ |
| tools:layoutManager | 设置ByRecyclerView的布局管理者 |
| tools:listitem      | 设置ByRecyclerView的布局样式   |

***1.3 设置适配器***

ByRecyclerview自带基类BaseRecyclerAdapter，只需继承即可使用

```java
public class ByRecyclerviewAdapter extends BaseRecyclerAdapter<String> {

    public ByRecyclerviewAdapter(List<Bean> data) {
        //把布局和数据传进去
        super(R.layout.item_main, dataList);
    }

    @Override
    protected void bindView(BaseByViewHolder<String> holder, String bean, int position) {
		//控件绑定数据
        holder.setText(R.id.tv_text, bean.getTitle())
              .addOnClickListener(R.id.tv_text)       //子item添加点击事件
              .addOnLongClickListener(R.id.tv_text);  //子item添加长按事件
    }
}
```

------

### 监听事件

>  刷新机制：不满一屏上拉加载更多，满一屏后触底加载更多。<br> 					设置监听即表示开启加载更多。不设置默认不开启。
>
> 想要使用加载更多，必须设置监听或将加载更多开关打开。<br>只打开开关不设置加载更多监听，多出现在只想在列表最后设置==没有更多数据了==的布局。<br>mRecyclerView.setLoadMoreEnabled(true);

#### 下拉刷新：

```java
byRecyclerView.setOnRefreshListener(new ByRecyclerView.OnRefreshListener() {
    @Override
    public void onRefresh() {
        //添加数据
        ...
        //结束刷新
        byRecyclerView.setRefreshing(false);
    }
});
```

#### 上拉加载：

```java
byRecyclerView.setOnLoadMoreListener(new ByRecyclerView.OnLoadMoreListener() {
    @Override
    public void onLoadMore() {
        //添加数据
        ...
		//加载完成
        byRecyclerView.loadMoreComplete();
        //加载失败
        byRecyclerView.oadMoreFail();
		//没有更多内容了
        byRecyclerView.loadMoreEnd();
    }
});
```

#### item点击与长按

```java
//item 点击事件
byRecyclerView.setOnItemClickListener(new ByRecyclerView.OnItemClickListener() {
    @Override
    public void onClick(View v, int position) {
        DataBean data = dataList.get(position);
		Toast.makeText(MainActivity.this, "item点击", Toast.LENGTH_SHORT).show();
    }
});
// 防止重复点击
mRecyclerView.setOnItemClickListener(new OnItemFilterClickListener() {
    @Override
    protected void onSingleClick(View v, int position) {

    }
});

//item 长按事件
byRecyclerView.setOnItemLongClickListener(new ByRecyclerView.OnItemLongClickListener() {
     @Override
     public boolean onLongClick(View v, int position) {
		DataBean data = dataList.get(position);
		Toast.makeText(MainActivity.this, "item长按", Toast.LENGTH_SHORT).show();
		return true;
     }
 });
```

#### 子item点击与长按

```java
//childItem 点击事件
byRecyclerView.setOnItemChildClickListener(new ByRecyclerView.OnItemChildClickListener() {
    @Override
    public void onItemChildClick(View view, int position) {
        DataBean data = dataList.get(position);
		Toast.makeText(MainActivity.this, "子item点击", Toast.LENGTH_SHORT).show();
    }
});

//childItem 长按事件
byRecyclerView.setOnItemChildLongClickListener(new ByRecyclerView.OnItemChildLongClickListener() {
    @Override
    public boolean onItemChildLongClick(View view, int position) {
        DataBean data = dataList.get(position);
        Toast.makeText(MainActivity.this, "子item长按", Toast.LENGTH_SHORT).show();
        return true;
    }
});
```

------

#### 设置不满一屏不加载

```java
//不满一屏无法上拉加载更多
recyclerView.setNotFullScreenNoLoadMore();
```

#### 设置加载更多布局底部间距

> 给加载更多底部增加高度，单位dp。这样的设计主要是为了底部如果有透明栏，加载更多布局不会被覆盖。

```java
// 为了底部透明显示
recyclerView.setLoadingMoreBottomHeight(50);
```

#### 自定义下拉刷新布局

> 使用者可以根据项目需求自定义布局，需要继承[BaseRefreshHeader](https://github.com/youlookwhat/ByRecyclerView/blob/master/ByRecyclerview/src/main/java/me/jingbin/library/BaseRefreshHeader.java)<br>仿网易云音乐Ios版下拉刷新布局示例：[NeteaseRefreshHeaderView](https://github.com/youlookwhat/ByRecyclerView/blob/master/app/src/main/java/me/jingbin/byrecyclerview/view/NeteaseRefreshHeaderView.java)

```java
// 设置下拉刷新布局
recyclerView.setRefreshHeaderView(new NeteaseRefreshHeaderView(this));
```

#### 自定义加载更多布局

> 自定义加载中，无内容，失败的布局，继承 [BaseLoadMore](https://github.com/youlookwhat/ByRecyclerView/blob/master/ByRecyclerview/src/main/java/me/jingbin/library/BaseLoadMore.java)

```java
// 设置加载更多布局
recyclerView.setLoadingMoreView(new NeteaseLoadMoreView(this));
```

------

### Adapter

***目前有三种Adapter，两种ViewHolder:***

- BaseByRecyclerViewAdapter(所有adapter超类)
- BaseRecyclerAdapter (单类型极简adapter)
- BaseBindingAdapter (使用了databinding的单类型极简adapter)
- BaseByViewHolder (所有ViewHolder超类)
- BaseBindingHolder (使用了databinding的ViewHolder)

***简单点说：***

- 多类型展示使用`BaseByRecyclerViewAdapter`，其中使用的ViewHolder可以是`BaseByViewHolder`或`BaseBindingHolder`
- 单类型展示使用`BaseRecyclerAdapter`或`BaseBindingAdapter`，区别是后者使用了`databinding`

注意：

- 因为databinding是在编译时生成对应xml文件的类，所以需要使用者拷贝项目中的[binding](https://github.com/youlookwhat/ByRecyclerView/tree/master/app/src/main/java/me/jingbin/byrecyclerview/binding)文件夹里的类。

#### 单类型列表极简实现

```java
public class OneTypeAdapter extends BaseRecyclerAdapter<DataItemBean> {

    public OneTypeAdapter(List<DataItemBean> data) {
        super(R.layout.item_main, data);
    }

    @Override
    protected void bindView(BaseByViewHolder<DataItemBean> holder, DataItemBean bean, int position) {
        holder.setText(R.id.tv_text, bean.getTitle())
              .addOnClickListener(R.id.tv_text)       // 子view点击事件
              .addOnLongClickListener(R.id.tv_text);  // 子view长按事件
    }
}
```

#### 单类型列表(databinding)

```java
public class DataAdapter extends BaseBindingAdapter<DataItemBean, ItemHomeBinding> {

    public DataAdapter() {
        super(R.layout.item_home);
    }

    public DataAdapter(List<DataItemBean> data) {
        super(R.layout.item_home, data);
    }

    @Override
    protected void bindView(BaseBindingHolder holder, ItemHomeBinding binding, DataItemBean bean, int position) {
        binding.tvText.setText(bean.getTitle() + ": " + position);
    }
}
```

#### 多类型列表实现

> 使用 BaseByRecyclerViewAdapter 结合 BaseBindingHolder 或 BaseByViewHolder

```java
public class MultiAdapter extends BaseByRecyclerViewAdapter<DataItemBean, BaseByViewHolder<DataItemBean>> {

    public MultiAdapter(List<DataItemBean> data) {
        super(data);
    }

    @Override
    public int getItemViewType(int position) {
        DataItemBean itemData = getItemData(position);
        if ("title".equals(itemData.getType())) {
            return 1;
        } else {
            return 2;
        }
    }

    @NonNull
    @Override
    public BaseByViewHolder<DataItemBean> onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        if (1 == viewType) {
            return new TitleHolder(parent, R.layout.item_multi_title);
        } else {
            return new ViewHolder(parent, R.layout.item_home);
        }
    }

    private class TitleHolder extends BaseByViewHolder<DataItemBean> {
        TitleHolder(ViewGroup viewGroup, int layoutId) {
            super(viewGroup, layoutId);
        }

        @Override
        protected void onBaseBindView(BaseByViewHolder<DataItemBean> holder, DataItemBean bean, int position) {
            holder.setText(R.id.tv_title, bean.getDes());
        }
    }

    private class ViewHolder extends BaseBindingHolder<DataItemBean, ItemHomeBinding> {
        ViewHolder(ViewGroup viewGroup, int layoutId) {
            super(viewGroup, layoutId);
        }

        @Override
        protected void onBindingView(BaseBindingHolder holder, DataItemBean bean, int position) {
            binding.tvText.setText(bean.getDes());
        }
    }
}
```

------

### ItemDecoration

#### 给LinearLayout设置分割线

> 可设置`drawable`，也可以直接设置颜色，高度，左右间距。并可以设置头部和尾部不显示item的个数，例如添加了header，不想在此header下添加分割线，则可以通过`setHeaderNoShowDivider()`方法处理。具体实现类：[SpacesItemDecoration](https://github.com/youlookwhat/ByRecyclerView/blob/master/ByRecyclerview/src/main/java/me/jingbin/library/decoration/SpacesItemDecoration.java)。

***设置drawable：***

```java
// 第二个参数表示 是纵向还是横向
SpacesItemDecoration itemDecoration = new SpacesItemDecoration(this, SpacesItemDecoration.VERTICAL)
        .setNoShowDivider(1, 1)  // 第一个参数：头部不显示分割线的个数，第二个参数：尾部不显示分割线的个数，默认为1
        .setDrawable(R.drawable.shape_line);// 设置drawable文件

recyclerView.addItemDecoration(itemDecoration);
```

***设置颜色、高度、间距等：***

> 也可设置横向或纵向

```java
SpacesItemDecoration itemDecoration = new SpacesItemDecoration(this, SpacesItemDecoration.VERTICAL)
        .setNoShowDivider(1, 1)
        // 颜色，分割线间距，左边距(单位dp)，右边距(单位dp)
        .setParam(R.color.colorBlue, 10, 70, 70);

recyclerView.addItemDecoration(itemDecoration);
```



#### 给宫格/瀑布流设置分割线

> 可以设置两种风格的分割线，1.四周没有间距，2.四周有间距。也可以设置头部和尾部不显示item的个数，具体实现类：[GridSpaceItemDecoration](https://github.com/youlookwhat/ByRecyclerView/blob/master/ByRecyclerview/src/main/java/me/jingbin/library/decoration/GridSpaceItemDecoration.java)

```java
GridSpaceItemDecoration itemDecoration = new GridSpaceItemDecoration(10, true)
        .setNoShowSpace(1, 1);

recyclerView.addItemDecoration(itemDecoration);
```

```java
/**
 * @param spacing     item 间距
 * @param includeEdge item 距屏幕周围是否也有间距
 */
public GridSpaceItemDecoration(int spacing, boolean includeEdge)

/**
 * 设置从哪个位置 结束设置间距
 *
 * @param startFromSize 一般为HeaderView的个数 + 刷新布局(不一定设置)
 * @param endFromSize   默认为1，一般为FooterView的个数 + 加载更多布局(不一定设置)
 */
public GridSpaceItemDecoration setNoShowSpace(int startFromSize, int endFromSize)
```

------

### 设置StickyView

***使用StickyLinearLayoutManager，传入adapter***

```java
StickyLinearLayoutManager layoutManager = new StickyLinearLayoutManager(getContext(), mAdapter);
```

***在adapter里，将悬浮的item的ItemViewType设置为StickyHeaderHandler.TYPE_STICKY_VIEW***

```java
@Override
public int getItemViewType(int position) {
    if ("title".equals(getItemData(position).getType())) {
        return StickyHeaderHandler.TYPE_STICKY_VIEW;
    } else {
        return 2;
    }
}
```

> 也可以使用StickyGridLayoutManager，只需将最后一个参数传入 adapter 即可

==注意：使用置顶item时，不能使用自带的下拉刷新。==

------

### 设置Skeleton骨架图

#### 设置item骨架图

通过额外setAdapter实现 【在之前 不能 setAdapter()】 示例代码：

```java
// 显示
skeletonScreen = BySkeleton
        .bindItem(binding.recyclerView)
        .adapter(mAdapter)// 必须设置adapter，且在此之前不要设置adapter
        .shimmer(false)// 是否有动画
        .load(R.layout.layout_by_default_item_skeleton)// item骨架图
        .angle(30)// 微光角度
        .frozen(false) // 是否不可滑动
        .color(R.color.colorWhite)// 动画的颜色
        .duration(1500)// 微光一次显示时间
        .count(10)// item个数
        .show();

// 隐藏
skeletonScreen.hide();
```

#### 设置view骨架图

通过setStateView实现 【在之前 需要 setAdapter()】 示例代码：

```java
// 显示
skeletonScreen = BySkeleton
        .bindView(binding.recyclerView)
        .load(R.layout.layout_skeleton_view)// view骨架图
        .shimmer(true)// 是否有动画
        .angle(20)// 微光角度
        .color(R.color.colorWhite)// 动画的颜色
        .duration(1500)// 微光一次显示时间
        .show();

// 隐藏
skeletonScreen.hide();
```

