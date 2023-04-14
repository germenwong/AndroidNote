## 视频播放

### 简介

VideoView是Android用来实现视频播放的主要控件，VideoView内部是使用MediaPlayer来对视频文件进行控制的。但是他支持的格式比较单一，只支持mp4,avi,3gp等格式的视频。

### 常用方法

| 方法名         | 说明                      |
| -------------- | ------------------------- |
| setVideoPath() | 播放视频文件的位置        |
| start()        | 播放视频                  |
| pause()        | 暂停视频                  |
| resume()       | 将视频从头开始播放        |
| seekTo()       | 从指定位置开始播放        |
| isPalying()    | 判断当前是否正在播放视频  |
| getDuration()  | 获取载入视频时长          |
| suspend()      | 释放VideoView所占用的资源 |

### 使用步骤

***1.添加权限***

```xml
<uses-permission android:name="android.permission.INTERNET "/>
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```

***2.在xml布局添加VideoView控件***

```xml
<VideoView
        android:id="@+id/videoView"
        android:layout_width="match_parent"
        android:layout_height="200dp"/>
```

***3.Activity代码如下：***

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    VideoView videoView;
    Button btn1, btn2, btn3;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        videoView = findViewById(R.id.videoView);
        btn1 = findViewById(R.id.btn_play);
        btn1.setOnClickListener(this::onClick);
        btn2 = findViewById(R.id.btn_stop);
        btn2.setOnClickListener(this::onClick);
        btn3 = findViewById(R.id.btn_replay);
        btn3.setOnClickListener(this::onClick);

		//权限
        if (ContextCompat.checkSelfPermission(MainActivity.this, Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED){
            ActivityCompat.requestPermissions(MainActivity.this,new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE},1);
        }else {
            initVideoPath();
        }
    }

    //初始化视频路径
    private void initVideoPath() {
        File file = new File(Environment.getExternalStorageDirectory()+"/Movies","video.mp4");
        //指定视频文件路径
        videoView.setVideoPath(file.getPath()); 
    }


    @Override
    public void onRequestPermissionsResult(int requestCode, @NonNull String[] permissions, @NonNull int[] grantResults) {
        switch (requestCode){
            case 1:
                if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED){
                    initVideoPath();
                }else {
                    Toast.makeText(this,"权限拒绝",Toast.LENGTH_LONG).show();
                    finish();
                }
                break;
            default:
        }
    }


    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.btn_play:
                if (!videoView.isPlaying()) {
                    videoView.start();
                }
                break;
            case R.id.btn_stop:
                if (videoView.isPlaying()) {
                    videoView.pause();
                }
                break;
            case R.id.btn_replay:
                if (videoView.isPlaying()) {
                    videoView.resume();
                }
                break;
        }
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (videoView != null) {
            //释放资源
            videoView.suspend();
        }
    }
```

