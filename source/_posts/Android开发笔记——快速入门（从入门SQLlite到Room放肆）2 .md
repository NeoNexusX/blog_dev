---
title:  Android开发笔记——快速入门（从入门SQLlite到Room放肆）2
categories:
  - Android
tags:
  - Android
  - SQLlite
  - Room
excerpt: "从入门SQLlite到Room放肆 2 SharePreferences "
date: 2024/10/22 00:00:00
---

# Android开发笔记——快速入门（从入门SQLlite到Room放肆）2

<img src="https://s2.loli.net/2024/10/04/WtmHlaEYCXrT5ev.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727981759445" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

## 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]

请先参考前一篇文章复习一下Kotlin的一些语法。

**大部分内容参考了郭霖先生的《第一行代码》，在书的基础上针对目前的实际情况进行实践记录。**

## Android的SharePreferences

**此部分配套代码名称:DataTest2**

将数据存储在SharePreferences中是一个针对多种数据类型的办法，使用文件存储很不方便，不方便的点在于很难对于具有数据类型的结构化数据进行存储，一个double存储进去就变成了文本，这样很不方便，所以我们来介绍如何使用具有数据类型支持的存储方案。

### 将数据存储在SharePreferences当中

将数据存储到SharePreferences当中就需要先获取到SharePreferences对象，获取的办法有两种：

#### 1、通过`context`类的`getSharedPreference`来获取对象

```kotlin
@Override
public SharedPreferences getSharedPreferences(String name, int mode) {
    return mBase.getSharedPreferences(name, mode);
}
```

此方法接收两个参数，第一个参数用于指定SharePreferences文件的名称，如果指定的文件不存在则会创建一个，一般存储在：包路径下的/share_prefs/目录下，第二个参数用于指定操作模式，目前只有默认的**MODE_PRIVATE**这一种模式可以选，他表达的意思是只有当前的应用程序才可以针对他进行读写，其他程序一律不可以。

#### 2、通过`Activity`类的`getPreferences`来获取

```kotlin
public SharedPreferences getPreferences(@Context.PreferencesMode int mode) {
    return getSharedPreferences(getLocalClassName(), mode);
}
```

此方法只需要接受一个模式参数即可获取到对象，与上文方法不同的是，这个方法创建的文件名与调用它的Actity类名字一模一样。可以看到此方法实际上是对上文方法的再封装。

### 向SharePreferences存储数据

存储的过程分为三步：

- 通过SharePreferences实例对象的edit()方法,可以获取到一个`SharePreferences.Editor`对象。
- 向`SharePreferences.Editor`添加数据，针对每一种类型都有其对应的方法可以添加数据，比如添加一个字符串就可以用`putString()`方法。
- 调用apply方法将数据提交，从而完成数据存储操作。

下面举个例子：

```kotlin
fun load_p()
{
    val editor = getSharedPreferences("data",Context.MODE_PRIVATE).edit()
    editor.putString("data","Jszszzy_Preference")
    editor.apply()
}
```

这里使用了context的getSharedPreference来获取editor对象，然后调用putSrtring来存入一个字符串，他的key值是data当你要获取的时候就要调用”data“了，最后使用apply方法将数据存储进去。

怎么用验证我们存储的数据呢？

我可以直接到对应目录下去查看，当然你也可以使用读取再把数据读取出来。

#### 从SharePreferences读取数据

你已经体验到了，我们只需要三行代码就可以将数据存储起来，并且不需要考虑太多的性能细节问题。有一个更好的消息是，读取其实更简单。

读取只有一个步骤：

通过SharePreferences实例对象的get方法分别传入键值和数据类型，就可以得到对应的数据，这里我们使用一个Textview来获取对应的数据，然后在程序启动的时候，再将读取到的对应的数据再显示出来。

onCreate读取数据代码如下：

