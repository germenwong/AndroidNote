## GreenDao

### 简介

一款开源的面向Android的轻便、快捷的==ORM==（对象关系映射：object Relational Mapping）框架，将Java对象映射到SQLite数据库中，我们操作数据库的时候，不再需要编写复杂的SQL语句，在性能方面，GreenDao针对Android进行了==高度优化，最小的内存开销、依赖体积小同时支持数据库加密==

### 使用

***1.添加依赖：***

在外层的build.gradle里添加一下代码：

```java
classpath 'org.greenrobot:greendao-gradle-plugin:3.3.0'//GreenDao
```

在内层(app内)的build.gradle里添加以下代码：

```java
apply plugin: 'com.android.application'
apply plugin: 'org.greenrobot.greendao' // apply plugin

implementation 'org-greenrobot:greendao:3.3.8' // add librany
```

***2.新建实体类***

```java
@Entity
public class Student{
	@Id(autoincrement = true)@Unique
    Long id;//long类型才能自增
    
    String name;
    int age;
    
    public Student(){
        
    }
    
    @Generated(hash = 352757281)
	public Student(Long id, String name, int age) {
		this.id = id;
		this.name = name;
		this.age = age;
    }
    
    public Long getId() {
		return this.id;
    }
	public void setId(Long id){
		this.id = id;
    }
	public String getName(){
		return this.name;
    }
    public void setName(String name){
		this.name=name;
    }
    public int getAge(){
		return this.age;
    }
	public void setAge(int age) {
		this.age = age;
    }
    
    @Override
	public String tostring() {
		return "Student{" +
			"id="+ id+
			", name='" + name + '\'" +
			", age=" + age +
		    '}'；
    }

}
```





***3、Make Project编译工程***

***4、初始化GreenDAO***

***5、获取DaoSession***

***6、通过DaoSession操作数据库***



### 注解

@Entity：表明这个实体类会在数据库中生成一个与之相对应的表

@Id：对应数据表中的id字段

@NotNull：设置数据表当前列不能为空

@Unique：表明该属性在数据库中只能由唯一值