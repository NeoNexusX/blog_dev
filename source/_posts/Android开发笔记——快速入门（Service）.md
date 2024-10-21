---
title:  Android开发笔记——快速入门（入门Service）
categories:
  - Android
tags:
  - Android
  - Android_Service
excerpt: "入门Service"
---

# Android开发笔记——快速入门（入门Service）

<img src="https://s2.loli.net/2024/10/04/GgvAfI4DCxnYPJT.jpg" alt="[lab.magiconch.com][福音戰士標題生成器]-1727980973253" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/09/18/zXu5EpoCmKH8FiJ.jpg" alt="标准监督" style="zoom: 50%;" />



#### 软件环境：

- **Jetbrains Toolbox**
- **Android Sudio 2021.1.1 Bumblebee**
- **JDK 17.0.2**

<!-- toc -->

[TOC]

请先参考前一篇文章复习一下Kotlin的一些语法。

**大部分内容参考了郭霖先生的《第一行代码》，在书的基础上针对目前的实际情况进行实践记录。**

## Android的Service

Service是为了解决那些不需要和用户通过界面交互但还是需要长期运行的程序需求，Service的运行不依赖于任何界面，即使程序切换到后台，或者用户打开了另一个应用程序，Service依然能在后台稳定运行。

不过Service的实现并不是单独创建了一个进程，而是依赖于创建Service的进程，当某个程序被kiil掉之后，依赖于该进程的Service也会被Kill掉。

更需要注意的是，Service不会自动开启线程去处理内部的操作，实际上是需要你手动开启线程来处理操作，否则就有可能导致主界面内容不刷新（主线程操作被Service占满了）。

下面来介绍一下，Android如何开启线程。

## Android的多线程操作

Android的多线程操作基本就是移植Java的操作，在kotlin中使用Java的API我们常常是用lambda表达式来创建一个线程，类似于如下所示：

```kotlin
Thread(){
    println("YES")
}.start()
```

实际上Kotlin提供了更方便快捷的函数来实现操作：

```kotlin
 thread { 
     println("YES")
 }
```

他实现的方法就是对Java接口的再封装：

```kotlin
public fun thread(
    start: Boolean = true,
    isDaemon: Boolean = false,
    contextClassLoader: ClassLoader? = null,
    name: String? = null,
    priority: Int = -1,
    block: () -> Unit
): Thread {
    val thread = object : Thread() {
        public override fun run() {
            block()
        }
    }
    if (isDaemon)
        thread.isDaemon = true
    if (priority > 0)
        thread.priority = priority
    if (name != null)
        thread.name = name
    if (contextClassLoader != null)
        thread.contextClassLoader = contextClassLoader
    if (start)
        thread.start()
    return thread
}
```

## Android异步消息处理机制

我们知道多线程的情况下，有可能存在线程数据不安全的问题，Android提供了一套异步处理机制，来保证数据的安全，当你需要在一个线程中去操作另一个线程中的内容的时候，就可以使用这一套机制来实现数据修改。

在Android中所有的UI界面显示的数据都是线程不安全的，也就是说你无法在其他线程直接访问UI的数据并修改其中的内容，因为这样会导致抛出线程不安全的异常。有些时候，我们必须在子线程里面执行一些耗时的任务，然后根据任务结果来更新UI的内容。

在讨论这个部分之前，请先参考部分Kotlin语法的内容。在完全了解异步线程机制的之前，我们不妨来先写一个试试。

下面我们来举个例子：

我们为了实现音乐播放器在暂停的时候显示音乐已经暂停的文本，我们在界面之中放置一个TextView来显示一些信息，再通过一个线程来修改它，具体代码如下：

```kotlin
class MainActivity : AppCompatActivity() {
    lateinit var binding : ActivityMainBinding
    val SongisPause = 1
    val handler = object : Handler(Looper.getMainLooper()) {
        override fun handleMessage(msg: Message) {
            super.handleMessage(msg)
            when (msg.what){
                SongisPause -> binding.textView.text ="Song is Pause"
            }
        }
    }
    
    
     override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding= ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
         
        binding.stop.setOnClickListener {
            thread {
                val msg = Message()
                msg.what = SongisPause
                handler.sendMessage(msg)
            }
        }
     }
    
}
```

