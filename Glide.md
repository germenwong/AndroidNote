## Glide

### 简介

Glide是一款由Bump Technologies开发的快速高效的Android==图片加载库==，使得我们可以在Android平台上以极度简单的方式加载和展示图片。注重于平滑的滚动。Glide提供了易用的API，高性能、可扩展的图片解码管道（decode pipeline），以及自动的资源池技术。

***开源地址：***https://github.com/bumptech/glide

***中文文档：***[Glide v4 : 快速高效的Android图片加载库 (muyangmin.github.io)](https://muyangmin.github.io/glide-docs-cn/)

------





### 基本使用

***添加依赖：***

```java
implementation 'com.github.bumptech.glide:glide:4.11.0'
annotationProcessor 'com.github.bumptech.glide:compiler:4.11.0'
```

>  support annotations自25.4.0之后support包不在sdk中更新了，需要用谷歌的maven库，在Project的build.gradle中添加如下谷歌的地址，因此需要我们手动的在Project的build.gradle中添加如下谷歌的地址：

```java
repositories {
  //需要添加的部分  
  maven { url "https://maven.google.com"}
  maven { url "https://dl.google.com/dl/android/maven2/"}
  ...
}
```

***添加网络权限：***

```java
<uses-permission android:name="android.permission.INTERNET"/>
```

***代码如下：***

> 普通加载图片，传统三步骤：先with()，再load()，最后into()。

```java
Glide.with(this)
    .load("网络图片/资源")
    .into(imageView);
```

------







### RequestOptions

熟悉Glide3的都知道，如果我们需要使用占位图，缓存等功能，只需要链式调用一直加方法即可。
而在Glide4中我们需要创建一个RequestOptions对象，将这些操作在RequestOptions对象上链式调用，然后apply方法中传入RequestOptions参数即可。==即Glide4是先with()，load()，再apply()，最后into()==。代码如下：

```java
RequestOptins options = RequestOptions()
            .placeholder(R.mipmap.loading)
            .error(R.mipmap.error)
            .skipMemoryCache(true)
            //.override(100, 200)
            .diskCacheStrategy(DiskCacheStrategy.NONE)
Glide.with(this).load("http://q8xcfpnt5.bkt.clouddn.com/hzw.jpg").apply(options).into(ivUrl)
```

| 作用         | 方法                | 说明                                                         |
| ------------ | ------------------- | ------------------------------------------------------------ |
| 占位图       | placeholder()       | 加载未成功时预先显示的图片，需要注意的是占位图的大小会影响加载后图片的大小 |
| 错误占位图   | error()             | 加载失败时显示的图片                                         |
| 内存缓存     | skipMemoryCache()   | 默认false开启内存缓存功能，传入true，就表示禁用掉Glide的内存缓存功能 |
| 硬盘缓存     | diskCacheStrategy() | DiskCacheStrategy.AUTOMATIC： 表示让Glide根据图片资源智能地选择使用哪一种缓存策略（默认选项） |
| 指定图片大小 | override(x,y)       | 会将图片加载成x*y像素的尺寸，而不会管你的ImageView的大小；可以使用Target.SIZE_ORIGINAL表示原始尺寸。 |

***缓存机制：***

1、内存缓存

> 默认情况下，内存缓存时自动开启的。当我们使用Glide加载了一张图片之后，这张图片就会被缓存到内存当中，只要在它还没从内存中被清除之前，下次使用Glide再加载这张图片都会直接从内存当中读取，而不用重新从网络或硬盘上读取了，这样可以大幅度提升图片的加载效率。
> 如果我们不需要使用内存缓存，只需要调用skipMemoryCache()方法并传入true，就表示禁用掉Glide的内存缓存功能。

2、硬盘缓存

| 值                          | 说明                                                         |
| --------------------------- | ------------------------------------------------------------ |
| DiskCacheStrategy.NONE      | 表示不缓存任何内容。                                         |
| DiskCacheStrategy.DATA      | 表示只缓存原始图片。                                         |
| DiskCacheStrategy.RESOURCE  | 表示只缓存转换过后的图片。                                   |
| DiskCacheStrategy.ALL       | 表示既缓存原始图片，也缓存转换过后的图片。                   |
| DiskCacheStrategy.AUTOMATIC | 表示让Glide根据图片资源智能地选择使用哪一种缓存策略（默认选项）。 |

> DiskCacheStrategy.AUTOMATIC是Glide 4中新增的一种缓存策略，并且在不指定diskCacheStrategy的情况下默认使用就是的这种缓存策略。调用diskCacheStrategy()方法并传入DiskCacheStrategy.NONE，就可以禁用掉Glide的硬盘缓存功能。
>

------





### 加载动图

Glide其中一个非常亮眼的功能就是可以加载GIF图片。使用Glide加载GIF图并不需要编写什么额外的代码，==Glide内部会自动判断图片格式==。使用代码如下：

```java
Glide.with(this)
		.load("http://q8xcfpnt5.bkt.clouddn.com/hzwgif.gif")
		.into(imageView)
```

> 如果我们希望无论什么格式的图片展示时都是静态图。 那么我们可以调用`asBitmap()`方法，这个方法只允许加载静态图片。如果传入的时gif动图的话，只会加载第一帧。

```java
Glide.with(this)
		.asBitmap()
		.load("http://q8xcfpnt5.bkt.clouddn.com/hzwgif.gif")
		.into(ivGiftFirst)
```

| 方法         | 说明                       |
| ------------ | -------------------------- |
| asBitmap()   | 只允许加载静态图片         |
| asGif()      | 强制指定加载动态图片       |
| asFile()     | 强制指定加载成文件格式     |
| asDrawable() | 强制指定加载成Drawable格式 |

**注意：**在Glide3中的语法是先load()再asxxx()的，而在Glide 4中是先asxxx()再load()，如果你写错了顺序就会报错

------







### Glide占位符

Glide4中占位图的使用方法，包括（==placeholder==、==error==、==fallback==）三种占位图

***placeholder：***正在请求图片的时候展示的图片

***error：***如果请求失败的时候展示的图片（如果没有设置，还是展示placeholder的占位符）

***fallback：***如果请求的url/model为null的时候展示的图片（如果没有设置，还是展示placeholder的占位符）

***使用代码如下：***

```java
RequestOptions requestOptions = new RequestOptions()
    .placeholder(R.drawable.ic_launcher_background)
    .error(R.drawable.ic_launcher_foreground)
    .fallback(R.drawable.ic_launcher_background);

Glide.with(this)
	.load("网络图片/资源")
    .apply(requestOptions)
    .into(image);
```

------







### 过渡动画

定义Glide如何从占位符到新加载的图片，或从缩略图到全尺寸图像过渡。

```java
交叉淡入（避免占位图还能显示）
DrawableCrossFadeFactory factory =new DrawableCrossFadeFactory.Builder().setCrossFadeEnabled(true).build();
	
Glide.with(context)
	.Joad(URL)
	.apply(requestOptions)
	.transition(DrawableTransitionOptions.withCrossFade(factory))
	.into((ImageView) holder.itemView);
```

------







### 变换

***Rotate：***旋转

***CircleCrop：***圆角

***RoundedCorners：***四个角度统一指定

***GranularRoundedCorners：***四个角度单独指定

```java
Glide.with(this)
	.load(URL)
	.transform(..)//变换效果
	.into(imageView);
```

------







### 圆角与居中冲突问题

项目中需要对图片进行圆角显示，并居中裁剪,使用glide时，发现这两种会出现冲突，只能显示一种效果。

解决方案：

```java
Glide.with(this)
		.load(imagesId2[i])
		.transform(MultiTransformation(CenterCrop(), RoundedCorners(20)))//使用这个方法
		.into(imageView)
```

