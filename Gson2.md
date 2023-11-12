## Gson

[Gson](https://github.com/google/gson)是Google开源的一个JSON库，被广泛应用在Android开发中



### 使用

***在`app/build.gradle`中添加以下依赖配置：***

```kotlin
dependencies {
  implementation 'com.google.code.gson:gson:2.8.6'
}
```

***创建模型类：***

```kotlin
Account {
	var uid: String = "00001"
	var userName: String = "Freeman"
	var password: String = "password"
	var telNumber: String = "13000000000"

	override fun toString(): String {
		return "Account(uid='$uid', userName='$userName', password='$password', telNumber='$telNumber')"
	}
}
```

***Json转对象：***

```kotlin
//json转对象
val json = "{\"uid\":\"00001\",\"userName\":\"Freeman\",\"telNumber\":\"13000000000\"}"
val gson = Gson()
val account = gson.fromJson(json, Account::class.java)
Log.e("gson", "fromJson: ${account.toString()}")
```

***对象转 Json：***

```kotlin
//对象转json
val accountJson = gson.toJson(account)
Log.e("gson", "toJson: $accountJson")
```

***Json转集合：***

```kotlin
//json转集合
val jsonList= "[{\"uid\":\"00001\",\"userName\":\"Freeman\",\"telNumber\":\"13000000000\"}]"
val gson = Gson()
val accountList :List<Account> = gson.fromJson(jsonList,object :TypeToken<List<Account>>(){}.type)
Log.e("gson", "json转集合$accountList" )
```

***集合转 Json：***

```kotlin
//集合转jsonss
val accountListJson = gson.toJson(accountList)
Log.e("gson", "集合转json$accountListJson" )
```



### 插件

在https://github.com/wuseal/JsonToKotlinClass/releases/下载3.7.0版本，导入AndroidStudio

复制Json到AS，自动生成相对应的数据模型



