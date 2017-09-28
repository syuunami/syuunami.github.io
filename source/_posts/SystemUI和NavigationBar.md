---
title: SystemUI和NavigationBar
date: 2017-09-28 10:32:24
tags: Android源码分析
---
前人栽树，后人乘凉:
<a href="http://www.jianshu.com/p/0ab1279465fa" target="_blank">1.SystemUI启动流程及主体布局介绍</a>
<a href="https://guolei1130.github.io/2017/01/07/SystemServer%E8%BF%9B%E7%A8%8B%E7%9A%84%E5%88%9D%E5%A7%8B%E5%8C%96/" target="_blank">2.SystemServer进程的初始化</a>
<h3>启动</h3><hr>
我相信作为一个Android程序员，大家一定对Context的getSystemService方法无比熟悉了，那么大家知道调用这个法的时候，系统都做了哪些工作吗？别急，我们先聊聊Android系统的启动流程.

Android启动后，init.rc将启动Zygote,Zygote再启动SystemServer.我们来看下SystemServer:(此处说明下，本篇博客基本是本人自己的一个读书笔记，所以可能有些地方跟标题内容不太吻合，有些地方过于深入，以后会慢慢整理的)

很久没有在Android代码中看到main函数啦．它会调用SystemServer类自身的run()方法．run方法中比较关键性的代码如下:

```java
//frameworks/base/services/java/com/android/server/SystemServer.java
startBootstrapServices();
startCoreServices();
startOtherServices();

```
startBootstrapServices():等待intalld进程去结束start up,以便让它以恰当的权限去创建关键性的目录，比如/data，/user等．在初始化其它Service之前，我们需要让它执行完.