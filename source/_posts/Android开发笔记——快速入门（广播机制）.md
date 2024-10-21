---
title:  Android开发笔记——快速入门（全局大喇叭）
categories:
  - Android
tags:
  - Android
  - BroadcastReceiver
excerpt: "全局大喇叭"
---

# Android开发笔记——快速入门（全局大喇叭）

<img src="https://s2.loli.net/2024/10/04/86NrLPCpqydHk2W.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727982374567" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />

#### 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]

请先参考前一篇文章复习一下Kotlin的一些语法。

## 广播机制简介

Android的每一个应用程序可以选择自己需要的广播来接收，可以是来自于系统的广播内容，也可以是来自某个应用程序的广播内容，下面介绍一下Android的广播的类型：

### 广播的类型

BroadcastReceiver是Android应用程序接收的主要实现类，他是每一个应用程序的广播接收者。

- 标准广播：

是一种完全异步的执行的广播，在广播发出以后，所有的BroadcastReceiver几乎会在同一个时刻收到这条广播，因此，这种广播没有任何顺序可言。效率较高，但无法截断。

广播流程示意图：

![image-20220621191208528](https://s3.bmp.ovh/imgs/2022/06/22/fe7b20e9672b0a13.png)

- 有序广播：

是一种完全同步执行的广播，在广播发出以后，同一个时刻，只会有一个BroadcastReceiver能够接收到这条广播信息，当这个BroadcastReceiver中的逻辑执行完毕以后，广播才会向下一个继续传递，所以这个广播是有前后顺序的，并且前边的BroadcastReceiver可以把广播截断这样后边的BroadcastReceiver就无法接收到消息了。

流程示意图如下：

![image-20220621191221501](https://s3.bmp.ovh/imgs/2022/06/22/c44404ed56d0f89d.png)



## 从接收系统广播开始

Android系统内置了很多广播，开机的时候就会自动发送一条广播，电池电量发生变化的时候会再发出来一条广播，系统时间发生变化的时候也会发出对应的广播，如果要接收这些广播，就需要使用BroadcastReceiver。

### 动态注册监听时间变化

在使用BroadcastReceiver前需要注册ManiFest，注册的方法有两种；

- 在代码中注册。

- 在ManiFest中注册。


我们先来讲解如何在代码中注册。

#### 动态注册BroadcastReceiver

我们新建一个项目，包含一个空的Act并将项目的名字设置为BroadcastReceiver。

如何创建一个BroadcastReceiver？只需要新建立一个类让他继承BroadcastReceiver就可以了，重写BroadcastReceiver的onReceiver方法，当有广播来的时候就会调用此方法进行处理。

修改MainActivity的代码如下：

```kotlin
class MainActivity : BaseActivity() {
    lateinit var  timeChangeReceiver : TimeChangeReceiver

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val intentFilter = IntentFilter()
        intentFilter.addAction("android.intent.action.TIME_TICK")
         timeChangeReceiver = TimeChangeReceiver()
        registerReceiver(timeChangeReceiver,intentFilter)
    }

    override fun onDestroy() {
        super.onDestroy()
        unregisterReceiver(timeChangeReceiver)
    }

    inner class TimeChangeReceiver : BroadcastReceiver()
    {
        override fun onReceive(context: Context?, intent: Intent?) {
            Toast.makeText(context,"Time is changed",Toast.LENGTH_SHORT).show()
        }
    }
}
```

这段代码看起来很复杂实际上思路很清晰，我们先从下边的TimeChangeReceiver类说起

```kotlin
inner class TimeChangeReceiver : BroadcastReceiver()
{
    override fun onReceive(context: Context?, intent: Intent?) {
        Toast.makeText(context,"Time is changed",Toast.LENGTH_SHORT).show()
    }
}
```

就像上文说的我们继承于BroadcastReceiver()类，并且重写了对应的onReceive方法，让他在收到系统的广播之后显示一个Toast表示时间变化了。

```kotlin
 override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val intentFilter = IntentFilter()
        intentFilter.addAction("android.intent.action.TIME_TICK")
         timeChangeReceiver = TimeChangeReceiver()
        registerReceiver(timeChangeReceiver,intentFilter)
    }
```

在onCreate方法之中我们通过这个 IntentFilter()方法构造了一个intent-Filter如果你不太清楚intent-Filter可以先去看看。

我们通过过滤器设置来设置接受的广播信息，其中**android.intent.action.TIME_TICK**就是Android发出的时间变化广播。

再将刚才声明的TimeChangeReceiver实例化，通过Context的函数registerReceiver来将这个过滤器和这个Act绑定到一起。最终实现在这个Act中时间变化广播的接收。

最后我们在onDestory方法中取消这个Receiver的注册：

这里调用的同样也是onContext里的方法。

```kotlin
    override fun onDestroy() {
        super.onDestroy()
        unregisterReceiver(timeChangeReceiver)
    }
```



#### 静态注册注册BroadcastReceiver

动态注册可以自由地注册与控制BoardcastReceiver，但是缺点也很明显，就是无法在不启动应用的情况下相应广播，要想实现这个功能就要使用静态注册的方式。

理论上来说，系统的广播不论在动态的BroadcastReceiver或者静态的BroadcastReceiver都应该能接收到对应的信息，但是在Android 8.0以后为了防止应用通过静态注册频繁自启动，就将静态注册获取到隐式广播的功能砍掉了，而大多数系统广播实际上是隐式广播，不过还好还留下了部分系统广播作为非隐式广播。

接下来我们演示如何通过静态广播实现应用自启动。

我们首先通过AS的引导来创建一个BroadcastReceiver：

![4](https://s3.bmp.ovh/imgs/2022/06/22/25dc693645eadcbb.png)

其中，Exported代表是否允许这个BroadcastReceiver接收本程序以外的广播，Enabled代表是否启用这个BroadcastReceiver，修改名字为`BootCompleteReceiver`后完成创建。

修改类的代码如下：

```kotlin
class BootCompleteReceiver : BroadcastReceiver() {

    override fun onReceive(context: Context, intent: Intent) {
        // This method is called when the BroadcastReceiver is receiving an Intent broadcast.
        Toast.makeText(context,"Boot Complete",Toast.LENGTH_SHORT).show()
  }
}
```

内容很简单就是在接收到广播的时候发出一条toast提示启动。

需要注意的是静态的BroadcastReceiver需要注册在Manifest的文件里面才可以使用，不过我们是通过引导的方式创建的，所以AS已经自动帮我们注册好了，是不是很方便？

我打开看一下：

![5](https://s3.bmp.ovh/imgs/2022/06/22/55eb477a9fd642bd.png)

出现了一个新的标签receiver，所有的BroadcastReceiver都是在这里注册的，他的注册方法和< Fragment>其实很相似，也是通过`android:name=".BootCompleteReceiver"`来指定绑定的是哪一个BroadcastReceiver。

不过需要注意的是我们没有指定这个BroadcastReceiver具体接收哪一个广播所以还是通过intent-filter来指定接受的广播。

修改后如下：

```xml
<receiver
    android:name=".BootCompleteReceiver"
    android:enabled="true"
    android:exported="true" >
    
<intent-filter>
    <action android:name="android.intent.action.BOOT_COMPLETED"/>
</intent-filter>
    
</receiver>
```

与其他广播不同的是有些系统广播是有很严格的权限要求的，这里不再详细赘述，只知道要在Manifest中添加一个权限声明获取即可：

```xml
<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
```

实测在荣耀magic40 最新版本下，Android12是无法获取到对应的广播的，但是在模拟器的原生google就可以：

![6](https://s3.bmp.ovh/imgs/2022/06/22/82c17426a25cbe5b.png)

## 发送自定义广播

在使用BroadcastReceiver接收广播以后，我们再来尝试使用如何发送广播。

### 发送标准广播

为了测试接收到的广播我们再来准备一个BroadcastReceiver作为接受对象，我们新建立一个类叫MyBroadcastReceiver，并在Mainfest里面添加这个BroadcastReceiver。

创建类代码入下：

```kotlin
class MyReceiver : BroadcastReceiver() {

    override fun onReceive(context: Context, intent: Intent) {
        // This method is called when the BroadcastReceiver is receiving an Intent broadcast.
        Toast.makeText(context,"Received the message",Toast.LENGTH_SHORT).show()
        
    }
```

对应的Mainfest添加代码如下：

```kotlin
<receiver
    android:name=".MyReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.MY_BOARDCAST" />
    </intent-filter>
</receiver>
```

这里设置让其接收一个`android.intent.action.MY_BOARDCAST`广播，等下就要发送这样一条广播。

在布局中再添加一个button如下：

```xml
<Button
    android:id="@+id/button"
    android:layout_width="0dp"
    android:layout_height="wrap_content"
    android:text="Send a boardcast"
    app:layout_constraintBottom_toBottomOf="parent"
    app:layout_constraintEnd_toEndOf="parent"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintTop_toTopOf="parent" />
```

效果如下：

![7](https://s3.bmp.ovh/imgs/2022/06/22/8c215ddb78bac494.png)

然后在MainActivity中调用viewbinding来获取到对应button的实例。

```kotlin
class MainActivity : BaseActivity() {
    lateinit var  timeChangeReceiver : TimeChangeReceiver
    lateinit var binding: ActivityMainBinding
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val intentFilter = IntentFilter()
        intentFilter.addAction("android.intent.action.TIME_TICK")
         timeChangeReceiver = TimeChangeReceiver()
        registerReceiver(timeChangeReceiver,intentFilter)

        /*Send a boardcast*/
        binding=ActivityMainBinding.inflate(layoutInflater)
        binding.button.setOnClickListener {
            val intent = Intent("android.intent.action.MY_BOARDCAST")
            intent.setPackage(packageName)
            sendBroadcast(intent)
        }
        setContentView(binding.root)
    }
    .......
}
```

在这一段中我们给button添加了一个点击回调函数：

```kotlin
/*Send a boardcast*/
binding=ActivityMainBinding.inflate(layoutInflater)
binding.button.setOnClickListener {
    val intent = Intent("android.intent.action.MY_BOARDCAST")
    intent.setPackage(packageName)
    sendBroadcast(intent)
}
setContentView(binding.root)
```

首先我们创建了一个Intent指明了发送广播的**意图**，这里用的就是上文注册的广播，然后通过setPackage方法指定发送的应用名称，通过**packageName**来获取到本应用的包名。最后调用**sendBroadcast(intent)**方法发送广播，这里需要注意的是，在默认情况下，如果你不用setPackage指明是哪一个应用，那么他就无法发送，因为上文提到过，没有指明包名的话它默认的是一个匿名的广播，而目前的系统是禁止发送匿名广播的。

最后实现效果，点击button:

![8](https://s3.bmp.ovh/imgs/2022/06/22/61d96c6c035525a8.png)

### 发送有序广播

发送有序广播的方法很简单，和标准广播几乎类似，为了验证标准广播是可以被截断的，我们再创建一个和上一小节一样的BroadcastReceiver来接收消息。

代码如下所示：

```kotlin
package com.example.boardcastreceiver

import android.content.BroadcastReceiver
import android.content.Context
import android.content.Intent
import android.widget.Toast

class MyReceiver2 : BroadcastReceiver() {

    override fun onReceive(context: Context, intent: Intent) {
        // This method is called when the BroadcastReceiver is receiving an Intent broadcast.
        Toast.makeText(context,"Received the message 2", Toast.LENGTH_SHORT).show()

    }
}
```

创建完成以后记得修改mainfest

```xml
<receiver
    android:name=".MyReceiver2"
    android:enabled="true"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.MY_BOARDCAST" />
    </intent-filter>
</receiver>
```

到这一步发送的还是标准广播。两个Receiver依次把内容显示出来。

想要改成有序广播很简单，直接修改发送的函数即可。

代码如下：

```kotlin
/*Send a boardcast*/
binding=ActivityMainBinding.inflate(layoutInflater)
binding.button.setOnClickListener {
    val intent = Intent("android.intent.action.MY_BOARDCAST")
    intent.setPackage(packageName)
    sendOrderedBroadcast(intent,null)
}
setContentView(binding.root)
```

`sendOrderedBroadcast(intent,null)`就能发送对应的有序广播，第二个参数是一个与权限相关的字符串，这里先不讨论。

![9](https://s3.bmp.ovh/imgs/2022/06/22/ae628ffda166e49c.png)

![10](https://s3.bmp.ovh/imgs/2022/06/22/3ec4b0ef2f071c94.png)

我先说如何变成有序：

修改mainfest的代码曾加第一个receiver的优先级：

```xml
<receiver
    android:name=".MyReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter android:priority="100">
        <action android:name="android.intent.action.MY_BOARDCAST" />
    </intent-filter>
</receiver>
```

这时候谁的优先级大谁就能优先接收到对应的消息。

那么如何截断消息呢？

也很简单直接在对应的BroadcastReceiver类中修改onReceive方法即可：

```kotlin
class MyReceiver : BroadcastReceiver() {

    override fun onReceive(context: Context, intent: Intent) {
        // This method is called when the BroadcastReceiver is receiving an Intent broadcast.
        Toast.makeText(context,"Received the message",Toast.LENGTH_SHORT).show()
        abortBroadcast()
    }
}
```

这时候就能截断消息了，方法还算很简单。

我们在来测试一下，发现只能接收一个消息了而且还只能是第一个Receiver。

![11](https://s3.bmp.ovh/imgs/2022/06/22/6c6bfb6008248a5f.png)

### 最佳实现——强制下线功能

强制下线的功能是一个很常见的功能，在某些时刻你的账号的异地登陆了，就需要将目前在线的内容强制下线，从技术层面上来讲，强制下线就是弹出一个对话窗，不管用户选择哪一个选项，都将退出当前的Activity，返回至登录界面。

下面我们来实现一下这个功能：

首先要实现的功能是在任意的Activity中都能退出程序，这个功能的实现方式已经在Activity讲过了，这里就不再详细了。

我们先来实现一个登陆界面：

```xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:orientation="horizontal"
        android:layout_width="match_parent"
        android:layout_height="60dp">
        <TextView
            android:layout_width="90dp"
            android:layout_height="wrap_content"
            android:layout_gravity="center_vertical"
            android:textSize="18sp"
            android:text="Account:" />

        <EditText
            android:id="@+id/accountEdit"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_gravity="center_vertical" />
    </LinearLayout>

    <LinearLayout
        android:orientation="horizontal"
        android:layout_width="match_parent"
        android:layout_height="60dp">
        <TextView
            android:layout_width="90dp"
            android:layout_height="wrap_content"
            android:layout_gravity="center_vertical"
            android:textSize="18sp"
            android:text="Password:" />

        <EditText
            android:id="@+id/passwordEdit"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:layout_gravity="center_vertical"
            android:inputType="textPassword" />
    </LinearLayout>

    <Button
        android:id="@+id/login"
        android:layout_width="200dp"
        android:layout_height="60dp"
        android:layout_gravity="center_horizontal"
        android:text="Login" />
</LinearLayout>
```

效果如下：

![12](https://s3.bmp.ovh/imgs/2022/06/22/cd232613da09a051.png)

我们在登陆按钮添加如下逻辑：

```kotlin
/*login*/
binding=ActivityMainBinding.inflate(layoutInflater)
binding.login.setOnClickListener {
    val account = binding.accountEdit.text.toString()
    val password = binding.passwordEdit.text.toString()
    
    if(account == "admin" && password =="123") {
        val intent = Intent(this,OfflineActivity::class.java)
        startActivity(intent)
        finish()
    }else{
        Toast.makeText(this,"account or password is invalid",Toast.LENGTH_SHORT).show()
    }
}
```

实现的内容很简单，就是读取账户和密码来校验一下，登陆成功后开启另一个OffilineActivity，并将这个Activity关闭。

我们再来看一下登陆后的Activity中有什么内容：

首先布局很简单：

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".OfflineActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:text="OFFLINE"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

只包含一个用于下线的按钮。

![13](https://s3.bmp.ovh/imgs/2022/06/22/295ab209e6055946.png)

为按钮添加一个回调，当点击的时候就会发出一个广播让对应的BroadcastReceiver来处理当前的事件。

代码如下：

```kotlin
class OfflineActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        lateinit var binding: ActivityOfflineBinding
        super.onCreate(savedInstanceState)
        setContentView(binding.root)
        binding.button.setOnClickListener {
            val intent = Intent("android.intent.action.MY_BOARDCAST")
            sendBroadcast(intent)
        }
    }
}
```

终于到了实现强制下线功能的核心阶段了，我们需要考虑的有两个方面，首先是

我怎么样才能弹出对应的对话框呢？单纯的一个静态的BroadcastReceiver是无法弹出任何一个UI组件的，因为不管哪个UI都需要在一定的context来构建，而在一个单独的静态的Receiver里面的context实际上是他自身，不信输出一下看看：

```kotlin
override fun onReceive(context: Context, intent: Intent) {
    // This method is called when the BroadcastReceiver is receiving an Intent broadcast.
    Toast.makeText(context,context.toString(),Toast.LENGTH_SHORT).show()
}
```

可以看到对应的context实际上是不对的，无法为UI提供支持：

![14](https://s3.bmp.ovh/imgs/2022/06/22/b1807016dbf90b92.png)

那么应该怎么办呢？也很简单我们在BaseActivity中动态注册一个receiver这样所有的Act中都包含了一个receiver。

动态注册的办法在前面已经讲得很详细了，这里就不在多讨论了直接看代码。

```kotlin
open class BaseActivity : AppCompatActivity() {

    val tag :String = javaClass.simpleName
    lateinit var receiver:OfflineReceiver

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        Log.d(tag,"onCreate")
        ActivityCollector.addActivity(this)
    }
    ........
    override fun onResume() {
        super.onResume()
        val intentFilter = IntentFilter()
        intentFilter.addAction("android.intent.action.MY_BOARDCAST")
        receiver = OfflineReceiver()
        registerReceiver(receiver,intentFilter)
        Log.d(tag, "onResume")
    }
    override fun onPause() {
        super.onPause()
        unregisterReceiver(receiver)
        Log.d(tag, "onPause")
    }
    ............
    inner  class OfflineReceiver:BroadcastReceiver() {

        override fun onReceive(context: Context, intent: Intent?) {
            AlertDialog.Builder(context).apply {
                setTitle("WARNING")
                setMessage("FORCE TO OFFLINE")
                setCancelable(false)
                setPositiveButton("OK") { _, _
                    ->
                    ActivityCollector.finishALL()
                    val i = Intent(context, MainActivity::class.java)
                    context.startActivity(i)
                }
                show()
            }
        }
    }

}
```

在这里我们实现了在onReceive方法里面为当前界面注册了一个AlertDialog，其中这里的context实际上是：

![image-20220622095300526](https://s3.bmp.ovh/imgs/2022/06/22/7f99a0a37156d6ed.png)

基于Activity的，这样就能生成一个DIALOG了。

但是具体是为什么，静态和动态的具体差距还不清楚，有时间再研究一下。





































