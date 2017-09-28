
---
title: AlarmManager分析与应用-AlarmManager和源码分析
date: 2017-09-28 11:51:22
tags: Android源码分析
---
/frameworks/base/core/java/android/app/AlarmManager.java
看一个类，首先当然是从它的构造器开始往下看．AlarmManager的构造器很简单
```java
AlarmManager(IAlarmManager service, Context ctx) {
    mService = service;
    mPackageName = ctx.getPackageName();//获取调用者的包名
    mTargetSdkVersion = ctx.getApplicationInfo().targetSdkVersion;//获取应用的targetSdkVersion
    mAlwaysExact = (mTargetSdkVersion < Build.VERSION_CODES.KITKAT);//对于API19之前的应用，alarm都是精确的
    mMainThreadHandler = new Handler(ctx.getMainLooper());//获取应用的MainLooper，这个之前看过api的都知道是为了OnAlarmListener做准备
}
```
还有个问题，mService是什么东西呢？
关于SystemServer,AlarmManager和AlarmManagerServer这三者的关系，可以看看我的另外一篇博文．这里就简述一下过程了．系统启动时会调用initrc进程，启动SystemServer.在SystemServer中启动了AlarmManagerService.当开发者调用Context.getSystemService(AlarmManager.class)的时候，会在SystemServiceRegistry中会去new一个AlarmManager:
SystemServiceRegistry中会去new一个AlarmManager:
/frameworks/base/core/java/android/app/SystemServiceRegistry.java

```java
public AlarmManager createService(ContextImpl ctx) {
    IBinder b = ServiceManager.getService(Context.ALARM_SERVICE);
    IAlarmManager service = IAlarmManager.Stub.asInterface(b);
    return new AlarmManager(service, ctx);
}});
```
学习过AIDL模型的同学现在应该能看出来mService其实就是AlarmManagerService里的一个IBinder对象了．先不管AMS里到底做了什么，我们继续往下看AlarmManager的其他方法．
首先是:
```java
private long legacyExactLength() {
    return (mAlwaysExact ? WINDOW_EXACT : WINDOW_HEURISTIC);
}
```
这个很简单，根据mAlwaysExact，其实也就是调用者的API level去判断，是返回WINDOW_EXACT(0)还是WINDOW_HEURISTIC(-1)
接着往下拖动．我们发现啊，以下几个方法的原理都是一样的:

```java
public void set(int type, long triggerAtMillis, PendingIntent operation)
public void set(int type, long triggerAtMillis, String tag, OnAlarmListener listener,
            Handler targetHandler)
public void setRepeating(int type, long triggerAtMillis,
            long intervalMillis, PendingIntent operation)
public void setWindow(int type, long windowStartMillis, long windowLengthMillis,
            PendingIntent operation)
public void setWindow(int type, long windowStartMillis, long windowLengthMillis,
            String tag, OnAlarmListener listener, Handler targetHandler)
public void setExact(int type, long triggerAtMillis, PendingIntent operation)
public void setExact(int type, long triggerAtMillis, String tag, OnAlarmListener listener,
            Handler targetHandler)
public void setIdleUntil(int type, long triggerAtMillis, String tag, OnAlarmListener listener,
            Handler targetHandler)
public void setAlarmClock(AlarmClockInfo info, PendingIntent operation)
public void set(int type, long triggerAtMillis, long windowMillis, long intervalMillis,
            PendingIntent operation, WorkSource workSource)
public void set(int type, long triggerAtMillis, long windowMillis, long intervalMillis,
            String tag, OnAlarmListener listener, Handler targetHandler, WorkSource workSource)
public void set(int type, long triggerAtMillis, long windowMillis, long intervalMillis,
            OnAlarmListener listener, Handler targetHandler, WorkSource workSource)
public void setInexactRepeating(int type, long triggerAtMillis,
            long intervalMillis, PendingIntent operation)
public void setAndAllowWhileIdle(int type, long triggerAtMillis, PendingIntent operation)
public void setExactAndAllowWhileIdle(int type, long triggerAtMillis, PendingIntent operation)
```
这些方法都是调用了私有成员方法setImpl,只是传入的参数不同罢了．
```java
private void setImpl(int type, long triggerAtMillis, long windowMillis, long intervalMillis,
            int flags, PendingIntent operation, final OnAlarmListener listener, String listenerTag,
            Handler targetHandler, WorkSource workSource, AlarmClockInfo alarmClock)
```
怎么样，是不是瞬间松了一口气？
那么我们就来看看setImpl都做了哪些工作．

