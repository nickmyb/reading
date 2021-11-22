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
