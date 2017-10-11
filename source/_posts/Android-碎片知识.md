---
title: Android 碎片知识
date: 2017-09-28 10:52:46
tags: App开发
---
1.
```java
//在被判定为滚动之前用户手指可以移动的最大值
ViewConfiguration.get(context).getScaledTouchSlop();

```
2.DatePicker有两种样式:spinner和clock
```java
<TimePicker
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:timePickerMode="spinner"/>
```
![1](https://raw.githubusercontent.com/syuunami/BlogPictures/master/timepick_spinner.png)

```java
<TimePicker
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:timePickerMode="clock"/>
```
![2](https://raw.githubusercontent.com/syuunami/BlogPictures/master/timepicker_clock.png)

3.关于AndroidBar中的android:showAsAction属性
在androidstudio中使用是会报错的．
现象：Should use app:showAsAction with the appcompat library with xmlns:app=”http://schemas.android.com/apk/res-auto“
解决方案:在menu标签加上xmlns:app=”http://schemas.android.com/apk/res-auto",再用 app:showAsAction=”always”替换就可以了

4.关于Parcelable和Serializable
Parcelable对象如果使用ObjectOutputStream或者ObjectInputStream会报NotSerializableException．如果Serializable对象不使用out.writeObject(obj)直接使用in.readObject()的话会报EOFException,另外transient对Parcelable无效

5.IllegalArgumentException: Service Intent must be explicit
这是因为Android5.0中service的intent一定要显式声明，这样绑定的时候不会出错.
解决方案:
```java
public Intent createExplicitFromImplicitIntent(Context context, Intent implicitIntent){
    PackageManager packageManager = context.getPackageManager();
    List<ResolveInfo> resolveInfo = packageManager.queryIntentServices(implicitIntent);
    if(resolveInfo == null || resolveInfo.size() != 1){
        return null;
    }
    ResolveInfo info = resolveInfo.get(0);
    String packageName = info.serviceInfo.packageName;
    String className = info.serviceInfo.name;
    ComponentName component = new ComponentName(packageName, className);
    Intent explicitIntent = new Intent(implicitIntent);
    explicitIntent.setComponent(component);
    return explicitIntent;
}
```
6.设置android:process=”:xxx”的组件无法打印Log?请将AS中”Show Only Selected Application”改为”No Filters”

7.ListView里的List不能重新设置个新的，否则会导致adapter的notifyDataChanged无效。