需要注意的是这里是在前文已经实现了存储的基础上，程序已经创建好了对应的文件，然后实现的读取。

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    binding = ActivityMainBinding.inflate(layoutInflater)
    setContentView(binding.root)

    load_p()?.let {
        binding.datasave.setText(it)
    }

    Toast.makeText(this,"Reload data succeed",Toast.LENGTH_SHORT).show()
}
```

load的代码如下：

```kotlin
fun load_p():String? {
    val loader = getSharedPreferences("data",Context.MODE_PRIVATE)
    return loader.getString("data","Cant get data")
}
```

存储的代码也很简单，我们只需要在Activity完全消失的时候，调用存储把数据存储起来，在Activity的回调函数中来保存数据：

```kotlin
override fun onStop() {
    save_p()
    super.onStop()
}
```

因为要保存显示的数据，所以保存的函数也要修改：

```kotlin
fun save_p()
{
    val editor = getSharedPreferences("data",Context.MODE_PRIVATE).edit()
    editor.putString("data",binding.datasave.text.toString())
    editor.apply()
}
```

效果如下，开启应用程序读取到数据如下：

![1-1](https://s3.bmp.ovh/imgs/2022/06/16/5334439cc5794979.png)

修改后保存，读取到的数据如下：

![1-2](https://s3.bmp.ovh/imgs/2022/06/16/f59c772af7876008.png)

### 使用高级函数来优化

尽管使用SharePreferences已经足够简单了，但实际上还是有一些重复的书写，我们这里就使用高阶函数来优化一下。

直接举一个例子：

```kotlin
fun SharedPreferences.open(block:SharedPreferences.Editor.()->Unit){
    val editor = edit()
    editor.block()//因为这个扩展函数实际上提供的是SharedPreferences上下文，二传入的参数提供的是SharedPreferences.Editor的上下文，需要加入前缀
    editor.apply()
}
```

以上一段代码看起来可能有点复杂，实际上没有那么复杂，我们实际上是通过扩展函数为SharedPreferences类添加了一个open函数，这个open函数接受一个以SharedPreferences.Editor为上下文的函数参数他的名为block。

我们为什么要创建这样一个函数呢？

实际上有两个目的，一个是省略重复的获取对象和提交数据的时候调用apply函数，二是将函数作为参数可以自定义要实现的数据存储操作，并且能提供对应的上下文。

如何使用这个函数呢？

我们可以直接调用getSharedPreferences方法获取到这个对象，然后调用open函数传入需要进行的操作即可：

完整的代码如下

```kotlin
getSharedPreferences("data",Context.MODE_PRIVATE).open {
    putString("data",binding.datasave.text.toString())
}
```

和上文实现相同的操作相比：

```kotlin
val editor = getSharedPreferences("data",Context.MODE_PRIVATE).edit()
editor.putString("data",binding.datasave.text.toString())
editor.apply()
```

省略了不少，尤其当需要添加的数据增多的时候比较方便。

当然为了避免因为kotlin的匿名类产生的开销，我们使用inline关键字来增强一下避免因为匿名类产生的开销。

## SQLite数据库入门

**此部分配套代码名称:DataTest3**

前边两种方式存储的实际上都是一些简单的数据，当我们的数据关系复杂起来的时候，通过前两种办法就变的捉襟见肘了。

SQLite是一个轻量级的关系数据库，还遵循了数据库的ACID事务，所以只要你以前使用过其他关系型数据库，就可以很快的上手SQLite，同时他不仅在大小上有很大的优化，使用起来也是更加的方便和快捷。

### 创建数据库管理对象

Android为了方便管数据库，专门提供了一个抽象类来供你使用，创建数据库的时候只需要单独写一个类然后继承对应的`SQLiteOpenHelper`类即可，其中包含了两个抽象方法需要你自己去实现，一个是创建的时候调用的`override fun onCreate(db: SQLiteDatabase?)` 他传递进来一个参数指明当前调用的具体哪个数据库，如果还没有数据库的表就可以通过SQLiteDatabase的方法执行SQL语句来创建表。

同时还需要重写的方法还有onUpgrade，他是当数据库需要升级的时候来调用的，具体等到数据库升级再详细讨论。

一般使用SQLiteOpenHelper的时候都会使用它较少参数的构造方法，这个构造方法如下：

```kotlin
public SQLiteOpenHelper(@Nullable Context context, @Nullable String name,
        @Nullable CursorFactory factory, int version) {
    this(context, name, factory, version, null);
}
```

第一个参数context用来定位数据库的存储位置，包名等等....，没什么好说的，很多函数都需要提供上下文参数，来实现一些信息获取或者Context的内置方法获取。

第二个参数是数据库的名称。

第三个参数是允许我们在查询数据的时候返回一个自定义的Cursor，至于这是什么我们晚点再来讨论。

第四个参数是显示当前数据的版本号，可用于对数据库进行升级操作。

#### 创建数据库

需要注意的是上边只是创建了一个数据库助手对象，并没有将数据库本身创建出来，当我们第一次去调用getWritableDatabase方法的时候才是真正的将数据库创建了出来，我们可以通过阅读源码来知道这一关键点。

```kotlin
Create and/or open a database that will be used for reading and writing. The first time this is called, the database will be opened and onCreate, onUpgrade and/or onOpen will be called.
Once opened successfully, the database is cached, so you can call this method every time you need to write to the database. (Make sure to call close when you no longer need the database.) Errors such as bad permissions or a full disk may cause this method to fail, but future attempts may succeed if the problem is fixed.
Database upgrade may take a long time, you should not call this method from the application main thread, including from ContentProvider.onCreate().

