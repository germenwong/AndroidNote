## OKHttp

<img src="https://songyubao.com/book/primary/network/OkHttp.png"/>

------





### 开始使用

***在AndroidManifest.xml添加网络访问权限***

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

***添加依赖***

```kotlin
implementation("com.squareup.okhttp3:okhttp:4.9.0")
// 网络请求日志打印
implementation("com.squareup.okhttp3:logging-interceptor:4.9.0")
```

***明文流量请求的问题***

安卓在Android 9.0（API 28）版本，限制了http明文流量的网络请求，未加密的流量请求都会被系统禁掉。

所以如果当前应用的请求是 http 请求，除非使用 https，否则就会导系统禁止当前应用进行该请求，所以避免使用明文流量的原因是缺乏机密性，真实性和防篡改保护；网络攻击者可以窃听所传输的数据，并且还可以对其进行修改而不会被检测到。

在`AndroidManifest.xml`清单文件的`application`中加入：

```kotlin
android:usesCleartextTraffic="true"
```

***初始化***

```kotlin
 val client = OkHttpClient.Builder() //builder构造者设计模式
	.connectTimeout(10, TimeUnit.SECONDS)//连接超时时间
	.readTimeout(10, TimeUnit.SECONDS)//读取超时
	.writeTimeout(10, TimeUnit.SECONDS)//写超时，也就是请求超时
	.addInterceptor(LoggingInterceptor())
	.build()
```

------







### Get请求

***同步：***同步GET的意思是一直等待http请求, 直到返回了响应. 在这之间会阻塞线程, 所以同步请求不能在Android的主线程中执行, 否则会报错NetworkMainThreadException.

```kotlin
//Get同步请求
fun get() {
	Thread(Runnable {
		//构造请求体
		val request: Request = Request.Builder()
			.url(BASE_URL)
			.build()
		
		//构造请求对象
		val call = client.newCall(request)
		//发起同步请求（execute）
		val response = call.execute()
		val body = response.body?.string()
		Log.e("okhttp", "get response :${body}")
	}).start()
}
```

发送同步`GET`请求很简单：

1. 创建`OkHttpClient`实例`client`
2. 通过`Request.Builder`构建一个`Request`请求实例`request`
3. 通过`client.newCall(request)`创建一个`Call`的实例
4. `Call`的实例调用`execute`方法发送同步请求
5. 请求返回的`response`转换为`String`类型返回



***异步：***GET是指在另外的工作线程中执行http请求, 请求时不会阻塞当前的线程, 所以可以在Android主线程中使用.

`onFailure`，`onResponse`的回调是在子线程中的,我们需要切换到主线程才能操作UI控件 

```kotlin
//Get异步请求
fun getAsync() {
	//构造请求体
	val request: Request = Request.Builder()
		.url(BASE_URL)
		.build()
	
	//构造请求对象
	val call = client.newCall(request)
	//发起异步请求（execute）
	call.enqueue(object : Callback {
		override fun onFailure(call: Call, e: IOException) {
			Log.e("okhttp", "请求失败:${e.message}")
		}
		override fun onResponse(call: Call, response: Response) {
			val body = response.body?.string()
			Log.e("okhttp", "请求成功:${body}")
		}
	})
}
```

异步请求的步骤和同步请求类似，只是调用了`Call`的`enqueue`方法异步请求，结果通过回调`Callback`的`onResponse`方法及`onFailure`方法处理。

看了两种不同的Get请求，基本流程都是先创建一个`OkHttpClient`对象，然后通过`Request.Builder()`创建一个`Request`对象，`OkHttpClient`对象调用`newCall()`并传入`Request`对象就能获得一个`Call`对象。

而同步和异步不同的地方在于`execute()`和`enqueue()`方法的调用，

调用`execute()`为同步请求并返回`Response`对象；

调用`enqueue()`方法测试通过callback的形式返回`Response`对象。

> 注意：无论是同步还是异步请求，接收到`Response`对象时均在子线程中，`onFailure`，`onResponse`的回调是在子线程中的,我们需要切换到主线程才能操作UI控件 



### Post请求

POST请求与GET请求不同的地方在于`Request.Builder`的`post()`方法，`post()`方法需要一个`RequestBody`的对象作为参数

***同步：***

```kotlin
//Post同步请求
fun post() {
	val body = FormBody.Builder()
		.add("username", "Anthony_Coder")
		.add("password", "hgm031201")
		.build()
	
	val request = Request.Builder()
		.url(BASE_URL2)
		.post(body)
		.build()
	
	Thread(Runnable {
		val response = client.newCall(request).execute()
		val responseBody = response.body?.string()
		Log.e("okhttp", "post response: $responseBody")
	}).start()
}
```

和`GET`同步请求类似，只是创建`Request`时通过`Request.Builder.post()`方法设置请求类型为`POST`请求并设置了请求体。

***异步提交：***

