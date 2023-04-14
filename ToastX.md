## ToastX（第三方库）

### 简介

轻量级的Android吐司库，底层使用SnackBar实现，可以实现顶部弹出，底部弹出。
UI可高度定制。支持自定义布局。可自定义显示时长。提供简洁的APi调用

------

### 使用步骤

#### 普通用法：

***1.添加库和依赖***

```java
//build.gradle(:ToastX)里面添加
allprojects {
    repositories {
        google()
        mavenCentral()
        jcenter()
        maven { url 'https://jitpack.io' }
    }
}

//build.gradle(:app)里面添加
dependencies {
	implementation 'com.ooimi:toastx:1.1.3'
}
```

***2.设置ToastX的各种参数***

```java
ToastX.with(this)
	.text("我是在顶部的文字")//文字
	.backgroundColor(R.color.toast_background_color_config3_succeed)//背景颜色
    .animationMode(ToastX.ANIM_MODEL_SLIDE) //动画模式 弹出或者渐变
	.textColor(R.color.white)//文字颜色
	.position(ToastX.POSITION_TOP)//显示的位置 (顶部或者底部)
	.textGravity(Gravity.CENTER)//文字的位置
	.duration(1000)//显示的时间 单位ms
	.textSize(14f)//文字大小
	.padding(10,10)//左右内边距
	.margin(15,15)//左右外边距
	.height(100)//整个的高度
	.width(500) //整个的宽度
	.radius(40) //圆角半径
	.offset(10) //距离顶部或者底部的偏移量
	.show(); 	//显示
```



#### 自定义布局用法：

***使用自定义布局的话以下几个属性将失效：`text、backgroundColor、textColor、textGravity、textSize、padding、height、width、radius`***

```java
View inflate = LayoutInflater.from(this).inflate(R.layout.custom_toastx, null);
        ToastX.with(this)
                .customizeView(inflate)
                .animationMode(ToastX.ANIM_MODEL_FADE)
                .position(ToastX.POSITION_TOP)
                .duration(1000)
                .show();
```



#### 自定义布局用法带交互：

***使用自定义布局带交互的话以下几个属性将失效：`text、backgroundColor、textColor、textGravity、textSize、padding、height、width、radius`***

```java
View inflate = LayoutInflater.from(this).inflate(R.layout.custom_toastx, null);
	SnackBarX toast = ToastX.with(this);
	toast.customizeView(inflate);
	toast.animationMode(ToastX.ANIM_MODEL_FADE) ;//动画模式 弹出或者渐变
	toast.position(ToastX.POSITION_TOP);
	toast.duration(1000);
	toast.show();//显示
	
	inflate.setOnClickListener(new View.OnClickListener() {
	    @Override
	    public void onClick(View view) {
	        toast.dismiss();
	    }
	});
```