Returns:
a read/write database object valid until close is called
Throws:
SQLiteException – if the database cannot be opened for writing

public SQLiteDatabase getWritableDatabase() {
      synchronized (this) {
      	 return getDatabaseLocked(true);
   }
}
```

源码的注释说的很清楚，我只阐述以下几个要点，让你对这个方法有更深的了解。

当第一次这个方法被调用的时候，会触发以下生命周期函数：

-  onCreate
-  onUpgrade and onOpen

这个方法的实现不仅包含了在第一次调用的时候创建数据库，同时也包含了在不同生命周期去调用onCreate、onDowngrade（非抽象）、onUpgrade方法、onOpen方法（非抽象），这也是为什么只有第一次调用这个方法的时候，数据库内的表和数据才会被创建。

数据库的升级过程是个很慢的过程，所以你不应该在某个组件Act或者Fragment的onCreate方法去调用getWritableDatabase或者getReadableDatabase。

接下来我们直观的体验一下如何创建一个数据库。

这里我们要创建一个BookStore.db数据库并且在数据库中建立一张表，上文说了，只有数据库第一次

被创建的时候会调用onCreate方法，所以我们初始化数据应该放在onCreate里面。

创建一个自己的SQLiteOpenHelper类，并重写初始化方法：

```kotlin
class MyDatabaseHelper(val context:Context,name:String,version: Int)
    :SQLiteOpenHelper(context,name,null,version) {
        
    private val createBook = "create table Book ("+
            "id integer primary key autoincrement,"+
            "price real,"+
            "pages integer,"+
            "name text)"
        
    override fun onCreate(db: SQLiteDatabase?) {
        db?.execSQL(createBook)
        Toast.makeText(context,"Create succeed",Toast.LENGTH_SHORT).show()
    }

    override fun onUpgrade(db: SQLiteDatabase?, oldVersion: Int, newVersion: Int) {

    }


}
```

在类里面我们声明了一个成员字符串变量，在字符串中写下SQL的语句，SQLite所包含的数据类并不多，具体如下：

- integer 整型
- real 浮点类型
- text 文本类型
- blob 二进制类型

其中语句中的关键字primary key 、autoincrement分别代表，设置为主键、内容自动增长。

最后调用onCreate方法中的数据库对象，执行SQL语句，完成建表。

那么如何查看建立好的数据库的内容呢？我们可以借助数据库查看器，[下载查看器](https://sqlitebrowser.org/blog/version-3-12-2-released/)。

从手机中把数据库的内容保存到本地来。

![1-5](https://s3.bmp.ovh/imgs/2022/06/16/ca17b99867404014.png)

数据库存储位置如下：

![1-6](https://s3.bmp.ovh/imgs/2022/06/16/db8170e5bac63ff8.png)

在程序的包中：

![1-7](https://s3.bmp.ovh/imgs/2022/06/16/39054830e573804d.png)

右键另存为，将数据库保存到PC本地，你可以使用插件打开：

![1-8](https://s3.bmp.ovh/imgs/2022/06/16/509bbcd04cb47b58.png)

效果如下：

![1-9](https://s3.bmp.ovh/imgs/2022/06/16/4d0213ff21d660ed.png)

或者使用刚下载的浏览器打开:

![1-10](https://s3.bmp.ovh/imgs/2022/06/16/07fdb3dd03f477c6.png)

### 升级数据库

在SQLiteOpenHelper中实际上还包含着一个onUpdate方法，他就是刚才说的针对数据库升级的方法。

目前的数据库应将创建完成了，但是我们想增加一个Category表用于存放书的各种分类信息怎么办呢？

建立表的语句很简单：

```sql
create table Category(id interger primary autoincrement , category_name text , category_code integer)
```

接下来我们将这条建表语句添加到MyDatabaseHelper中，代码如下所示：

```kotlin
private var tablename:String=""

