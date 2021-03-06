# BackgroudLibrary
A framework for directly generating shape through Tags, no need to write shape.xml again（通过标签直接生成shape，无需再写shape.xml）


![](https://user-gold-cdn.xitu.io/2018/9/11/165c43b71b691bde?w=1258&h=346&f=png&s=64351)
# 前言
作为一个android程序员，对于shape、selector这两个标签一定不陌生。每当UI设计师给我们设计出一个个button背景的时候，我们就需要去drawable文件夹下去新建一个bg_xxx.xml，然后很多时候区别仅仅是一个边框的颜色或者填充的颜色。这就导致了很多非常相似的.xml文件产生。  
网上之前也有了一种通过自定义View，在xml中通过设置属性达到shape效果的控件。但是这种自定义的控件不太灵活，归根到底是一个自定义的button，如果我想改造项目的话就得去替换原有的button或者textView。接下来就给大家提供一种更加简单的方式：  
**无需自定义View，直接添加属性便可以实现shape、selector效果**。

# 具体内容
## 效果展示
话不多说，直接上代码。  
使用方法：  
1、在BaseActiviy的super.onCreate()之前调用一行代码，仅仅是一个方法

![](https://user-gold-cdn.xitu.io/2018/9/10/165c3fb848a781e0?w=1482&h=348&f=jpeg&s=81921)
2、没有其他操作了，直接layout里开始写控件吧！

### 布局代码
我们来添加一些实例属性：

![](https://user-gold-cdn.xitu.io/2018/9/10/165c3fe8b4fb7844?w=1836&h=1078&f=png&s=325855)
有没有觉得很熟悉，就是**原生标签的tag名+_+属性名**，很容易记住，而且不管是Button还是TextView，**只要是View**都可以。
### 效果
我们来看一下实际效果：

![](https://user-gold-cdn.xitu.io/2018/9/10/165c400590e01e22?w=296&h=629&f=gif&s=177296)

### 修改背景
现在UI设计师告诉我们要改一下背景，没事，我们只需要在xml添加或者修改属性就行。  
我们来把圆形改成正方形，加个边框。5秒ok！

![](https://user-gold-cdn.xitu.io/2018/9/10/165c402c9dd65991?w=1267&h=688&f=gif&s=1748267)

## 简单的原理解析
### app:xxx
app:xxx属性就不用多说了，这些就是一些自定义属性而已。在这里我把shape、selector的部分属性转换成自定义的属性，这样就方便添加到已有原生控件中。
### BackgroundLibrary.inject(this)
这个方法是这个框架唯一需要加入的代码。  
inject中实际上是给LayoutInflater添加了一个LayoutInflater.Factory类。而Android的Activity在创建过程（也就是setContentView）中实际上是通过把xml转换成View的对象。而LayoutInflater.Factory相当于这中间的一个后门，它是xml解析创建成View的必经方法，google中的v7support包里很多内容就是通过LayoutInflater.Factory来实现向下兼容的。  
在这里，我通过低入侵的方式，加入一个自定义的LayoutInflater.Factory，去解析添加的自定义属性，接下来就简单了。生成系统提供的GradientDrawable、RippleDrawable、StateListDrawable即可。  
同时由于AppcompatActivity是已经实现了LayoutInflater.Factory，而Activity又设定一个Activity只能加入一个factory类，因此这里需要在super.onCreate之前调用该方法，利用AppcompatActivity的factory去创建View。  
具体原理解释可以参考我的这篇文章：[Android 常用换肤方式以及原理分析](https://juejin.im/post/5b8f6dcde51d450e6a2dcadf)

## 具体使用方法：
添加依赖：

    implementation 'com.noober.backgorund:core:1.0.1'

BaseActivity的super.onCreate之前添加代码：

    BackgroundLibrary.inject(context);
支持属性，命名规则就是**标签名_标签属性名**,支持shape所有属性：

       <attr name="shape" format="enum">
           <enum name="rectangle" value="0" />
           <enum name="oval" value="1" />
           <enum name="line" value="2" />
           <enum name="ring" value="3" />
       </attr>

       <attr name="solid_color" format="color"/>

       <attr name="corners_radius" format="dimension"/>
       <attr name="corners_bottomLeftRadius" format="dimension"/>
       <attr name="corners_bottomRightRadius" format="dimension"/>
       <attr name="corners_topLeftRadius" format="dimension"/>
       <attr name="corners_topRightRadius" format="dimension"/>

       <attr name="gradient_angle" format="integer"/>
       <attr name="gradient_centerX" format="float"/>
       <attr name="gradient_centerY" format="float"/>
       <attr name="gradient_centerColor" format="color"/>
       <attr name="gradient_endColor" format="color"/>
       <attr name="gradient_startColor" format="color"/>
       <attr name="gradient_gradientRadius" format="dimension"/>
       <attr name="gradient_type" format="enum">
           <enum name="linear" value="0" />
           <enum name="radial" value="1" />
           <enum name="sweep" value="2" />
       </attr>
       <attr name="gradient_useLevel" format="boolean"/>

       <attr name="padding_left" format="dimension"/>
       <attr name="padding_top" format="dimension"/>
       <attr name="padding_right" format="dimension"/>
       <attr name="padding_bottom" format="dimension"/>

       <attr name="size_width" format="dimension">
           <enum name="wrap_content" value="-2" />
           <enum name="match_parent" value="-1" />
       </attr>
       <attr name="size_height" format="dimension">
           <enum name="wrap_content" value="-2" />
           <enum name="match_parent" value="-1" />
       </attr>

       <attr name="stroke_width" format="dimension"/>
       <attr name="stroke_color" format="color"/>
       <attr name="stroke_dashWidth" format="dimension"/>
       <attr name="stroke_dashGap" format="dimension"/>

       <!--以下是selector事件-->
       <attr name="ripple_enable" format="boolean"/>
       <attr name="ripple_color" format="color"/>
       <attr name="unpressed_color" format="color"/>
       <attr name="pressed_color" format="color"/>
    
例如加一个边框背景，如下即可：

    <TextView
        android:layout_width="130dp"
        android:layout_height="36dp"
        android:gravity="center"
        android:text="TextView"
        android:textColor="#8c6822"
        android:textSize="20sp"
        app:corners_radius="4dp"
        app:solid_color="#E3B666"
        app:stroke_color="#8c6822"
        app:stroke_width="2dp" />
        
如果需要selector效果，需要同时添加
    
    app:unpressed_color
    app:pressed_color

如果需要水波纹效果，5.0以上才支持：
    
    app:ripple_enable="true"//打开水波纹开关
    app:solid_color="xxx"//设置默认填充颜色
    app:ripple_color="xxx"//设置水波纹颜色

注意：  
如果直接给原生控件添加属性，在xml中会如下报红色异常，这时候不用理会即可。

![](https://user-gold-cdn.xitu.io/2018/9/11/165c43f215081b96?w=764&h=604&f=png&s=147211)
但是红色多了，显示总归难受，可以在根节点添加 

    tools:ignore="MissingPrefix" 
即可

![](https://user-gold-cdn.xitu.io/2018/9/11/165c44039942ecc4?w=848&h=468&f=png&s=134125)
# 结尾
项目地址：https://github.com/JavaNoober/BackgroudLibrary  
后续更新完善内容会在项目里标明，欢迎star。
