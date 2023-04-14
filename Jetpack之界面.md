# Jetpack之界面

## Animation&transitions



## Emoji

### 简介

Emoji 是可以被插入文字中的图形符号。它是一个日本语，e 表示"绘"，moji 表示 "文字" ，连在一起就是 “绘文字”。

2010年开始，Unicode 开始为 Emoji 分配码点，也就是说，在那之后的 Emoji 符号，就是一个字体，它会被渲染为图片显示。



## Fragment



## Layout





## Palette

Palette是一个可以从==图片(Bitmap)中提取颜色的帮助类==，可以使UI更加美观，根据图片动态的显示相应的颜色。在Android5.0（API22）引入的。

### 使用步骤

***添加依赖***

```java
implementation 'com.android.support:palette-v7:23.4.0'
```

***主要方法***

```java
Palette.from(bitmap).generate(new Palette.PaletteAsyncListener() {
    @Override
    public void onGenerated(Palette palette) {
        //获取样本
        Palette.Swatch vibrant = palette.getVibrantSwatch();
        //判空
        if (vibrant == null) {
            for (Palette.Swatch swatch : palette.getSwatches()) {
                vibrant = swatch;
                break;
            }
        }
        //从样本中获取颜色的RGB值
        int rgb = vibrant.getRgb();
        
        //接下里可以设置控件的颜色.....
    }
});

/**
 * 对获取到的RGB颜色进行修改。（涉及到位运算，我也不是很懂这块）
 * （这个方法是给状态栏的颜色用的）
 * @param rgb
 * @return
 */
private int changeColor(int rgb) {
    int red = rgb >> 16 & 0xFF;
    int green = rgb >> 8 & 0xFF;
    int blue = rgb & 0xFF;
    red = (int) Math.floor(red * (1 - 0.2));
    green = (int) Math.floor(green * (1 - 0.2));
    blue = (int) Math.floor(blue * (1 - 0.2));
    return Color.rgb(red, green, blue);
}
```

***通过Palette 可以获取到一些颜色值***

<img src="https://upload-images.jianshu.io/upload_images/1930161-187e73b0d8845b90.gif" style="zoom: 100%" />

```java
// 获取到柔和的深色的颜色（可传默认值）
palette.getDarkMutedColor(Color.BLUE);
// 获取到活跃的深色的颜色（可传默认值）
palette.getDarkVibrantColor(Color.BLUE);
// 获取到柔和的明亮的颜色（可传默认值）
palette.getLightMutedColor(Color.BLUE);
// 获取到活跃的明亮的颜色（可传默认值）
palette.getLightVibrantColor(Color.BLUE);
// 获取图片中最活跃的颜色（也可以说整个图片出现最多的颜色）（可传默认值）
palette.getVibrantColor(Color.BLUE);
// 获取图片中一个最柔和的颜色（可传默认值）
palette.getMutedColor(Color.BLUE);
// ...  这里省略其他的方法。
```

***具体代码查看案例：***Palette

***运行效果：***

<img src="https://upload-images.jianshu.io/upload_images/1930161-bed6eddbb3d12246.gif" style="zoom: 100%" />







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

***设置适配器***

```java
 viewPager2.setAdapter(new ViewPagerAdapter(viewList));
```





## WebView

### 加载网页

- 加载URL（网络或者本地assets文件夹下的html文件）
- 加载HTML代码
- Native和Javascript相互调用



### 加载网络URL

```java
webview.loadUrl("www.baidu.com");
```



### 加载assets下的html文件

```java
webview.loadUrl("file:///android_asset/test.html");
```



### 加载html代码

- webview.loadData();
- webview.loadDataWithBaseURL();



### 网页的前进后退

1. 判断是否能后退：webview.canGoBack();
2. 后退：webview.goBack();
3. 判断是否能前进：webview.canGoForward();
4. 前进：webview.goForward();
5. 判断是否能后退或者前进：webview.canGoBackOrForward(int steps);
6. 后退或者前进：webview.goBackOrForward(int steps);