private val front_test= "drop table if exists ${tablename}"

private val createBook = "create table Book ("+
        "id integer primary key autoincrement,"+
        "price real,"+
        "pages integer,"+
        "name text)"
private val createCategory = "create table Category(id integer primary key autoincrement , category_name text , category_code integer)"

override fun onCreate(db: SQLiteDatabase?) {
    db?.execSQL(createBook)
    db?.execSQL(createCategory)
    Toast.makeText(context,"Create succeed",Toast.LENGTH_SHORT).show()
}
```

我们在oncreate方法中调用了db来执行创建表的语句，最后提示表创建成功。

可是这里就存在一个问题，onCreate方法实际上只会在数据库创建的时候执行，也就是说，目前想去更新数据库的内容是无法通过Oncreate方法来执行的，这该怎么办呢？这就需要调用OnUpdate方法来使用了，在改变版本号的情况下，数据库会在创建的时候执行OnUpdate方法，我们只需要在创建管理类的时候传入一个比当前数据库版本更大的数字就可以了，代码如下：

```kotlin
val dbHelper = MyDatabaseHelper(this,"BookStore.db",2)
```

为了让其满足一定的鲁棒性，也就是说不管数据库存在不存在，是否需要升级，我们保留在Oncreate方法中创建数据库的操作，我们可以在OnUpdate方法中调用OnCreate。

最后方法实现结果如下：

```kotlin
    override fun onUpgrade(db: SQLiteDatabase?, oldVersion: Int, newVersion: Int) {
        tablename="Book"
        db?.execSQL(front_test)
        tablename="Category"
        db?.execSQL(front_test)
        onCreate(db)
    }
```

重新启动应用程序，效果如下：

![image-20220616100553867](https://s3.bmp.ovh/imgs/2022/06/16/66cbb88631bc124a.png)

需要注意的是，你在使用Android的真机调试的时候，实际上每次系统都会给你重新安装程序，也就是说，它会自动执行OncCreate方法重新创建一个数据库。

### 数据库的增删改查

有时候你可能还没学习过数据库的一些基础内容，不过没关系，Android帮我们实现了一套不需要SQL语句就能操作数据库的API，我们对于数据库的操作也就是SQL实际上功能就分为四种：CRUD。

在前面我们使用getWritableDatabase方法来创建和修改数据库，实际上这个方法返回的是一个SQLiteDatabase对象，我们借助这个对象就是可实现CRUD操作了。

#### 向数据库添加数据

SQLiteDatabase对象下文简称：SQL对象，包含一个insert方法：

```kotlin
public long insert(String table, String nullColumnHack, ContentValues values) 
```

第一个参数用来指定是哪一个table，第二个是用于在未指定添加数据的情况下给某些可以为空的列自动赋值为NULL，这里不需要，所以我们把它设置为NULL。第三个参数很直白，就是传入一个需要插入的值。需要注意的是，这里使用的是ContentValues对象。这个对象提供了一系列的方法来将需要的数据添加进去，使用方法如下：

我们通过来获取对应的SQL对象：

```kotlin
 val sql = dbHelper.writableDatabase
