## ButterKnife

### 简介

中文名：***黄油刀***，一款Android视图的字段和方法绑定的快速注解框架

### 为什么使用它

* 可以不用不断的重复`findViewById`
* 强大的View绑定和Click事件处理功能，简化代码，提升开发效率
* 方便的处理Adapter里的ViewHolder绑定问题
* 运行时不会影响APP效率，使用配置方便
* 代码清晰，可读性强



### 使用步骤

***1.添加依赖***

```java
implementation 'com.jakewharton:butterknife:8.8.1'
annotationProcessor 'com.jakewharton:butterknife-compiler:8.8.1'
```

***2.Activity下的代码：***

```java
public class MainActivity extends AppCompatActivity {

    @BindView(R.id.btn_login)
    Button btnLogin;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.bind(this);
    }

    @OnClick(R.id.btn_login) public void login(){
       Toast.makeText(MainActivity.this,"点击了按钮",Toast.LENGTH_SHORT).show();
    }
}
```

***如果想在Library projects(库)中使用需按一下步骤：***

1.`build.gradle`中添加如下配置:

```java
buildscript {
  repositories {
    mavenCentral()
   }
  dependencies {
    classpath 'com.jakewharton:butterknife-gradle-plugin:8.8.1'
  }
}
```

2.在Library的`build.gradle`中添加如下配置:

```java
apply plugin: 'com.android.library'
apply plugin: 'com.jakewharton.butterknife'
```

```java
class ExampleActivity extends Activity {
  @BindView(R2.id.user) EditText username;
  @BindView(R2.id.pass) EditText password;
...
}
```

> 值得注意的是：在Library中使用Butterknife注解使用的是R2.而不是R.



### 使用场景

#### ***1.在Activity中绑定：***

> 值得注意的是:ButterKnife.bind(this)必须在初始化绑定布局文件之后,否则会报错

```java
public class MainActivity extends AppCompatActivity {
    
  @BindView(R.id.btn)
  Button button;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ButterKnife.bind(this);
    }

    @OnClick(R.id.btn)
    public void onClick(){
        Toast.makeText(this, "绑定单个view事件", Toast.LENGTH_SHORT).show();
    }
}
```

#### ***2.在Fragment中绑定：***

> 值得注意的是:在Fragment中需要在视图销毁时解绑Butterknife,否则会造成内存泄漏

```java
public class ExampleFragment extends Fragment {

    private Unbinder unbinder;
    @BindView(R.id.example)
    Button example;

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
       View view = View.inflate(getContext(),R.layout.fragment_example,null);
        unbinder = ButterKnife.bind(this,view);
        return view;

    }

    @Override
    public void onDestroyView() {
        super.onDestroyView();
        unbinder.unbind();//视图销毁时必须解绑
    }
}
```

#### ***3.在Adapter的ViewHolder中绑定：***

```java
 @NonNull
    @Override
    public MyViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        View itemView = LayoutInflater.from(parent.getContext()).inflate(R.layout.recy_dynamic_state_item,parent,false);
        MyViewHolder myViewHolder = new MyViewHolder(itemView);//此处将view传入
        itemView.setOnClickListener(this);
        return myViewHolder;
    }

public class MyViewHolder extends RecyclerView.ViewHolder {
        @BindView(R.id.tv_title)
        TextView tvTitle;
        @BindView(R.id.tv_detail)
        TextView tvDetail;

        public MyViewHolder(View itemView) {
            super(itemView);
            ButterKnife.bind(this,itemView);//此处进行绑定
        }
```



### 具体详解

#### ***绑定View：***

1.单个控件id 注解： @BindView（）

```java
@BindView(R.id.example)
Button example;
```

2.布局内多个控件id 注解： @BindViews（）

```java
@BindViews({R.id.butter,R.id.butter1,R.id.butter2})
List<Button> buttons;
```



#### ***绑定资源文件：***

1.绑定string 字符串：@BindString()

```java
@BindString(R.string.app_name) //绑定资源文件中string字符串
String name;
```

2.绑定array数组：@BindArray()

```xml
<resources>
    
<string name="app_name">开眼视频</string>
<string-array name="weather">
<item>高温</item>
<item>低温</item>
<item>阴天</item>
<item>雨天</item>
<item>晴天</item>
</string-array>

</resources>
```

```java
@BindArray(R.array.weather) //绑定string资源里面array数组
String [] weathers ;
```

3.绑定颜色值：@BindColor( )

```java
@BindColor( R.color.colorPrimary) //绑定color文件中颜色值
int colorPrimary; 
```

4.绑定Bitmap：@BindBitmap( )

```java
@BindBitmap(R.mipmap.ic_launcher)
Bitmap bitmap;
```

5.其他资源绑定:

```java
@BindBool(R.bool.is_tablet) boolean isTablet; //绑定真假boolean
@BindFont(R.font.comic_sans) Typeface comicSans; //绑定字体文字
@BindDimen(R.dimen.horizontal_gap) int gapPx //绑定尺寸
@BindDimen(R.dimen.horizontal_gap) float gap; //绑定尺寸
@BindAnim(R.anim.fade_in) Animation fadeIn; //绑定动画
@BindDrawable(R.drawable.placeholder) Drawable placeholder;//绑定Drawable
```



#### ***绑定监听事件：***

1.绑定控件点击事件：@OnClick( )

* 单个控件点击事件：

```java
@OnClick(R.id.butter)
public void onClick(){
    Toast.makeText(this, "绑定单个view事件", Toast.LENGTH_SHORT).show();
}
```

* 多个控件同一点击事件：

```java
@OnClick({R.id.share_wechat,R.id.share_moments,R.id.share_weibo})
public void onClick(){
    Toast.makeText(this, "都执行此事件", Toast.LENGTH_SHORT).show();
}
```

* 多个控件点击事件:

```java
@OnClick({R.id.share_wechat,R.id.share_moments,R.id.share_weibo})
public void onClick(View v) {
    switch (v.getId()){
        case R.id.share_wechat:
            sharePlatform(Wechat.NAME);
            break;
        case R.id.share_moments:
            sharePlatform(WechatMoments.NAME);
            break;
        case R.id.share_weibo:
            sharePlatform(SinaWeibo.NAME);
            break;
    }
}
```

2.绑定控件长按事件：@OnLongClick( )

```java
@OnLongClick( R.id.button1 ) //给 button1 设置一个长按事件
public boolean onLongClick (){
	Toast.makeText(this, "is a long click", Toast.LENGTH_SHORT).show();
	return true ;
}
```

3.其他事件绑定

```java
@OnEditorAction(R.id.example)
public  boolean onEditorAction(KeyEvent key) {
    
    return true;
  }

//焦点改变
@OnFocusChange(R.id.example) 
public void onFocusChanged(boolean focused) {
   
  }

//Item长按，返回true则可以拦截onItemClick
@OnItemLongClick(R.id.example_list) 
public boolean onItemLongClick(int position) {
    
    return true;
  }

//Item点击事件
@OnItemClick(R.id.example_list) 
public void onItemClick(int position) {
  
}

//Item被选择事件
@OnItemSelected(R.id.example_list) 
public void onItemSelected(int position) {
    
}

//EditText里面的文本变化事件
@OnTextChanged(R.id.example) 
public void onTextChanged(CharSequence text) {
    
}

//页面改变事件
@OnPageChange(R.id.example_pager) 
public void onPageSelected(int position) {
   
}

//触摸事件
@OnTouch(R.id.example)
public  boolean onTouch() {
    return false;
}
```

