## Android项目各个文件用途详解

### Android 工程目录

<img src="https://img-blog.csdn.net/20180514195410446?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MzEyMjMw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"/>

***1、.gradle和.idea***

这两个目录下放置的都是Android Studio自动生成的一些文件，我们无须关心，也不要去手动编辑。

***2、app***
项目中的代码、资源等内容几乎都是放置在这个目录下的，我们后面的开发工作也基本都是在这个目录下进行的，待会儿还会对这个目录单独展开进行讲解。

***3、build***
这个目录你也不需要过多关心，它主要包含了一些在编译时自动生成的文件。

***4、gradle***
这个目录下包含了gradle wrapper的配置文件，使用gradle wrapper的方式不需要提前将gradle下载好，而是会自动根据本地的缓存情况决定是否需要联网下载gradle。Android Studio默认没有启动gradle wrapper的方式，如果需要打开，可以点击Android Studio导航栏 --> File --> Settings --> Build，Execution，Deployment --> Gradle，进行配置更改。

***5、.gitignore***
这个文件是用来将指定的目录或文件排除在版本控制之外的。

***6、build.gradle***
这是项目全局的gradle构建脚本，通常这个文件的内容是不需要修改的。下面回详细分析gradle构建脚本中的具体内容。

***7、gradle.properties***
这个文件是全局的gradle配置文件，在这里配置的属性将会影响到项目中所有的gradle编译脚本。

***8、gradlew和gradlew.bat***
这两个文件是用来在命令行界面中执行gradle命令的，其中gradlew是在Linux或Mac系统中使用的，gradlew.bat是在Windows系统中使用的。

***9、HelloWorld.iml***
iml文件是所有IntelliJ IDEA项目都会自动生成的一个文件（Android Studio是基于IntelliJ IDEA开发的），用于标识这是一个IntelliJ IDEA项目，我们不需要修改这个文件中的任何内容。

***10、local.properties***
这个文件用于指定本机中的Android SDK路径，通常内容都是自动生成的，我们并不需要修改。除非你本机中的Android SDK位置发生了变化，那么就将这个文件中的路径改成新的位置即可。

***11、settings.gradle***
这个文件用于指定项目中所有引入的模块。由于HelloWorld项目中就只有一个app模块，因此该文件中也就只引入了app这一个模块。通常情况下模块的引入都是自动完成的，需要我们手动去修改这个文件的场景可能比较少。

------



### app目录结构

<img src="https://img-blog.csdn.net/20180514195456663?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MzEyMjMw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"/>

***1、build***
这个目录和外层的build目录类似，主要也是包含了一些在编译时自动生成的文件，不过它里面的内容会更多更杂，我们不需要过多关系。

***2、libs***
如果你的项目中使用到了第三方jar包，就需要把这些jar包都放在libs目录下，放在这个目录下的jar包都会被自动添加到构建路径里去。

***3、src/AndroidTest***
此处是用来编写Android Test测试用例的，可以对项目进行一些自动化测试。

***4、src/main/java***
毫无疑问，java目录是放置我们所有java代码的地方，展开该目录，你将看到我们刚才创建的HelloWorldActivity文件就在里面。

***5、src/main/res***
这个目录下的内容就有点多了。简单点说，就是你在项目中使用到的所有图片，布局，字符串等资源都要存放在这个目录下。当然这个目录下还有很多子目录，图片放在drawable目录下，布局放在layout目录下，字符串放在values目录下，所以你不用担心会把整个res目录弄得乱糟糟的。

***6、main/AndroidManifest.xml***
这是你整个Android项目的配置文件，你在程序中定义的所以四大组件都需要在这个文件里注册，另外还可以在这个文件中给应用程序添加权限声明。

***7、test***
此处是用来编写Unit Test测试用例的，是对项目进行自动化测试的另一种方式。

***8、.gitignore***
这个文件用于将app模块内的指定的目录或文件排除在版本控制之外，作用和外层的.gitignore文件类似。

***9、app.iml***
IntelliJ IDEA项目自动生成的文件，我们不需要关心或修改这个文件中的内容。

***10、build.gradle***
这是app模块的gradle构建脚本，这个文件中会指定很多项目构建相关的配置。

***11、proguard-rules.pro***
这个文件用于指定项目代码的混淆规则，当代码开发完成后打成安装包文件，如果不希望代码被别人破解，通常会将代码混淆，从而让破解者难以阅读。

------



### res目录的结构

<img src="https://img-blog.csdn.net/20180514200323657?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5MzEyMjMw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"/>

***以 ==drawable== 开头的文件夹是用来放图片、背景图的；***

***以 ==layout== 开头的文件夹是用来放布局文件的；***

***以 ==mipmap== 开头的文件夹是用来放icon应用图标的；***

***以 ==values== 开头的文件夹是用来放字符串、样式、颜色等配置的；***

------



### 如何存放图片与图标

***android的drawable文件一共可以有：***

* drawable-ldpi(低密度) 
* drawable-mdpi(中等密度) 
* drawable-hdpi(高密度)
* drawable-xhdpi(超高密度) 
*  drawable-xxhdpi(超超高密度)  
* drawable-xxxhdpi(超超超高密度) 
* drawable-nohdpi(无缩放) 
* 当然还加上默认的drawable。

比如在一个中等分辨率的手机上，Android就会选择drawable-mdpi文件夹下的图片，文件夹下有这张图就会优先被使用，在这种情况下，图片是不会被缩放的；

但是如果没有在drawable-mdpi的文件夹下找到相应图片的话，Android系统会首先从更高一级的drawable-hdpi文件夹中查找，如果找到图片资源就进行缩放处理，显示在屏幕上；

如果drawable-hdpi文件夹下也没有的话，就依次往=drawable-xhdpi文件夹、drawable-nodpi、drawable-xxhdpi文件夹、drawable-xxxhdpi文件夹；

如果更高密度的文件夹里都没有找到，就往更低密度的文件夹里寻找，drawable-ldpi文件夹下查找，以此类推；

如果都没找到，最终会在默认的drawable文件夹中寻找，如果默认的drawable文件夹中也没有那就会报错啦。（前提是把一张图片做成很多不同的分辨率放在各个对应密度的drawable文件夹下）。

***计算手机 dpi（像素点）：***

```java
float xdpi = getResources().getDisplayMetrics().xdpi;
float ydpi = getResources().getDisplayMetrics().ydpi;
```

***dpi范围密度对应范围：***

<img src="https://img-blog.csdnimg.cn/20190312171428378.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTExNjk4,size_16,color_FFFFFF,t_70"/>

***分辨率的计算方式如下：***

<img src="https://img-blog.csdnimg.cn/20190312171859536.png"/>

***dpi范围密度对应 icon尺寸：***

<img src="https://img-blog.csdnimg.cn/20190312171531728.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTExNjk4,size_16,color_FFFFFF,t_70"/>

------



### 存放位置不同对占用内存影响

***相关文章：***

https://blog.csdn.net/zhaokaiqiang1992/article/details/49787117

https://blog.csdn.net/zhaokaiqiang1992/article/details/45419023