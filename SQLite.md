## SQLite

### 简介

SQLite是一个==轻量级==的关系型数据库，==运算速度快==，==占用资源少==，很适合在移动设备上使用， 不仅支持标准SQL语法，还遵循ACID(数据库事务)原则，无需账号，使用起来非常方便！

### 特点

* 轻量级
* 独立性
* 隔离性
* 跨平台
* 多语言接口
* 安全性

SQlite通过文件来保存数据库，一个文件就是一个数据库，数据库中又包含多个表格，表格里又有
多条记录，每个记录由多个字段构成，每个字段有对应的值，每个值我们可以指定类型，也可以不
指定类型(==主键除外==)

### 数据库的创建

SQLiteOpenHelper：帮助类，我们通过继承该类，然后重写数据库创建以及更新的方法， 我们还可以通过该类的对象获得数据库实例，或者关闭数据库！

### 实现步骤

***1.新建Sqlite类并继承SQLiteOpenHelper***

```java
public class MySqlite extends SQLiteOpenHelper {
    public MySqlite(@Nullable Context context) {
        super(context, "mySqlite.db", null, 1);
        //第二个参数：数据库名称
        //第三个参数：一般为空
        //第四个参数：版本号
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        String sql = "create table user(id Integer, name varchar(10), age Integer)";
        db.execSQL(sql);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {

    }
}
```

***2、实例化Sqlite类，设置读写权限***

```java
MySqlite mySqlite = new MySqlite(getApplicationContext());
database = mySqlite.getWritableDatabase();
```

***3、操作数据库***

添加

```java
//1.插入sql
String sql = "insert into user(id, name, age) values(?, ?, ?)";
//2.使用execSQL执行执行sql
database.execSQL(insertSql, new Object[]{
    Integer.parseInt(number.getText().toString()),
    name.getText().toString(),
    Integer.parseInt(age.getText().toString())
});
```

删除

```java
//1.定义删除sql
String sql = "delete from user where id = ?";
//2.使用execSQL执行执行sql
database.execSQL(deleteSql, new Object[]{
    number.getText().toString()
});
```

修改

```java
//1. 定义修改sql
String sql = "update user set name = ? where id = ?";
//2. 使用execSQL执行执行sql
database.execSQL(updateSql, new Object[]{
    name.getText().toString(),
    number.getText().toString()
});
```

查询

```java
//1.定义查询sql
String sql = "select * from user where id = ?";
//2.使用rawQuery进行查询
Cursor cursor = database.rawQuery(selectSql, new String[]{findNumber.getText().toString()});
//3. moveToNext判断游标是否还能移动到下一个
while (cursor.moveToNext()){
    Integer userId = cursor.getInt(cursor.getColumnIndex("id"));
    String name = cursor.getString(cursor.getColumnIndex("name"));
    Integer age = cursor.getInt(cursor.getColumnIndex("age"));
    show.setText("编号：" + userId + "\t 名称：" + name + "\t年龄" + age);
}
```

### 版本管理

在数据库的版本发生变化时会被调用，一般在软件升级时才需改变版本号，而数据库的版本是由程序员控制的，假设数据库现在的 版本是1，由于业务的变更，修改了数据库表结构，这时候就需要升级软件，升级软件时希望 更新用户手机里的数据库表结构，为了实现这一目的，可以把原来的数据库版本设置为2 或者其他与旧版本号不同的数字即可！