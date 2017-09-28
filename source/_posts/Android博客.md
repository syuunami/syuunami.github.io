---
title: Android博客
date: 2017-09-28 10:58:59
tags: App开发
---
<ul>
<li><a href="https://jaeger.itscoder.com/android/2016/02/15/status-bar-demo.html" target="_blank">Android App 沉浸式状态栏解决方案</a></li>
<li><a href="http://www.cnblogs.com/zhaoyanjun/p/6016341.html" target="_blank">Android Butterknife 8.4.0 使用方法总结</a></li>
<li><a href="http://blog.csdn.net/duanyy1990/article/details/51658332" target="_blank">NavigationView更改菜单icon和title颜色变化效果</a></li>
<li><a href="http://blog.csdn.net/github_2011/article/details/72195254" target="_blank">自定义Android Toast</a></li>
<li><a href="http://www.cnblogs.com/xilinch/p/4444833.html" target="_blank">TypedValue.applyDimension 中dp和sp之间转化的真相</a></li>
<li><a href="http://www.cnblogs.com/kissazi2/p/4049982.html" target="_blank">为什么需要在TypedArray后调用recycle</a></li>
<li><a href="http://blog.csdn.net/jijiaxin1989/article/details/42237401" target="_blank">setWillNotDraw（）;方法的使用</a></li>
<li><a href="Android aidl 编译报couldn’t find import for class" target="_blank">Activity之taskAffinity属性、allowTaskReparenting属性和Android退出整个应用解决方案</a></li>
<li><a href="http://www.cnblogs.com/Amandaliu/archive/2011/07/05/2098026.html" target="_blank">AudioManager音量控制</a></li>
<li><a href="https://book.2cto.com/201508/54842.html" target="_blank">通用的音量设置函数setStreamVolume</a></li>
<li><a href="http://blog.csdn.net/haoel/article/details/2890" target="_blank">跟我一起写 Makefile（五）</a></li><a href="#note1" target="_blank">note</a>
<li><a href="http://blog.csdn.net/zhengzhb/article/details/7281833" target="_blank">设计模式六大原则（2）：里氏替换原则</a></li><a href="#note2" target="_blank">note</a>
<li><a href="http://blog.csdn.net/error/404.html?from=http%3a%2f%2fblog.csdn.net%2fsunsonfly%2farticle%2fdetails%2f13502993" target="_blank">make snod 命令机制解析</a></li>
<li><a href="http://www.jianshu.com/p/d507e3514b65" target="_blank">教你步步为营掌握自定义View</a></li>
<li><a href="http://blog.csdn.net/tingfengzheshuo/article/details/51706174" target="_blank">canvas的save，restore方法的使用理解</a></li>
<li><a href="http://blog.sina.com.cn/s/blog_726322c80101c0r9.html" target="_blank">使用setDrawingCacheEnabled(boolean flag)提高绘图速度</a></li>
<li><a href="http://www.cnblogs.com/xilinch/p/4444833.html" target="_blank">TypedValue.applyDimension 中dp和sp之间转化的真相</a></li>
<li><a href="http://blog.csdn.net/kehui123/article/details/5298337" target="_blank">switch与ifelse的效率问题</a></li><a href="#note3" target="_blank">note</a>
<li><a href="http://blog.csdn.net/u011433995/article/details/50475131" target="_blank">Canvas的saveLayer理解</a></li>
<li><a href="http://www.jianshu.com/p/e689d0196a17" target="_blank">超级简单的Android Studio jni 实现(无需命令行)</a></li>
<li><a href="http://blog.csdn.net/hudashi/article/details/7050897" target="_blank">Android中如何查看内存</a></li>
<li><a href="http://www.cnblogs.com/android-blogs/p/5778997.html" target="_blank">Android为什么方法数不能超过65535</a></li>
<li><a href="http://www.tuicool.com/articles/EvEZzqe" target="_blank">PermissionDemo：Android M (6.0) 权限申请研究</a></li>
<li><a href="http://man.linuxde.net/htpasswd" target="_blank">htpasswd命令</a></li>
<li><a href="http://www.cnblogs.com/xudong-bupt/p/6079849.html" target="_blank">linux shell 多线程执行程序</a></li>
<li><a href="https://imsun.net/posts/gitment-introduction/" target="_blank">Gitment：使用 GitHub Issues 搭建评论系统</a></li>
</ul>

<hr>
<h3>读书笔记</h3>
<div id='note1'>
note1
```java
define getdef
	$(subst hello.o: ,, $(shell cc -MM $1))
endef
#main:hello.c hello.h
#替换为
main:$(call getdef, hello.c)
	@cc -o main hello.c
.PHONY:clean
clean:
	@if [ -e main ];then rm main;fi
```
</div>
<div id='note2'>
note2
里氏替换原则有四层定义:
子类可以实现父类的抽象方法，但不能覆盖父类的非抽象方法。
子类中可以增加自己特有的方法。
当子类的方法重载父类的方法时，方法的前置条件（即方法的形参）要比父类方法的输入参数更宽松。
当子类的方法实现父类的抽象方法时，方法的后置条件（即方法的返回值）要比父类更严格。
关于cd,总有些不太理解.在后续研究Android源码的时候可以多看看Google是如何看待这个问题的
</div>
<div id='note3'>
note3
首先大家去看看一本书《C++ Footprint and Performance Optimization》，里面的7章，第一节。

然后根据大量的实际程序测试（不考虑不同的编译器优化程度差异，假设都是最好的优化），那么Switch语句击中第三个选项的时间跟if/else if语句击中第三个选项的时间相同。
击中第一，第二选项的速度if语句快，击中第四以及第四之后的选项的速度switch语句快。

所以，如果所有选项出现概率相同的话，结论就是：5个选项（包括default）的情况下，switch和if/else if相同。低于5个选项if快，高于5给选项switch快！
</div>