---
title:  Android开发笔记——快速入门（布局与RecycleView组件）
categories:
  - Android
tags:
  - Android
  - RecycleView
excerpt: "布局与RecycleView组件"
date: 2024/10/22 00:00:00
---



# Android开发笔记——快速入门（布局与RecycleView组件）



<img src="https://s2.loli.net/2024/10/04/ESkpsrWfO9q6PNz.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727982825836" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

## 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]

请先参考前一篇文章复习一下Kotlin的一些语法。

**大部分内容参考了郭霖先生的《第一行代码》，在书的基础上针对目前的实际情况进行实践记录。**

## 线性布局

创建一个ACT，并将xml布局文件修改如下：

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
android:orientation="vertical"
android:layout_width="match_parent"
android:layout_height="match_parent">

    <Button
        android:id="@+id/button1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/button_1" />

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button2" />

    <Button
        android:id="@+id/button4"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button3" />

</LinearLayout>
```

我们在里面添加了三个button，每个的长宽都是：

```xml
android:layout_width="wrap_content"
android:layout_height="wrap_content"
```

并指定了排列方向是：

```xml
android:orientation="vertical"
```

实现效果如下：

![2](https://s2.loli.net/2024/10/04/mo3LsKahclgFu9V.png)

## 约束布局

此部分参考了：

作者：四会歌神陈子豪链接：https://www.jianshu.com/p/17ec9bd6ca8a

**约束布局ConstraintLayout** 是一个ViewGroup，可以在Api9以上的Android系统使用它，它的出现主要是为了解决布局嵌套过多的问题，以灵活的方式定位和调整小部件。从 **Android Studio 2.3** 起，官方的模板默认使用 **ConstraintLayout**。

RelativeLayout是相对布局，它可以根据各个子组件的相对位置来实现布局，打个比方A、B两个组件，想要使用RelativeLayout来针对B布局，你可以指定B组件在A的右侧，这样就根据相对布局完成了一个组件布局。ConstraintLayout同样也能实现这样的功能，ConstraintLayout使用起来比RelativeLayout更灵活，性能更出色！还有一点就是ConstraintLayout可以按照比例约束控件位置和尺寸，能够更好地适配屏幕大小不同的机型。

## ConstraintLayout示例

### 相对定位

可以看到我们创建了一个布局，其父组件的宽度大小是固定的，长度随文本大小变化而变化：

其中包含了两个组件一个是imageview 还有一个 Textview。

![7](https://s2.loli.net/2024/10/04/IsnK3X5qQcTmYiS.png)



我们要通过相对定位来实现二者中心位置对齐：

首先文本框一定是在图片下头的，所以就要把这个属性设置为这样：

```xml
app:layout_constraintTop_toBottomOf="@+id/imageView"
```

constraintTop_toBottomOf这个属性很好理解，第一个constraintTop指的就是这个组建的头位置，toBottomOf就把这个头位置设定到谁的底部。

![8](https://s2.loli.net/2024/10/04/3a7SBKrAVFnh59q.png)

补一张图方便理解，具体还有什么就不再赘述了，详细了解请参考链接，链接写的非常好！。

![9](https://s2.loli.net/2024/10/04/CJatVOdw1jzKXgu.png)

需要注意的是，ConstraintLayout设计出来压根就不是来代替经典如LinearLayout、FrameLayout、GridLayout 等布局，你可以把他看作是RelativeLayout的超级加强版，所以切记不要在典型的线性布局需求中使用ConstraintLayout。经典线性布局的需求意味着子控件之间不需要存在任意依赖关系，任何一个控件拿掉了对整体布局没有任何影响。而你拿着ConstraintLayout对每一个子控件进行依赖，说好比国家拎着一个女人强行让你配对一样。

所以接下来使用组件的时候这两个配合着来用。

## RecycleView组件

RecycleView同样也是ListView的增强版，他的设计和使用更加符合逻辑，不再需要更多的优化操作，所以建议抛弃掉ListView，使用RecycleView。

### 添加依赖

```groovy
implementation 'androidx.recyclerview:recyclerview:1.2.1'
```

![a-2](https://s2.loli.net/2024/10/04/kYb1hIGxqfOMup4.png)

### 在布局中添加RecycleView组件

由于不是系统内置的组件，需要把完整的包名写下来。

```xml
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recycleView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        />
```

### 让RecycleView组件显示内容

我们需要创建一个布局文件，来规定RecycleView的里面的内容，这里就直接采用上边演示的布局文件，内容很简单，就是一个图片加下边一段文字，接下来我们研究如何将这个布局作为RecycleView的子组件导入到RecycleView之中。

我们首先需要创建一个Adapter类来将数据先转化为**子布局的内容**再将**子布局**导入到RecycleView之中。标准写法如下：

```kotlin
class FruitAdapter_Recycle(val  fruitList :List<Fruit>):
    RecyclerView.Adapter<FruitAdapter_Recycle.ViewHolder>()
{
    inner class ViewHolder (view:View):RecyclerView.ViewHolder(view)
    {
        val fruitImage : ImageView = view.findViewById(R.id.imageView)
        val fruitName : TextView = view.findViewById(R.id.textView)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val view = LayoutInflater.from(parent.context)
            .inflate(R.layout.fruit_item,parent,false)
        return ViewHolder(view)
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val fruit = fruitList[position]
        holder.fruitImage.setImageResource(fruit.imageId)
        holder.fruitName.text=fruit.name
    }
    override fun getItemCount(): Int = fruitList.size
}
```

继承了RecyclerView包中的Adapter类，可以看到将泛型指定为FruitAdapter_Recycle.ViewHolder，说明其本就是基于ViewHolder的。这为我们优化省去了不少事情。

```kotlin
inner class ViewHolder (view:View):RecyclerView.ViewHolder(view)
{
    val fruitImage : ImageView = view.findViewById(R.id.imageView)
    val fruitName : TextView = view.findViewById(R.id.textView)
}
```

内部类ViewHolder使用最RecyclerView的最外层View来作为构造方法的参数，通过导入布局的View会把对应的子项的内容创建好，方便构造对应的ViewHolder。

同时这个类还内置好了其他的方法，我们只需将对应方法重写完成就可以正常使用了。

onCreateViewHolder方法用于创建对应的ViewHolder实例，我们在这个方法中将对应的子布局（fruit_itm）加载进来把构造好的子布局传入到ViewHolder的构造函数当中作为ViewHolder实例返回。

```kotlin
 override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder 
{
    val view = LayoutInflater.from(parent.context)
    .inflate(R.layout.fruit_item,parent,false)
    return ViewHolder(view)
 }