```java
private void setImpl(int type, long triggerAtMillis, long windowMillis, long intervalMillis,
        int flags, PendingIntent operation, final OnAlarmListener listener, String listenerTag,
        Handler targetHandler, WorkSource workSource, AlarmClockInfo alarmClock) {
    //对于triggerAtMillis < 0的设置为0
    if (triggerAtMillis < 0) {
        /* NOTYET
        if (mAlwaysExact) {
            // Fatal error for KLP+ apps to use negative trigger times
            throw new IllegalArgumentException("Invalid alarm trigger time "
                    + triggerAtMillis);
        }
        */
        triggerAtMillis = 0;
    }
    //ListenerWrapper初始化
    ListenerWrapper recipientWrapper = null;
    if (listener != null) {
        synchronized (AlarmManager.class) {
            if (sWrappers == null) {
                sWrappers = new ArrayMap<OnAlarmListener, ListenerWrapper>();
            }
            recipientWrapper = sWrappers.get(listener);
            // no existing wrapper => build a new one
            if (recipientWrapper == null) {
                recipientWrapper = new ListenerWrapper(listener);
                sWrappers.put(listener, recipientWrapper);
            }
        }
        final Handler handler = (targetHandler != null) ? targetHandler : mMainThreadHandler;
        recipientWrapper.setHandler(handler);
    }
    //调用AMS中的IAlarmManager的set方法
    try {
        mService.set(mPackageName, type, triggerAtMillis, windowMillis, intervalMillis, flags,
                operation, recipientWrapper, listenerTag, workSource, alarmClock);
    } catch (RemoteException ex) {
        throw ex.rethrowFromSystemServer();
    }
}
```
ListenerWrapper是AlarmManager中的一个内部类，是OnAlarmListener的包装器．暂时先不管它，我们看看ALMS(AlarmManagerService)中IAlarmManager的set方法都做了啥:
/frameworks/base/services/core/java/com/android/server/AlarmManagerService.java
```java
private final IBinder mService = new IAlarmManager.Stub() {
        @Override
        public void set(String callingPackage,
                int type, long triggerAtTime, long windowLength, long interval, int flags,
                PendingIntent operation, IAlarmListener directReceiver, String listenerTag,
                WorkSource workSource, AlarmManager.AlarmClockInfo alarmClock) {
            final int callingUid = Binder.getCallingUid();
            // make sure the caller is not lying about which package should be blamed for
            // wakelock time spent in alarm delivery
            mAppOps.checkPackage(callingUid, callingPackage);
            // 重复的alarm必须使用PendingIntent,不能使用OnAlarmListener
            if (interval != 0) {
                if (directReceiver != null) {
                    throw new IllegalArgumentException("Repeating alarms cannot use AlarmReceivers");
                }
            }
            //如果workSource不为null,检查是否有UPDATE_DEVICE_STATS权限
            if (workSource != null) {
                getContext().enforcePermission(
                        android.Manifest.permission.UPDATE_DEVICE_STATS,
                        Binder.getCallingPid(), callingUid, "AlarmManager.set");
            }
            // 先不处理WAKE_FROM_IDLE或者ALLOW_WHILE_IDLE_UNRESTRICTED的alarm,稍后恰当时候会处理．
            flags &= ~(AlarmManager.FLAG_WAKE_FROM_IDLE
                    | AlarmManager.FLAG_ALLOW_WHILE_IDLE_UNRESTRICTED);
            //只有系统可以使用FLAG_IDLE_UNTIL--它用来告诉AlarmManager什么时候退出IDLE模式，只会被DeviceIdleController使用．
            if (callingUid != Process.SYSTEM_UID) {
                flags &= ~AlarmManager.FLAG_IDLE_UNTIL;
            }
            // 如果是精确alarm,则它不可以和其他alarm批量处理
            if (windowLength == AlarmManager.WINDOW_EXACT) {
                flags |= AlarmManager.FLAG_STANDALONE;
            }
            //如果alarm是一个闹铃，那它必须独立而且如果需要的话，会提前从IDLE中唤醒．
            if (alarmClock != null) {
                flags |= AlarmManager.FLAG_WAKE_FROM_IDLE | AlarmManager.FLAG_STANDALONE;
            // If the caller is a core system component or on the user's whitelist, and not calling
            // to do work on behalf of someone else, then always set ALLOW_WHILE_IDLE_UNRESTRICTED.
            // This means we will allow these alarms to go off as normal even while idle, with no
            // timing restrictions.
            } else if (workSource == null && (callingUid < Process.FIRST_APPLICATION_UID
                    || Arrays.binarySearch(mDeviceIdleUserWhitelist,
                            UserHandle.getAppId(callingUid)) >= 0)) {
                flags |= AlarmManager.FLAG_ALLOW_WHILE_IDLE_UNRESTRICTED;
                flags &= ~AlarmManager.FLAG_ALLOW_WHILE_IDLE;
            }
            setImpl(type, triggerAtTime, windowLength, interval, operation, directReceiver,
                    listenerTag, flags, workSource, alarmClock, callingUid, callingPackage);
        }
```