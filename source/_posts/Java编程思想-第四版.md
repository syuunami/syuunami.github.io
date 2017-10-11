---
title: Java编程思想(第四版)
date: 2017-10-09 09:34:20
tags: 读书笔记
---
<h3>第1章  对象导论</h3>

+ OOP的五个特征:
&nbsp;&nbsp;&nbsp;&nbsp;1）万物皆对象。
&nbsp;&nbsp;&nbsp;&nbsp;2) 程序是对象的集合，它们通过发送信息来告知彼此所要做的。
&nbsp;&nbsp;&nbsp;&nbsp;3) 每个对象都有自己的由其它对象所构成的存储。
&nbsp;&nbsp;&nbsp;&nbsp;4) 每个对象都有类型。
&nbsp;&nbsp;&nbsp;&nbsp;5) 某一特定类型的所有对象都可以接收同样的信息。
    
+ Java使用的是动态内存分配，在堆中动态地创建对象。因为存储时间实在运行时被动管理的，所以需要大量的时间在堆中分配存储空间，这可能要远远大于在堆栈中创建存储空间的时间。new出来的对象都是存储在堆中的，基本数据类型变量和对象的引用放在栈中。静态域存放静态成员，常量池存放字符串常量和基本类型常量。


<h3>第2章  一切都是对象</h3>

|基本类型|大小|最小值|最大值|包装器类型|
|:-----|:-----|:-----|:-----|:-----|
|boolean|-|-|-|Boolean|
|char|16bit|Unicode 0|Unicode 2^16 -1|Character|
|byte|8bit|-128|127|Byte|
|short|16bit|-2^15|2^15-1|Short|
|int|32bit|-2^31|2^31-1|Integer|
|long|64bit|-2^63|2^63-1|Long|
|float|32bit|IEEE754|IEEE754|Float|
|double|64bit|IEEE754|IEEE754|Double|
|void|-|-|-|Void|



<h3>第3章  操作符</h3>
