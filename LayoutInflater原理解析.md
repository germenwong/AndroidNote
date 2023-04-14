### LayoutInflater的作用

LayoutInflater用于加载布局的。加载布局的任务通常都是在Activity中调用setContentView()方法来完成的。其实setContentView()方法的内部也是使用LayoutInflater来加载布局的，只不过这部分源码是内部的。





### LayoutInflater.inflate和View.inflate的区别

#### LayoutInflater.inflate()

（1）该方法适用于所有布局填充的的场景，但使用前需要先实例化LayoutInflater对象，获取LayoutInflater实例，使用getLayoutInflater();
这个方法可以在Activity和Fragment中使用，不过在Fragment中使用时，要传入一个bundle参数

```java
// Activity中使用
LayoutInflater layoutInflater = getLayoutInflater();
// Fragment中使用
LayoutInflater layoutInflater = getLayoutInflater(savedInstanceState);
```



（2）LayoutInflater.from(context).inflate(int resource,ViewGroup root，boolean attachToRoot)

**参数含义：**

* resource：布局资源id
* root：resource生成view对象要填入的父布局。为null，则返回的view就是布局资源；否则，需要参照第三个参数
* attachToRoot：是否将resource生成view对象填入root，以root作为最终返回view的根布局。  false，root不为null，则提供root的LayoutParams约束resource生成的view；true，root不为null，以root作为最终返回view的根布局



（3）平时Recyclerview加载item中，adapter的getView方法中，我们经常用到LayoutInflater.inflate这样的方法来加载布局xml，如果使用View.inflate()，没有将item的布局文件最外层的所有layout属性设置



#### View.inflate()

（1）这个是View类的静态方法，可直接调用，实际上还是使用了LayoutInflater，所以，它没有直接使用LayoutInflater.inflate()强大

**参数含义：**

- **context**：上下文
- **resource**：布局资源
- **root**：resource生成view对象要填入的父布局。为null，则返回resource生成view对象，否则将view填入到root中，以root作为根布局返回 







### 总结

View.inflate()是封装了LayoutInflater的inflate()方法，由于是静态方法，比较简便；但LayoutInflater相当于比View多了一个三个参数的inflate()方法，功能更强大