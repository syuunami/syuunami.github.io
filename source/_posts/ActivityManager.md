---
title: ActivityManager
date: 2017-09-28 11:56:32
tags: Android源码分析
---
public class ActivityManager
extends Object
java.lang.Object
↳android.app.ActivityManager
这个类主要负责提供activities,services和包含的process的信息，并且与之交互．
这里面的一些方法是用来调试和提供目的信息的，它们不应该对你的app运行产生影响．这些在method的level文档中都有说明．
大多数应用开发者并不是必须要使用这个类．然而，有一小部分方法被广泛使用．举个例子，isLowRamDevice()可以让你的应用检测是否运行在一个低内存的设备上，并做相应的操作．clearApplicationUserData()可以用来清除应用的用户数据．
在一些特殊的使用场合，当一个app和它的任务栈交互时，这个app也许会使用内部类ActivityManager.AppTask和ActivityManager.RecentTaskInfo.然而，一般情况下，这些方法应该仅用来调试．
获得这个类的实例只能通过调用Context.getSystemService(Class)或者Context.getSystemService(Context.ACTIVITY_SERVICE)

<h1>Summary</h1>

|||
|:-----|:-----|
|class|ActivityManager.AppTask 允许你去管理你自己应用的task|
|class|ActivityManager.MemoryInfo 你可以通过getMemoryInfo(ActivityManager.MemoryInfo)访问内存信息|
|class|ActivityManager.ProcessErrorStateInfo 你可以访问任何在error条件的进程|
|class|ActivityManager.RecentTaskInfo 你可以访问用户最近打开或者访问的task信息|
|class|ActivityManager.RunningAppProcessInfo 你可以访问一个正在运行的进程的信息|
|class|ActivityManager.RunningServiceInfo 你可以访问一个特定的正在系统在运行的Service的信息|
|class|ActivityManager.RunningTaskInfo 你可以访问一个特定的正在系统在运行的Task的信息|
|class|ActivityManager.TaskDescription　你可以设置和访问当前Activity在recent task list里的信息|