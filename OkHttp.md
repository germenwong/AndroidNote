## OkHttp

### 什么是OkHttp

一个处理网络请求的开源项目，使用广泛的网络框架。***官网：***https://square.github.io/okhttp/

支持HTTP/2并允许对同一主机的所有请求共享一个套接字；
如果非HTTP/2，则通过连接池，减少了请求延迟；
默认请求GZip压缩数据；
响应缓存，避免了重复请求的网络；



### 开启权限

***1.（安全权限）在res -> 新建xml目录 -> 新建 network_security_config.xml文件  -> 并且清单文件引用***

```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true" />
</network-security-config>
```

```xml
<application
		...
		android:networkSecurityConfig="@xml/network_security_config">
		
</application>
```

***2.（网络权限）在AndroidManifest文件添加两行代码***

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```



### 具体用法

```java
//创建OkHttpClient实例
OkHttpClient okHttpClient = new OkHttpClient();
//必须开启个子线程
new Thread() {
    @Override
    public void run() {
        //创建请求对象
        Request request = new Request.Builder().url("服务器url").build();
		//准备好请求的Call对象
		Call call = okHttpClient.newCall(request);
		try {
		    Response response = call.execute();
            //输出响应体
		    Log.d(TAG, "getSync: " + response.body().string());
		} catch (IOException e) {
		    e.printStackTrace();
		}
	}
}.start();
```



### 同步请求

通过OkHttpClient的实例调用newCall()方法来创建一个Call对象，并调用execute()方法执行同步请求来获取服务器返回的数据，代码如下：

```java
Response response = okHttpClient.newCall(request).execute();
```



### 异步请求

通过OkHttpClient的实例调用newCall()方法来创建一个Call对象，并调用enqueue()方法执行异步请求来获取服务器返回的数据，代码如下：

```java
call.newCall(request).enqueue(new Callback() {
    @Override
    public void onFailure(@NonNull Call call, @NonNull IOException e) {
		//失败
    }
    @Override
    public void onResponse(@NonNull Call call, @NonNull Response response) throws IOException {
		//成功
	}
});
```



### POST请求

协议规定POST提交的数据必须放在请求体中，但协议并没有规定数据必须使用什么编码数据

***常用编码数据方式：***https://www.runoob.com/http/http-content-type.html

```java
//创建RequestBody对象来存储提交的数据
FormBody formBody = new FormBody.Builder()
    .add("username", "hgm")
    .add("password", "123456")
    .build();

//Request.Builder中调用post()方法，将RequestBody对象传入
Request request = new Request.Builder()
    .url("https://www.baidu.com")
    .post(formBody)
    .build();
```

```java
//数据被编码为名称/值对，默认类型；
Content-Type: application/x-www-form-urlencoded
//数据被编码为一条消息，一般用于文件上传；
Content-Type: multipart/form-data
//提交二进制数据，如果用于文件上传，只能上传一个文件；
Content-Type: application/octet-stream
//提交json数据
Content-Type: application/json
```



### okhttp配置

***拦截器***

```java
OkHttpClient okHttpClient = new OkHttpClient.Builder().addinterceptor(new XXX).build();
OkHttpClient okHttpClient = new OkHttpClient.Builder().addNetworkInterceptor(new XXX).build();
```

***缓存和Cookie***

okhttp按照Http协议规则实现了缓存的处理，缓存是比如：当我们发起第一次请求之后，如果后续还需要进
行同样的请求，此时如果符合缓存规则，则可以减少与服务器的网络通信，直接从本地文件缓存中读取响应
返回给请求者。==但是默认情况下，okhttp的缓存是关闭状态，需要我们开启==

Cookie是某些网站为了辨别用户身份，进行会话跟踪（比如确定登陆状态）而储存在用户本地终端上的数据（通常经过加密）由用户客户端计算机暂时或永久保存的信息

```java
//pathname:缓存文件地址，maxSize:缓存最大容量字节
OkHttpClient okHttpClient = new OkHttpClient.Builder().cache(
new Cache(new File( pathname: "/path/cache"), maxSize: 100)).build();
```

***两者区别：***一个在前面执行，一个后面执行



### okhttp与Retrofit

<img src="https://img-blog.csdnimg.cn/20210407144559955.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMwNjIxMzMz,size_16,color_FFFFFF,t_70"/>
