## SharePreferences

### 简介

Android平台用于存储轻量级数据的存储方式，以键值对（key-value）的方式来进行存储，本质上是一个xml 文件。



### 使用

***保存数据一般分为四个步骤：***

1. 使用Activity类的getSharedPreferences方法获得SharedPreferences对象；
2. 使用SharedPreferences接口的edit获得SharedPreferences.Editor对象；
3. 通过SharedPreferences.Editor接口的putXXX方法保存key-value对；
4. 通过过SharedPreferences.Editor接口的commit方法保存key-value对。

***读取数据一般分为两个步骤：***

1. 使用Activity类的getSharedPreferences方法获得SharedPreferences对象；
2. 通过SharedPreferences对象的getXXX方法获取数据；



### 具体代码

***存储数据代码如下：***

```java
//获取输入框的内容
String str = editText.getText().toString().trim();
//获取 SharedPreferences对象 ，创建名为data的文件存储
SharedPreferences shp = getSharedPreferences("data", MODE_PRIVATE);
SharedPreferences.Editor editor = shp.edit();
//提交内容
editor.putString("name", str);
//判断是否保存成功
boolean isSaveSuccess = editor.commit();
if (isSaveSuccess) {
	Toast.makeText(MainActivity.this, "保存成功", Toast.LENGTH_SHORT).show();
}
```

***读取数据代码如下：***

```java
//读取存储在文件的内容
SharedPreferences shp = getSharedPreferences("data", MODE_PRIVATE);
String s = shp.getString("name", "无参数");
textView.setText(s);
```



### 两种提交的区别

![image-20211122194806227](SharePreferences\image-20211122194806227.png)

### 文件操作模式

![image-20220408135528882](SharePreferences\image-20220408135528882.png)