这段代码看起来很复杂难以理解，我们不妨在再拆开看一看：

在类的开始我们初始化了一些变量和对象：

```kotlin
lateinit var binding : ActivityMainBinding
val SongisPause = 1
val handler = object : Handler(Looper.getMainLooper()) {
    override fun handleMessage(msg: Message) {
        super.handleMessage(msg)
        when (msg.what){
            SongisPause -> binding.textView.text ="Song is Pause"
        }
    }
}
```

前两个就不再多说分别是一个绑定对象和一个一般的变量，需要仔细了解的是下面的这一个对象，这个handler使用了对象表达式来构造自己，他继承了Handler类，看到名字你也应该能猜到他是具体干什么的，他就是处理，具体去操作的函数。不过为什么这里要声明一个具体操作的函数，其背后原理的概念我们晚点再说，这里先理解最后线程的操作归咎于这里。

```kotlin
binding.stop.setOnClickListener {
    thread {
        val msg = Message()
        msg.what = SongisPause
        handler.sendMessage(msg)
    }
}
```

我们又在onCreate函数里面为暂停按钮绑定了一个回调函数，在回调函数里面我们使用thread函数创建了一个线程去处理暂停的操作，实际上我们是创建了一个message来向刚才创建的handler发送了一条消息，提示他该暂停了，当handler接收到了消息以后就会执行刚刚注册到handler的操作。

最终先效果如下：