```kotlin
//Post异步请求
fun postAsync() {
	val body = FormBody.Builder()
		.add("username", "Anthony_Coder")
		.add("password", "hgm031201")
		.build()
	
	val request = Request.Builder()
		.url(BASE_URL2)
		.post(body)
		.build()
	
	client.newCall(request).enqueue(object : Callback {
		override fun onFailure(call: Call, e: IOException) {
			Log.e("okhttp", "post失败: ${e.message}")
		}
		override fun onResponse(call: Call, response: Response) {
			Log.e("okhttp", "post成功: ${response.body?.string()}")
		}
	})
}
```

***异步文件上传：***

```kotlin
//异步表单文件上传
val file = File(Environment.getExternalStorageDirectory(), "1.png")
if (!file.exists()) {
    Toast.makeText(this, "文件不存在", Toast.LENGTH_SHORT).show()
         return
}

val muiltipartBody: RequestBody = MuiltipartBody.Builder() 
     .setType(MultipartBody.FORM)//一定要设置这句
     .addFormDataPart("username", "admin") //
     .addFormDataPart("password", "admin") //
     .addFormDataPart( "file", "1.png",RequestBody.create(MediaType.parse("application/octet-stream"), file))
      .build()
```

***异步提交字符串：***

```kotlin
val mediaType = MediaType.parse("text/plain;charset=utf-8")
val body = "{username:admin, password:admin}"
RequestBody body = RequestBody.create(mediaType,body);

val request = new Request.Builder()
      .url(url)
      .post(body)
      .build();

val call = client.newCall(request)
call.enqueue(new Callback(){
  @Override
   public void onFailure(Request request, IOException e){

   }

   @Override
   public void onResponse(final Response response) throws IOException{
       // 回调的结果是在子线程中的,我们需要切换到主线程才能操作UI控件 
       String response =  response.body().string();
   }
}
```



### 自定义拦截器

拦截器是OkHttp当中一个比较强大的机制，可以监视、重写和重试调用请求。

这是一个比较简单的`Interceptor`的实现，对请求的发送和响应进行了一些信息输出。

```kotlin
//在初始化的时候添加拦截器
.addInterceptor(LoggingInterceptor())
```

```kotlin
//自定义拦截器
class LoggingInterceptor : Interceptor {
	override fun intercept(chain: Interceptor.Chain): Response {
		val time_start = System.nanoTime()
		val request = chain.request()
		val response = chain.proceed(request)
		
		val buffer = Buffer()
		request.body?.writeTo(buffer)
		val requestBodyStr = buffer.readUtf8()
		
		Log.e("okhttp", String.format("Sending request %s with params %s", request.url, requestBodyStr))

	val data = response.body?.string() ?: "response body is null"
	//这里不能直接把response返回出去，因为这里获取了一次数据流之后，就不能再使用response了，不然会报错
	//所以需要重新构造一个response返回出去
	val mediaType = response.body?.contentType()
	val newBody = ResponseBody.create(mediaType, data)
	val newResponse = response.newBuilder().body(newBody).build()
	
	val time_end = System.nanoTime()
	Log.e("okhttp", String.format("Received response for %s in %.1fms  >>> %s",request.url,(time_end-time_start)/1e6,data) )
            
	return newResponse
	}
}
```

------





### OkHttpManager封装

```kotlin
//单例模式
object OkHttpManager{
		//创建OKHttpClient实例
		private val client = OkHttpClient.Builder() //builder构造者设计模式
		    .connectTimeout(10, TimeUnit.SECONDS)//连接超时时间
		    .readTimeout(10, TimeUnit.SECONDS)//读取超时
		    .writeTimeout(10, TimeUnit.SECONDS)//写超时，也就是请求超时
		    .addInterceptor(HttpLoggingInterceptor())//拦截器
		    .build()
  
  
  	//Get请求
  	fun getData(url: String , callback: okhttp3.Callback){
      	val request=Request.Builder()
      			.url(url)
      			.build
      	client.newCall(request).enqueue(callback)
    }
  
  	
  	//Post请求
  	fun postData(url: String , requestBody: RequestBody , callback: okhttp3.Callback){
      	val request=Request.Builder()
      			.url(url)
      			.post(requestBody)
      			.build
      	client.newCall(request).enqueue(callback)
    }
}
```

外部直接使用工具类：

```kotlin
OkHttpManager.getData("https://47.107.231.209:20000/hot",object : Callback{
  	override fun onFailure(call: Call, e: IOException) {
 				//请求失败
    }
 
		override fun onResponse(call: Call, response: Response) {
				//请求成功
				//注意在此函数内是子线程不能直接更新ui
		}
})


//Post请求需要先bulid requestBody
val requestBody = FormBody.Builder()
    .add("username", username)
    .add("password", password)
    .build()
OkHttpManager.postData("https://47.107.231.209:20000/admin/login" , requestbody , object : Callback{
  	override fun onFailure(call: Call, e: IOException) {
 				//请求失败
    }
 
		override fun onResponse(call: Call, response: Response) {
				//请求成功
		}
})
```

