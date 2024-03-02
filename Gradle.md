# Gradle

------

## 什么是Gradle？



## Gradle的分类

目前 Gradle 分为两种类型：

* Gradle Groovy
* Gradle Kotlin



## Gradle文件解析

### gradlew

`gradlew` 它是一个二进制文件、可执行文件，其中`w` 代表的是 `wrapper` 包装器。它是一种脚本，以确保它安装了我们指定版本的 `gradle`，然后运行特定任务，因此该文件的所有事件都被视为==任务==。

例如：在 `Terminal` 执行以下命令，它会执行构建任务。

```groovy
./gradlew build
```

又或者列出使用 `gradle` 执行的所有任务

```groovy
./gradlew tasks
```





