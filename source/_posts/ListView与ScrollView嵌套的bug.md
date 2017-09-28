---
title: ListView与ScrollView嵌套的bug
date: 2017-09-28 11:15:04
tags: App开发
---
今天在学习<<Android开发艺术探索>>中关于滑动冲突的时候,作者提到可以用ScrollView和ListView做演示.
我写demo的时候发现,在ScrollView中嵌套的ListView只会显示一个item.
![1](https://raw.githubusercontent.com/syuunami/BlogPictures/master/screenshot_2017062204.png)
从网上找了个方法,重写ListView的onMeasure方法.
```java
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        int expandSpec = MeasureSpec.makeMeasureSpec(Integer.MAX_VALUE>>2, MeasureSpec.AT_MOST);
        super.onMeasure(widthMeasureSpec,expandSpec);
}
```
makeMeaseureSpec的两个参数分别代表父布局提供的参考大小和规格.
Integer.MAX_VALUE>>2,这是因为int 是32位，其中最高2位是模式，后30位是尺寸,表示边界无限大.
上述代码效果如下:
![2](https://raw.githubusercontent.com/syuunami/BlogPictures/master/screenshot2017062202.png)
如果要指定ListView的高,可以这么写
```java
protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        int expandSpec = MeasureSpec.makeMeasureSpec(高度, MeasureSpec.EXACTLY);
        super.onMeasure(widthMeasureSpec,expandSpec);
}
```
当然,这么写代码复用性很差,明天研究下获取xml中指定的值和解决滑动冲突