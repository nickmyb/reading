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

## 6 第二个activity

### 6.2 启动activity

- Activity -> startActivity -> ActivityManager -> 创建Activity Instance & onCreate
- Intent
    - component(activity， service, braodcast receiver, content provider)与操作系统通信的媒介工具
- companion object
    - static method

### 6.3 activity间的数据传递

- startActivityForResult
- setResult
    - 没有调用setResult,按back会收到RESULT_CANCELED
- back
    - onActivityResult(requestCode, resultCode, intent)
- startActivity启动新activity后ViewModel会调用onCleared被销毁,isFinishing状态也是false

## 7 Android SDK 版本与兼容

- Build.VERSION.SDK_INT >= Build.VERSION_CODES.M
- jetpack向后兼容
- onStart -> onActivityResult -> onResume

## 8 UI fragment 与 fragment管理器

- onCreate / onCreateView
- 监听器可以再用户交互,设备选择,视图恢复导致数据重置时触发
- 视图状态在onCreateView后onStart前恢复
    - 避免beforeTextChanged和afterTextChanged在视图状态改变时被调用
    - TextWatcher在onStart中注册
- fragment自己无法在屏幕上显示视图
- 托管UI fragment
    - activity布局中为fragment安排位置
    - 管理fragment实例生命周期
- Activity -> FragmentManager -> FragmentTransaction & Fragment
- 设备旋转或回收内存时,fragment队列会被保持
- 生命周期
    - activity的生命周期由操作系统调用
    - fragment的生命周期由于托管activity的FragmentManager调用
    - 操作系统并不知道fragment
- fragment会尽量与activity保持一致

## 9 使用RecyclerView显示列表

- ViewModel生命周期与fragment
    - fragment视图还在屏幕,ViewModel一直处于活动,即时发生旋转
    - 当前fragment被销毁或替换,关联ViewModel会被销毁
    - fragment事务添加到回退栈,即使fragment被替换,关联ViewModel也不会被销毁

- RecyclerView需要LayoutManager来安排位置
- RecycleView仅限于回收和摆放View
- 列表项View显示数据需要配合ViewHolder和Adapter
- ViewHolder.itemView
- Adapter
    - 创建ViewHolder
    - 绑定ViewHolder到模型层数据
- RecyclerView
    - 请Adapter创建ViewHolder
    - 请Adapter绑定ViewHolder到模型层数据
- onCreateView中关联RecyclerView和adapter
- onCreateViewHolder调用会比onBindViewHolder少的多
- adapter和viewholder尽量分离

## 10 使用布局与部件创建用户界面

- 嵌套布局难以维护并且影响性能,减少嵌套能缩短布局绘制时间
- ConstraintLayout: 添加约束,将widget向指定方向拉
- theme & style
- blueprint - guideline

## 11 数据库与Room库

### 11.2 创建数据库

- 定义实体 -> 表结构
    - @Entity & @PrimaryKey
- 创建数据库类
    - 实体类需要和数据库关联
    - @Database
- 创建类型转换器
    - TypeConverter
- 定义数据库访问对象
    - @Dao
    - @Query
- Application.onCreate
    - 应用程序实例在应用启动后创建,应用进程被销毁时销毁
- View - Tool Windows - Device File Explorer - chmod 777
- 主线程(UI线程)
    - 所有耗时操作都应该在后台线程完成
    - UI只能在主线程更新
- LiveData
    - 不同模块之间传递数据
- LifecycleOwner
- fragment & fragment视图的生命周期
- Application,单例活的久,但不适合做永久存储,存储共享数据

## 12 Fragment Navigation

### 12.1 单Activity多Fragment

#### 12.1.1 Fragment回调接口

- Fragment中定义Callbacks接口
    - onAttach
    - onDetach
- 托管Activity实现Callbacks
- addToBackStack(就是activity的堆栈)
- fragment argument传递数据
    - Fragment.setArguments(Bundle)
    - 创建fragment后添加给activity前
