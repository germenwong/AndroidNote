## Retrofit

`Retrofit`是一个高质量高效率的HTTP请求库，和`OkHttp`同样出自Square公司。Retrofit内部依赖于OkHttp，它将OKHttp底层的代码和细节都封装了起来，功能上做了更多的扩展,比如返回结果的自动解析，网络引擎的切换，拦截器......

有了Retrofit之后对于一些请求我们就只需要一行代码或者一个注解、大大简化了网络请求的代码量。



### 注解

retrofit注解驱动型上层网络请求框架，使用注解来简化请求，大体分为以下几类：

- 用于标注网络请求方式的注解
- 标记网络请求参数的注解
- 用于标记网络请求和响应格式的注解

<img src="https://songyubao.com/book/primary/network/retrofit%E6%B3%A8%E8%A7%A3.png"/>

#### 请求方法注解

| 注解     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| @GET     | get请求                                                      |
| @POST    | post请求                                                     |
| @PUT     | put请求                                                      |
| @DELETE  | delete请求                                                   |
| @PATCH   | patch请求，该请求是对put请求的补充，用于更新局部资源         |
| @HEAD    | head请求                                                     |
| @OPTIONS | option请求                                                   |
| @HTTP    | 通用注解,可以替换以上所有的注解，其拥有三个属性：method，path，hasBody |

#### 请求头注解

| 注解     | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| @Headers | 用于添加固定请求头，可以同时添加多个。通过该注解添加的请求头不会相互覆盖，而是共同存在 |
| @Header  | 作为方法的参数传入，用于添加不固定值的Header，该注解会更新已有的请求头 |

#### **请求参数注解**

| 名称      | 说明                                                         |
| --------- | ------------------------------------------------------------ |
| @Body     | 多用于post请求发送非表单数据,比如想要以post方式传递json格式数据 |
| @Filed    | 多用于post请求中表单字段,Filed和FieldMap需要FormUrlEncoded结合使用 |
| @FiledMap | 和@Filed作用一致，用于不确定表单参数                         |
| @Part     | 用于表单字段,Part和PartMap与Multipart注解结合使用,适合文件上传的情况 |
| @PartMap  | 用于表单字段,默认接受的类型是Map，可用于实现多文件上传       |
| @Path     | 用于url中的占位符                                            |
| @Query    | 用于Get中指定参数                                            |
| @QueryMap | 和Query使用类似                                              |
| @Url      | 指定请求路径                                                 |

#### 请求和响应格式注解

| 名称            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| @FormUrlEncoded | 表示请求发送编码表单数据，每个键值对需要使用@Field注解       |
| @Multipart      | 表示请求发送multipart数据，需要配合使用@Part                 |
| @Streaming      | 表示响应用字节流的形式返回.如果没使用该注解,默认会把数据全部载入到内存中.该注解在在下载大文件的特别有用 |



### 使用

***添加依赖：***

```kotlin
implementation("com.squareup.okhttp3:okhttp:4.9.0")
// 网络请求日志打印
implementation("com.squareup.okhttp3:logging-interceptor:4.9.0")
implementation 'com.squareup.retrofit2:retrofit:2.9.0'//Retrofit
implementation 'com.squareup.retrofit2:converter-gson:2.9.0'//Gson
```

***创建 RetrofitManager：***

```kotlin
object RetrofitManager {

	private val client = OkHttpClient.Builder()
	      .connectTimeout(10, TimeUnit.SECONDS)//连接超时时间
	      .readTimeout(10, TimeUnit.SECONDS)//读取超时
	      .writeTimeout(10, TimeUnit.SECONDS)//写超时，也就是请求超时
	      .build()
	
	private var retrofit = Retrofit.Builder()
	      .client(client)
	      .baseUrl("https://www.wanandroid.com/project/list/")
	      .addConverterFactory(GsonConverterFactory.create())
	      .build()
	
	fun <T> create(clazz: Class<T>):T{
	      return retrofit.create(clazz)
	}
}

interface ApiService {
	@GET("1/json")
	fun getData(@Query(value = "cid",encoded = true) cid:Int): Call<String>
}
```

***外部使用：***

```kotlin
val apiService = RetrofitManager.create(ApiService::class.java)
apiService.getData(294).enqueue(object : retrofit2.Callback<String> {
	override fun onResponse(call: Call<String>, response: Response<String>) {
		Log.e("retrofit", "请求成功： ${response.body() .toString() ?: "response is null"}")
	}

	override fun onFailure(call: Call<String>, t: Throwable) {
		Log.e("retrofit", "请求失败： ${t.message}")
	}
})
```

