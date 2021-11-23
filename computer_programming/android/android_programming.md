*Android Programming (The Big Nerd Ranch Guide, Fourth Edition)*
=================================================================

- [source code](https://bignerdranch.com/solutions/AndroidProgramming4e.zip)
- [bignerdranch android-programming](https://forums.bignerdranch.com/c/android-programming-4th-edition/)
- [android studio 3.3.1](https://developer.android.com/studio/archive)

## 1 Android开发初体验

### 1.2 创建Android项目

- 关闭Instant Run
    - 无需生成新APK就生效
    - Configure - Settings - Build - Instant Run
- Jetpack
    - 拆分的支持库解决兼容性问题

### 1.4 用户界面设计

- AaaBbbActivity -> activity_aaa_bbb.xml
- view & widget
    - view: 用户界面的构造模块(ConstraintLayout...)
    - widget: 用户能看到并与之交互的视图(TextView...)
    - widget是View或View子类的实例
    - ViewGroup: 是View,包含并布置其他View,又称layout

#### 1.4.1 视图层级结构

- 根元素需要指定Android XML资源文件命名空间属性: xmlns(xml name space)
- ViewGroup与包含在其中的View构成父子关系
- 根元素也有父视图

#### 1.4.2 部件属性

- android:layout_width(height) -> match_parent/wrap_content
- android:padding
- android:orientation -> 子部件垂直还是水平放置
- android:text -> @string/xxx
- android:gravity -> sets the gravity of the contents (i.e. its subviews) of the View it's used on

#### 1.4.3 创建字符串资源

- res/values/strings.xml

### 1.5 从布局XML到视图对象

- R.layout.activity_main
    - ./app/build/generated/not_namespaced_r_class_sources/debug/processDebugResources/r/com/example/ngeoquiz/R.java
- 创建资源id
    - android:id="@+id/true_button"

### 1.6 部件的实际应用

- private lateinit var trueButton: Button
    - setContentView后View才会实例化到内存
    - lateinit: 使用属性时我们会提供not null对象

### 1.9 深入学习: Android编译过程

- aapt2: Android Asset Packaging Tool
- 手动编译
    - ./gradlew tasks

## 2 Android与MVC设计模式

### 2.3 更新视图层

- xmlns:tools="http://schemas.android.com/tools"
    - tools:text
        - 预览时tools属性覆盖部件属性,设备上运行时忽略tools属性

```
// 函数定义
private fun functionName(variableName: VariableType) {
    // TODO
}
```

### 2.5 添加图标资源

- app/src/main/res/drawable-...

### 2.6 屏幕像素密度

- px,pt,mm,in
- dp: density-independent pixel
- sp: scale-independent pixel

## 3 activity的生命周期

### 3.1 旋转GeoQuiz应用

- 旋转GeoQuiz应用后应用再次显示第一题

### 3.2 activity状态与生命周期回调

```
Nonexistent
onCreate onDestroy
对象实例在内存
Stopped
onStart onStop
视图部分或全部可见
Paused
onResume onPause
用户与当前activity交互
Resumed
```

- 避免手动调用activity生命周期函数

### 3.4 activity生命周期如何响应用户操作

- 设备旋转会改变设备配置
    - 可能会有更合适的资源来匹配新的配置
    - 系统会调用 onDestroy + onCreate
- 设备处于水平方向
    - res/layout-land
- FrameLayout不负责子视图的位置
    - FrameLayout子视图位置取决于andorid:layout_gravity
    - FrameLayout的直接子视图需要设置andorid:layout_gravity

#### 3.6 深入学习: UI刷新与多窗口模式

- onStart -> onStop的可见生命周期都应该刷新UI
- multi-resume
    - 完全可见的activity都将处于运行状态
    - AndroidManifest.xml
        - <meta-data android:name="andorid.allow_multiple_resumed_activities" android:value="true" />

## 4 UI状态的保存与恢复

### 4.1 引入ViewModel依赖

- app/build.gradle
    - File - Sync Project with Gradle Files

### 4.2 添加ViewModel

- onDestroy -> activity.isFinishing: true(用户结束)/false(正在被系统销毁), true时ViewModel才会被销毁

- ViewModel
    - init: 创建
    - onCleared: 销毁前

### 4.3 进程销毁时保存数据

- 操作系统销毁应用进程时,会直接清除整个应用进程,并且不会调用生命周期的回调函数
- saved instance state
    - Acitivity.onSaveInstanceState(Bundle)
    - 用于恢复被系统回收的未结束activity
    - *未结束使用*的activity进入停止状态时调用(onStop调用完成后,onDestroy调用完成前)
    - 手动点击back也不会触发
    - 手动调用finish不会触发
    - Developer options - Apps - Dont't keey activities
    - onSaveInstanceState保存暂存数据,onStop保存永久性数据

### 4.4 ViewModel与保存实例状态

- ViewModel
    - 擅长处理耗时操作
- onSaveInstanceState
    - 进程消亡
    - 需要序列化磁盘,需要避免大对象
    - lifecycle-viewmodel-savedstate: 进程消亡也能保存数据

### 4.5 深入学习: Jetpack、AndroidX与架构组件

- Jetpack库(Google官网出品的一套开发库)都在androidx开头的包里

## 5 Android应用的调试

- Log.d(TAG, "nickmyb", Exception())

### 5.2 Android特有的调试工具

- Android Lint
    - Analyze -> Inspect Code
- 重新编译整个项目
    - Build - Clean Project
- Tools - Layout Inspector
- View - Tool Windows - Profiler