```

onBindViewHolder方法用于对RecycleView中每一个子项进行赋值，在每一个子项进入到屏幕的时候此方法会执行，将对应的数据缓存进来，指定数据的方法很简单通过Position来获取当前的位置然后将数据缓存进去。

```kotlin
  override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        val fruit = fruitList[position]
        holder.fruitImage.setImageResource(fruit.imageId)
        holder.fruitName.text=fruit.name
    }
```

最后getItemCount方法返回Recycle有多少子项。

```kotlin
override fun getItemCount(): Int = fruitList.size
```

使用Adapter_Recycle载入数据：

载入数据的方法很简单，FruitAdapter_Recycle类的构造函数只需要传入一个List即可，Lsit的数据包含textview要显示的文本和imagebiew现实的图片资源地址：

```kotlin
val fruitList = mutableListOf<Fruit>()
fruitList.add(Fruit("Apple", R.drawable.apple_pic))
```

每次一个子项滚入到屏幕的时候就会通过onBindViewHolder将数据加载到viewholder里面，然后再显示。

#### RecycleView的布局与显示

我们在完成数据加载以后，可以意识到一个问就是，没有指定子类的布局，在子项之间应该怎么排序？

RecycleView之中专门有一个成员来管理子类的布局，我们在这就使用线性布局来做演示：

首先创建一个布局管理类：LinearLayoutManager是系统内置的类，可以让需要的组件实现线性布局。

```kotlin
val layoutManager =LinearLayoutManager(this)
```

当然实现也要实现一点不一样的，Listview是无法实现横向滚动的，咱们就来实现一下，实现方法也很简单，只要把线性布局的方向改为水平即可：

```kotlin
layoutManager.orientation=LinearLayoutManager.HORIZONTAL
```

最后我们把这个layoutManager传递给recycleview：

```kotlin
binding.recycleView.layoutManager =layoutManager
```

然后将数据导入：

```kotlin
val adapter = FruitAdapter_Recycle(fruitViewModel.fruitList)
binding.recycleView.adapter=adapter
```

实现效果：

其中名字较长的是为展现一下约束布局的具体效果而加长的。

![img](https://s2.loli.net/2024/10/04/bwIV3E7CQAeftDU.jpg)



















