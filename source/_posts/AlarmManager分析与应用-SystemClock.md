---
title: AlarmManager分析与应用----SystemClock
date: 2017-09-28 11:44:54
tags: Android源码分析
---
<h1>SystemClock</h1>
public final class SystemClock extends Object
java.lang.Object
    ↳android.os.SystemClock
location:/frameworks/base/core/java/android/os/SystemClock.java
<hr/>
核心计时工具
有三种不同的时钟，不可以混用：

+  System.currentTimeMillis()　标准的”wall” clock，获得的是一个自从1970-1-1 0时以来的毫秒数．它可以被用户或者手机网络设置．（见 setCurrentTimeMillis(long)),所以这个时间可能不可预见地向前或向后跳跃．这个时钟应该只有当与现实时间一直时使用，例如在一个日历或者闹铃应用．时间间隔或者过去时间测量(Interval or elapsed time measurements)应该使用其它时钟．如果你正在使用System.currentTimeMillis(),建议你监听ACTION_TIME_TICK,ACTION_TIME_CHANGED和ACTION_TIMEZONE_CHANGED广播以及时发现时间变化．
+  uptimeMillis()　获取的是自从开机以来的毫秒数．当系统进入深度睡眠(CPU off, display dark, device waiting for external input)时此时钟会停止．但是不会受到clock scaling，idle,或者其他电池优化机制影响．它是大多数间隔时间，比如Thread.sleep(millis),Object.wait(millis) 和System.nanoTime()等．这个时钟保证不会变化，当设备不会休眠的时间内，这个时钟适合用来计算时间间隔．
+  elapsedRealtime()和elapsedReadtimeNanos()　获取的是自动开机以来的毫秒数，包括系统深度睡眠．这个时钟保证不会变化，即使CPU处于节能模式也能继续计时．所以这是最推荐的用来计时的方法．

以下是一些延时机制：

+  标准的方法比如Thread.sleep(millis)和Object.wait(millis)总是可行的．这些方法使用的是uptimeMillis()时钟，如果设备进入休眠．剩余的时间将会被延迟直到设备重新被唤醒．这些同步的方法也许会被Thread.interrupt()方法中断，所以你必须处理InterruptedException
+  SystemClock.sleep(millis)是一个非常类似Thread.sleep(millis)的多功能的方法，但是它会忽略InterruptedException.如果你不使用Thread.interrupt()的话，可以用它来延时．
+  Handler类可以在一个相对或绝对时间后延时同步回调．Handler实例同样也是使用的uptimeMillis()时钟，且需要一个event loop(通常由GUI应用提供)．
+  AlarmManager可以一次性触发或多次触发事件，即使设备进入深度睡眠或者你的应用并没有在运行．在你选择currentTimeMillis()(RTC)或者elapsedRealtime()(ELAPSED_REALTIME)后,事件将被延迟，且当事件触发时发送一个广播．

<h2>Public methods</h2>

|return-type|method name|
|:-----|:-----|
|static long|currentThreadTimeMillis() 返回当前线程处于运行状态的总时间|
|static long|elapsedRealtime()　返回从系统启动以来的毫秒数，包括休眠时间|
|static long|elapsedRealtimeNano()　返回从系统启动以来的纳秒数，包括休眠时间|
|static boolean|setCurrentTimeMillis(long millis)　设置”wall” clock的时间|
|static void|sleep(long ms)　给定等待的时间|
|static long|updateMillis()　返回系统启动以来的毫秒数，不包括休眠时间|

currentThreadTimeMillis

long currentThreadTimeMillis()

返回的是当前线程处于运行状态的时间．
```java
new Thread(new Runnable() {
    @Override
    public void run() {
        Log.d("syuu", "" + SystemClock.currentThreadTimeMillis()); //0
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        Log.d("syuu", "" + SystemClock.currentThreadTimeMillis()); // still 0
        
        long count = 0;
        for (int j = 0; j < Integer.MAX_VALUE; j++) {
            count++;
        }
        Log.d("syuu", "" + SystemClock.currentThreadTimeMillis()); // 21990(这个只是在我的设备上运行的时间)
        
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        Log.d("syuu", "" + SystemClock.currentThreadTimeMillis());// 21990
    }
}).start();
```