![And](https://s3.bmp.ovh/imgs/2022/06/26/ba6f7fd04f661604.png)

看完以后你可能一脸懵，什么鬼？我们再来讨论一下，其背后实现的原理。

实际上我们可以注意到，接受收消息处理的操作还是在主线程中完成的，并不是在子线程中完成的，子线程实际上只是发送了meaasge，可是，message、handler和隐藏在背后的looper、MessageQueue都是什么呢？

Message：

是在线程中间传递的消息，它可以携带少量的内部信息，用于在不同线程之间传递数据，上一节我们使用了Message的what字段，除此之外他还包含arg1、arg2字段，用来携带一些整型数据，还有obj字段用来携带一个Object对象。

Handler：

它主要用于发送和处理信息，发送消息一般使用sendMessage和Post方法，而发出的消息经过内部处理以后，最终会传递到Handler的handleMessage方法当中。

MessageQueue：

是隐藏在消息背后的数据解构，里面以队列的形式存放着所有通过Handler发送的消息。这部分消息会一直存放在队列中，直到被取出后处理。每一个线程中最多只允许存在一个MessageQueue对象。

Looper

Looper是每个线程中的管家，调用Looper以后会进入到loop方法中，这是一个无限循环，每当MessageQueue中存放着一条消息的时候们就会将它自动取出来，并将消息内容传递到handleMessage()方法中。

我们主要来说一说Looper和MessageQueue是在哪里创建的，以及他们是如何串起来工作的。

Loop很简单，在主线创建的时候，就会自动产生一个属于主线程的looper对象，我们一般通过以下方式获取到looper对象：

1. 使用Looper静态方法getMainLooper()
2. 使用Looper的静态方法获取到当前线程的Looper对象。

```kotlin
Looper.getMainLooper()
```

一般情况下我们不会去手动创建一个loop去和某个线程产生关联，大多数直接调用拥有Looper的线程，比如主线程，如果想要创建的话请参考：[Looper文档](https://developer.android.google.cn/reference/android/os/Looper#getMainLooper%28%29)。

MessageQueue其中的技术细节我们不探讨，我只考虑如何创建的MessageQueue以及MessageQueue是如何前边几个对象产生关联的。

在Hnadler接口的初始化函数里面我们可以看到：

```java
@UnsupportedAppUsage
public Handler(@NonNull Looper looper, @Nullable Callback callback, boolean async) {
    mLooper = looper;
    mQueue = looper.mQueue;
    mCallback = callback;
    mAsynchronous = async;
}
```

队列直接和looper是相互存在的，我们在looper的内部构造函数中可以看到，在Looper创建的时候就会创建配套的MessageQueue。

```kotlin
private Looper(boolean quitAllowed) {
    mQueue = new MessageQueue(quitAllowed);
    mThread = Thread.currentThread();
}
```

也就是说在一个线程里面，创建了Looper就相当于创建好了两个通信组件，Looper和MessageQueue是相互绑定的。

那么Looper是如何和Handler来产生关联的呢？

很简单就在Handler的构造函数啊，可别忘了我们是传入了一个Looper进去的。

```kotlin
val handler = object : Handler(Looper.getMainLooper())
```

那么在looper里面是如何调用我传入的处理函数呢？详细解答请看：stackoverflow上一个大佬的解释：[连接](https://stackoverflow.com/questions/14030534/how-the-looper-knows-to-send-the-message-to-handler)。

这里不再做过多解释。最后放一张图来帮助你理解：

![Servic-1](https://s3.bmp.ovh/imgs/2022/06/26/b0df7efa37bcc9bf.png)

当然更具体的详解可以看：

[同步异步消息详解](https://blog.csdn.net/qq_39431405/article/details/113716185)。

[为什么looper一直循环却不占满cpu？](https://stackoverflow.com/questions/38818642/android-what-is-message-queue-native-poll-once-in-android)

### AsyncTask

AsyncTask是另一种多线程工具， 借助AsyncTask，即使你对异步消息处理机制完全不了解，也可以十分简单的从子线程切换到主线程。当然Async背后也是上述异步消息处理机制构成的，只是Android提供了更好的封装组成了的AsyncTask。

首先我们来看一下用法：

AsyncTask是个抽象类，如果我们想使用它，就必须创建一个子类去继承他，在继承的时候，可以为其指定三个泛型参数，这三个参数的用途如下：

Params  在执行AsyncTask时候需要传入的参数，可用于后台任务中使用。

Progress 在后台任务执行的时候，如果需要在界面上显示当前的进度，则使用这里指定的泛型作为单位。

Result 当任务执行完毕以后，如果需要对结果进行返回，则这里指定的泛型作为返回值类型。

```kotlin
inner class Download: AsyncTask<Unit, Int, Boolean>() {}
```

我们在这里将Params设置为Unit代表线程是不需要参数的设置为Unit。至于这样设置效果是什么请看下边的方法，同时还存在四个需要重写的方法：

- onPreExecute()

​	这个方法会在后台任务开始之前调用，用于进行一些界面上的初始化操作，比如显示一下进度条对话框等。

- doInBackground(Params )

这个方法中的所有代码都会在子线程中运行，我们应该在这里去处理所有耗时的任务。任务一旦完成，就可以通过return语句将任务的执行结果返回，如果AsyncTask的第三个泛型参数指定的是Unit，就可以不返回任务执行结果.注意此方法中是不可以进行UI操作的，因为此时实际上是在另一个线程并不是在主线程。如果需要反馈任务进度，可以调用publishProgress来实现。

- onProgressUpdate(Progress)

当后台任务调用了 pulishProgress（Progress）方法以后，onProgressUpdate方法就很快会被调用，该方法中携带的参数是在后台任务传递过来的。在这个方法中可以针对UI进行操作，利用参数中的数值，可以对界面元素进行更新。

- onPostExecute(Result)

当后台任务之执行完毕并通过return语句进行返回的时候，这个方法很快就会被调用，返回的数据会作为参数传递到此方法当中。可以利用返回的数据进行一些UI操作，比如提醒任务执行的结果，以及关闭进度条等。

```kotlin
inner class Download: AsyncTask<Unit, Int, Boolean>() {

     override fun onPreExecute() {
         super.onPreExecute()
         binding.progressBar.visibility=View.VISIBLE
     }

     override fun onPostExecute(result: Boolean?) {
         super.onPostExecute(result)
         if(result == true) {
             binding.progressBar.setProgress(100,true)
             binding.textView.text="Jszszzy"
         }
     }

    override fun doInBackground(vararg params: Unit?): Boolean {
        Log.d("doInBackground","This is background")
        publishProgress(10)
        return true
    }

    override fun onProgressUpdate(vararg values: Int?) {
         super.onProgressUpdate(*values)
        values[0]?.let { binding.progressBar.setProgress(it,true) }
        Toast.makeText(baseContext,"Downloading",Toast.LENGTH_SHORT).show()
     }

 }
```

我们将第二参数设置为Int，这样调用publishProgress()的时候，切换到主线程，并且给主线程的方法传入一个参数为Int,这样我们就可以获得具体的参数，并且在主线程中更新U。

可惜的是，目前AsyncTask API 在Android10 已经被弃用，目前推荐的是使用协程来实现相同的操作，这个我们在Kotlin专栏里面再去讨论，接下来直接进入Service。

### Android 的Service

创建一个Service，我们在项目的顶层包下右键，new->Service->Service。

![service](https://s3.bmp.ovh/imgs/2022/06/26/169026e4d4df6fea.png)

创建一个以后我们再来重写几个方法：

```kotlin
class PlayerService : Service() {

    override fun onBind(intent: Intent): IBinder {

    }

    override fun onCreate() {
        super.onCreate()
    }

    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        return super.onStartCommand(intent, flags, startId)
    }

    override fun onDestroy() {
        super.onDestroy()
    }
}
```

其中：

onCreate方法在Service创建的时候调用。

onStartCommand方法在每次Service启动的时候调用。

onDestory方法会在Service销毁的调用。

关于具体的生命周期和调用我们留到生命周期部分再来详细说。

实际上在我们通过Service向导创建以后，AS在Manifest文件中帮我们声明好了对应的Service，所有的Service都需要在Manifest文件中声明以后才能使用。实际上Android的四大组件都需要在Manifest文件中来声明。

```xml
<service
    android:name=".PlayerService"
    android:enabled="true"
    android:exported="true">
</service>
```

#### 与Activity紧密结合

虽然Service是在Activity中启动的，但是启动了之后好像与Activty并没有什么太大关系了，但是如果我们想让Service和Act有一点交流呢？比如传递个参数什么的，比如我们有些时候需要在Act中去控制Service的一些方法，让他去进行一些任务的操作，这就需要我们刚刚专门没有说的onBind()方法了。

修改Service类，创建一个Binder对象，用来向Act提供内部的方法。

```kotlin
inner class PlayerBinder:Binder()
 {
     fun initPlayer(){
         initMediaPlayer()
     }
 }
```

在类里面创建成员：

```kotlin
private val mBinder = PlayerBinder()
```

同时在onBind方法中返回对象，在Act绑定的时候会带调用这个方法，并将绑定对象返回给Act中的绑定方法。

```kotlin
override fun onBind(intent: Intent?): IBinder {
    return mBinder
}
```

##### 如何在Act中绑定Service？

首先创建一个与Service连接的类，这里我们使用对象表达式，连同对应的对象一起创建了：

```kotlin
private  val connection = object : ServiceConnection{
    
    override fun onServiceConnected(name: ComponentName?, service: IBinder) {
        playerBinder = service as PlayerService.PlayerBinder
        Log.e("jszszzy",playerBinder.toString())
        playerBinder.initPlayer()
    }
    
    override fun onServiceDisconnected(name: ComponentName?) {
    }
}
```

创建ServiceConnection的实体对象需要实现其中的量抽象方法：

onServiceConnected是在Act与Service建立连接完成的时候回调的，传入的参数包含binder对象，实际上就是通过刚才创建的binder类构造的，在创建的时候会调用Service的onBind方法来返回一个binder对象。

我们当然可以在这个方法中获取到对应的绑定对象，通过绑定对象就可以在下文代码中使用Binder对象中的方法。

比如在这里我们使用了一个初始化播放器的方法，就是直接调用binder对象中的的方法。当然你也可以直接在Act中创建一类成员（`lateinit var binding : ActivityMainBinding`），让他直接获取到binder中的方法，这样你就可以在Act中的任意位置获取到binder对象，并调用他其中的方法。

```kotlin
class MainActivity : BaseActivity() {
    lateinit var binding : ActivityMainBinding
    lateinit var playerBinder: PlayerService.PlayerBinder

    private  val connection = object : ServiceConnection{
        override fun onServiceConnected(name: ComponentName?, service: IBinder) {
            playerBinder = service as PlayerService.PlayerBinder
            Log.e("jszszzy",playerBinder.toString())
            playerBinder.initPlayer()
        }

        override fun onServiceDisconnected(name: ComponentName?) {
        }
    }
}
```

你可能注意到了里面其实还有一个onServiceDisconnected方法，它只有在Service创建进程崩溃或者被杀掉的时候才会调用，这个方法不太常用，所以空着不写。

当然到这里还没有进行绑定呢，绑定的方式也很简单，我们来举个例子：

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
     val intent = Intent(this, PlayerService::class.java)
     bindService(intent, connection, Context.BIND_AUTO_CREATE)
}
```

我们直接在onCreate回调中执行绑定的代码，先构造要绑定的Service意图，这和前边的intent差不多，这里就不再赘述，再通过context的`bindService`方法，传入`intent`，和刚才构造的`ServiceConnection`对象，还有一个标志位`Context.BIND_AUTO_CREATE`，其代表的意思是，在Act和Service绑定的时候自动创建一个Service对象，**其实就是调用OnCreate方法，但是这个过程中，onStartCommand()方法并不会执行。**

解除绑定的方法也很简单	，使用unbindService方法即可解除绑定，同**时Service也会执行onDestroy方法来销毁Service。**

我们为界面一个名为stop按钮添加一个解绑的回调函数：

```kotlin
binding.stop.setOnClickListener {
    playerBinder.stopPlayer()
    unbindService(connection)
}
```

### 注意

Service这个功能是一个极其特殊的功能功能，他在绑定的时候部分代码过程并不是在主线程完成的，虽然我们知道Service只是一个长久运行的模块，他不创建进程，也不创建线程，他需要运行的时候是直接在主线程中运行的，但是初始化的时候是通过Android AMS来帮助创建的，这就会导致一个问题，即我在某个阶段直接去调用它的初始化函数可能导致，变量没有被正确赋值，因为在其他AMS还没有执行到对应的赋值代码。举个例子：

如果我们绑定调用完就立马执行binder里面的方法就会报错：

我们不再在OnCreate方法里面绑定我们在一个按钮的会带函数里面绑定并且调用绑定的方法：

```kotlin
binding.play.setOnClickListener {
    val intent = Intent(this, PlayerService::class.java)
    bindService(intent, connection, Context.BIND_AUTO_CREATE)
    playerBinder.startPlayer()
}
```

我们点击按钮就会产生报错：

```powershell
E/AndroidRuntime: FATAL EXCEPTION: main
    Process: com.example.audiotest, PID: 709
    kotlin.UninitializedPropertyAccessException: lateinit property playerBinder has not been initialized
        at com.example.audiotest.MainActivity.getPlayerBinder(MainActivity.kt:17)
        at com.example.audiotest.MainActivity.onCreate$lambda-0(MainActivity.kt:49)
        at com.example.audiotest.MainActivity.$r8$lambda$NLfemTBvJsTkHif14Uhm1FqFVaI(Unknown Source:0)
        at com.example.audiotest.MainActivity$$ExternalSyntheticLambda1.onClick(Unknown Source:2)
        at android.view.View.performClick(View.java:7603)
        at com.google.android.material.button.MaterialButton.performClick(MaterialButton.java:1119)
        at android.view.View.performClickInternal(View.java:7577)
        at android.view.View.access$3800(View.java:865)
        at android.view.View$PerformClick.run(View.java:29375)
        at android.os.Handler.handleCallback(Handler.java:955)
        at android.os.Handler.dispatchMessage(Handler.java:102)
        at android.os.Looper.loopOnce(Looper.java:206)
        at android.os.Looper.loop(Looper.java:296)
        at android.app.ActivityThread.main(ActivityThread.java:8899)
        at java.lang.reflect.Method.invoke(Native Method)
        at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:569)
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:976)
```

实际上就是我们的binder对象没有初始化完成，就在主线程里调用了binder的方法。所以绑定和调用需要分开在两个不同的阶段，以保证绑定的时候binder对象被正确的赋值。

#### Service生命周期

我们在每一个回调函数里面加入Log来提示对应的生命周期，具体的代码如下：

```kotlin
class PlayerService : Service() {

    override fun onBind(intent: Intent?): IBinder? {
        TODO("Not yet implemented")
    }

    override fun onCreate() {
        super.onCreate()
        Log.d("Service","This is onCreate Service")
    }

    override fun onStartCommand(intent: Intent?, flags: Int, startId: Int): Int {
        Log.d("Service","This is onStartCommand Service")
        return super.onStartCommand(intent, flags, startId)

    }

    override fun onDestroy() {
        Log.d("Service","This is onDestroy Service")
        super.onDestroy()
    }
}
```

我们在主函数中向一个button中添加按钮事件，按下按钮的时候开启Service,在另一个按钮按下的时候关闭Service。

当我们按下开启的按钮的时候，对应回调执行如下：



![service-1](https://s3.bmp.ovh/imgs/2022/06/26/c0a3d0922b8d7fde.png)

当我们按下结束的时候：

![service-2](https://s3.bmp.ovh/imgs/2022/06/26/9f10bc948c9cc030.png)

下面来说一下，onCreate，onStartCommand存在什么区别，onStartCommand在每次Service启动的时候都会去调用这个方法，而onCreate方法只有在第一次Service被调用创建的时候才会使用，Service启动了之后，会一直保持启动状态，直到stopService或者stopSelf方法被调用的时候Service才会停止。

这都是很常规的情况，如果一个Service被startService()和bindService()同时调用，那么在这种情况下就需要调用stopService和unbindService方法，这样OnDestory才会执行。

代码留档：

```kotlin
package com.example.audiotest
import android.content.Intent
import android.media.MediaPlayer
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.os.Handler
import android.os.Looper
import android.os.Message
import com.example.audiotest.databinding.ActivityMainBinding
import kotlin.concurrent.thread

class MainActivity : AppCompatActivity() {
    lateinit var binding : ActivityMainBinding
    private val mediaPlayer = MediaPlayer()
    val SongisPause = 1
    val handler = object : Handler(Looper.getMainLooper()) {
        override fun handleMessage(msg: Message) {
            super.handleMessage(msg)
            when (msg.what){
                SongisPause -> binding.textView.text ="Song is Pause"
            }
        }
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding= ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        initMediaPlayer()
        binding.play.setOnClickListener {
            val intent = Intent(this,PlayerService::class.java)
            startService(intent)
            if (!mediaPlayer.isPlaying)
            {
                mediaPlayer.start()
            }
        }
        binding.stop.setOnClickListener {
            thread {
                if (mediaPlayer.isPlaying){
                    val intent = Intent(this,PlayerService::class.java)
                    stopService(intent)
                    val msg = Message()
                    msg.what = SongisPause
                    handler.sendMessage(msg)
                    mediaPlayer.reset()
                    initMediaPlayer()
                }
            }

        }

        binding.pause.setOnClickListener {
            if (mediaPlayer.isPlaying)
                mediaPlayer.pause()
        }
    }


    private fun initMediaPlayer()
    {
        val assetManager = assets
        val fd = assetManager.openFd("music.mp3")
        mediaPlayer.setDataSource(fd.fileDescriptor,fd.startOffset,fd.length)
        mediaPlayer.prepare()
    }

    override fun onDestroy() {
        super.onDestroy()
        mediaPlayer.stop()
        mediaPlayer.release()
    }
}
```