```

通过这个SQL对象来实现添加数据，再添加数据之前，我们先要把对应的数据嵌入到ContentValues对象当中，通过ContentValues对象指定将数据插入到哪一列当中，当然数据的插入要符合对应的数据类型否则会报错。

最终插入的代码如下所示：

```kotlin
binding.button.setOnClickListener {
   val sql = dbHelper.writableDatabase
   val values = ContentValues().apply {
        put("name","The Da Vinci Code")
        put("pages",188)
        put("price",16.96)
    }
   sql.insert("Book",null,values)
}
```

可以看到我们把对应的插入函数绑定到了按钮上，只要按下按钮就会执行对应的插入程序。

插入的结果如下：

![image-20220616201956921](https://s3.bmp.ovh/imgs/2022/06/16/d4da2ab096527caa.png)

这里有一个需要特殊注意的地方是，我们在本地浏览数据库内容的时候，需要将设备的信息同步刷新下载以后再使用SQLite浏览器打开，并且先关闭原来正在浏览的数据库后删除本地原来存在的数据库内容，防止因为正在访问而导致的本低内容更新失败，我并不推荐使用插件因为bug确实存在。

#### 修改对应的数据

对应有insert方法也有update方法，这个方法接收四个参数，第1个参数同样也是制定了操作的表名，指定更新哪张表的数据。第二个参数是ContentValues，就是需要更新的数据，当然也是通过ContentValues来构造对应的数据，并包装起来，第三个、第四个参数用于约束某一行或者某几行中的数据进行更新，没有约束的话默认更新所有数据。

举个例子：

对于参数的使用直接说起来，还是很抽象，我们不妨结合例子来说：

```kotlin
binding.update.setOnClickListener {
    //Data_update_method
    val sql = dbHelper.writableDatabase
    val values = ContentValues()
    values.put("price",10.99)
    sql.update("Book",values,"name=?", arrayOf("The Da Vinci Code"))
}
```

第一第二个参数就不再重复了，我们直接来说第三个参数，第三个参数实际上就是where字句，在这里传入的字符串自动和where拼接起来，实现筛选指定行的目的，比如这里就相当于：

```sql
where name=?
```

而这里的？就是一个占位符号，当你使用第四个参数的时候可以将一个字符数组的全部内容传递过来放到where单中，所以我在第四个数据当中使用了 arrayof来创建一个数组，当然为什么要用数组呢？实际上呢一个表达式里面可以包含多个占位符，所以这里使用数组来满足多个？的情况。

```kotlin
arrayOf("The Da Vinci Code")
```

操作结果如下：

可以看到所有的价格都改为了10.99

![image-20220616224216557](https://s3.bmp.ovh/imgs/2022/06/16/e485a1bb70c828a3.png)

#### 删除对应的数据

删除对应的数据更简单，参数和上边基本一样：

```kotlin
binding.delete.setOnClickListener {
    //Data_update_delete
    val sql = dbHelper.writableDatabase
    sql.delete("Book","name = ?", arrayOf("The Da Vinci Code"))
}
```

最后删除完成以后效果如下：

可以看到所有内容都被删除了。

![image-20220616225453207](https://s3.bmp.ovh/imgs/2022/06/16/2f4407fdd5027e09.png)

#### 查询对应的数据

SQL实际上是一种结构化的查询语言，他的大部分功能体现在“查”这个字上，具体的SQL操作，可以参考我的数据库专栏。

query相对来说很复杂，一共有七个参数，参数的含义分别如下：

![image-20220616230920144](https://s3.bmp.ovh/imgs/2022/06/16/f687645e791a0edb.png)

如果你不理解，没关系，请先参考我的数据库专栏，里面有详细介绍SQL的内容。

我在这里就很简单的举个例子：

```kotlin
binding.query.setOnClickListener {
    //Data_update_query
    val sql = dbHelper.writableDatabase
    val result =sql.query("Book", null,null,null,null,null,null)
    binding.datasave.setText( result.columnCount.toString())
    result.close()
}
```

我们新增一个查询按钮，这句查询语句的意思实际上就是获取当前表中有多少行，是不是很好理解？

其等价于SQL为

```sql
select * from Book
```

最后通过columnCount成员获取到对应的行数。

