## 介绍

..



## 隐藏密钥

在 `.gitignore` 文件中定义的路径或者文件都不会随着代码上传到远程存储库中（忽略上传），此文件中就包含了 `local.properties` 该文件由 `Android Studio` 自动生成，该文件不应签入版本控制系统，因为它包含特定于您的本地配置的信息、SDK 的位置。这仅由 `Gradle` 使用。

首先在 `local.properties` 文件中定义我们的密钥字段

```kotlin
API_KEY = abc
```

在应用级的 `build.gradle` 文件中载入配置后进行 `Sync Now` 同步，最后重新 `build` 一下项目。

```kotlin
android {
    ...

    defaultConfig {
        ...

        //创建一个构建配置字段，在编译期间创建
        //1. 读取项目根路径从本地属性文件中访问读取API密钥
        Properties properties = new Properties()
        properties.load(project.rootProject.file("local.properties").newDataInputStream())
        //2. 读取特定文件的字段
        buildConfigField "String", "API_KEY", "\"${properties.getProperty("API_KEY")}\""
    }
    
    //新版本需要开启自定义BuildConfig功能，否则报错
    buildFeatures {
        buildConfig = true
    }
}
```

在 `MainActivity` 中使用我们的密钥，目前为止我们的密钥就可以很好保存在本地计算机中，防止在存储库中泄露。

```kotlin
class MainActivity : ComponentActivity() {
	override fun onCreate(savedInstanceState: Bundle?) {
		super.onCreate(savedInstanceState)
		//访问API密钥
		val apiKey=BuildConfig.API_KEY
		
		setContent {
			HideAPIKeyTheme { }
		}
	}
}
```





## 保护密钥

在逆向工程中，攻击者会获取我们的 `apk` 进行反编译获取有价值的信息，但是密钥始终要在项目中存在以进行网络调用，我们不能百分百保护我们的密钥，唯一能做的就是开启代码混淆，让代码变得不可读降低攻击者获取密钥的可能性