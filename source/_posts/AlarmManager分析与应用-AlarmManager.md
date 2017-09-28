---
title: AlarmManager分析与应用----AlarmManager
date: 2017-09-28 11:17:36
tags: Android源码分析
---
<h1>Alarm Manager</h1>
public class AlarmManager extends Object
java.lang.Object
    ↳android.app.AlarmManager
location: /frameworks/base/core/java/android/app/AlarmManager.java

这个类提供了访问系统闹铃服务(Alarm Services)的接口．它使你可以让你的应用在未来的某个时间点运行．当一个闹铃响了，之前注册的intent就会被系统广播出来，自动开始运行当前应用如果它还没有运行的话．即使设备休眠，注册过的alarm仍然会被保存（而且，如果在此期间如果闹铃go off的话可以随意地唤醒设备），但是当它被关闭或者设备重启的话就会被清除掉．

只要alarm receiver的onReceive方法执行，Alarm Manager就会持有一个CPU wake lock.这就保证在你处理完广播之前手机不会休眠．一旦onReceive()方法返回，Alarm Manager就会释放这个wake lock.这意味着一旦你onReceive()方法执行完毕手机就有可能会休眠．如果你的alarm receiver调用了Context.startService(),很有可能service还没有被启动手机就休眠了．为了避免发生这种情况，你的BroadcastReceiver和Service将需要去实现一个独立的唤醒策略(a separate wake lock policy)去确保直到服务可用启动之前手机继续工作．

<h4>注意：Alarm Manager适用于在未来的某个确切时间运行你的应用代码，即使当前这个应用还没有运行．对于一般性的时间操作(ticks,timeouts,etc)使用Handler更容易和直接有效．</h4>

<font size='1'>&nbsp;&nbsp;注意：从API 19(KITKAT)开始，alarm传递就变得不准确了：OS将会对齐alrams，目的是减少唤醒和电量使用．如果你需要准确的alarm传递的话，Android提供了新的api,详情可见下面的setWindow(int, long, long, PendingIntent)和setExact(int, long, PendingIntent).targetSdkVersion API 19之前的应用仍然使用之前的策略，alarms是准确的.</font>

获取这个类的实例必须通过调用Context.getSystemService(AlarmManager.class)或者Context.getSystemService(Context.ALARM_SERVICE)

<hr/>
<h1>Summary</h1>

|type|Nested classes|
|:-----|:-----|
|class|<a href="AlarmClockInfo" target="_blank">AlarmManager.AlarmClockInfo</a> 　　延时”alarm clock”事件的一个不可改动的描述|
|interface|<a href="OnAlarmListener" target="_blank">AlarmManager.OnAlarmListener</a> 　　　Direct-Notification alarms:调用者从alram的设置到alram到期必须一直运行，否则就会接收不到alarm delivery(到期)|

|type|Constants|
|:-----|:-----|
|String|ACTION_NEXT_ALARM_CHANGED Broadcast Action:当getNextAlarmClock()返回值变化时被发送|
|int|ELAPSED_REALTIME SystemClock.elapsedRealtime()时间（开机以来，包括休眠）|
|int|ELAPSED_REALTIME SystemClock.elapsedRealtime()时间（开机以来，包括休眠）|
|int|ELAPSED_REALTIME_WAKEUP SystemClock.elapsedRealtime()时间 (开机以来，包括休眠)，当alarm响了的时候会唤醒设备|
|long|INTERVAL_DAY 设置闹铃，间隔一天|
|long|INTERVAL_FIFTEEN_MINUTES　设置闹铃，间隔15min|
|long|INTERVAL_HALF_DAY 设置闹铃，间隔半天|
|long|INTERVAL_HALF_HOUR 设置闹铃，间隔半小时|
|long|INTERVAL_HOUR　设置闹铃，间隔一小时|
|int|RTC System.currentTimeMillis()时间(wall clock time in UTC)|
|int|RTC_WAKEUP alarm响时会唤醒设备|

|return-type|method name|
|:-----|:-----|
|void|cancel(PendingIntent operation) 移除掉任何匹配intent的广播|
|void|cancel(AlarmManager.OnAlarmListener listener) 移除掉注册了这个listener的延时alarm|
|AlarmManager.AlarmClockInfo|getNextAlarmClock() 获取下一个延时alarm的信息|
|void|set(int type,long triggerAtMillis,PendingIntent operation) 设置一个延时alarm|
|void|set(int type,long triggerAtMillis,String tag,AlarmManager.OnAlarmListener listener,Handler targetHandler)|
|void|setAlarmClock(AlarmManager.AlarmClockInfo info,PendingIntent operation)|
|void|setAndAllowWhileIdle(int type,long triggerAtMillis,PendingIntent operation)|
|void|setExact(int type,long triggerAtMillis,String tag,AlarmManager.OnAlarmListener listener,Handler targetHandler)|
|void|setExactAndAllowWhileIdle(int type,long triggerAtMillis,PendingIntent operation)|
|void|setInexactReapting(int type,long triggerAtMillis,long intervalMillis,PendingIntent operation)|
|void|setRepeating(int type,long triggerAtMillis,long intervalMillis,PendingIntent operation)|
|void|setTime(long millis)|
|void|setTimeZone(String timeZone)|
|void|setWindow(int type,long windowStartMillis,long windowLengthMillis,PendingIntent operation)|
|void|setWindow(int type,long windowStartMillis,long windowLengthMillis,String tag,AlarmManager.OnAlarmListener listener,Handler targetHandler)|

<h3>Constants</h3>
ACTION_NEXT_ALARM_CLOCK_CHANGED
String ACTION_NEXT_ALARM_CLOCK_CHANGED
广播Action:当getNextAlarmClock()返回值改变的时候被发送
这是一个protected的intent,只有系统能发送．
Constant Value: “android.app.action.ACTION_NEXT_ALARM_CLOCK_CHANGED”

ELAPSED_REALTIME
int ELAPSED_REALTIME
SystemClock.elapsedRealtime()时钟(开机以来，包括休眠).不会唤醒设备，如果设备休眠时时间到了，知道下次设备唤醒才会被传递．
Constant Value:3(0x00000003)

ELAPSED_REAKTIME_WAKEUP
int ELAPSED_REAKTIME_WAKEUP
SystemClock.elapsedRealtime()时钟(开机以来，包括休眠)，时间到了就会唤醒设备
Constant Value:2(0x00000002)

INTERVAL_DAY
long INTERVAL_DAY
Android API 19以前的一种不准确的循环间隔，可用于setInexactRepeating(int,long,long,PendingIntent)
Constant Value: 86400000 (0x0000000005265c00)

INTERVAL_FIFTEEN_MINUTES
long INTERVAL_FIFTEEN_MINUTES
Android API 19以前的一种不准确的循环间隔，可用于setInexactRepeating(int,long,long,PendingIntent)
Constant Value: 900000 (0x00000000000dbba0)

INTERVAL_HALF_DAY
long INTERVAL_HALF_DAY
Android API 19以前的一种不准确的循环间隔，可用于setInexactRepeating(int,long,long,PendingIntent)
Constant Value: 43200000 (0x0000000002932e00)

INTERVAL_HALF_HOUR
long INTERVAL_HALF_HOUR
Android API 19以前的一种不准确的循环间隔，可用于setInexactRepeating(int,long,long,PendingIntent)
Constant Value: 1800000 (0x00000000001b7740)

INTERVAL_HOUR
long INTERVAL_HOUR
Android API 19以前的一种不准确的循环间隔，可用于setInexactRepeating(int,long,long,PendingIntent)
Constant Value: 3600000 (0x000000000036ee80)

RTC
int RTC
System.currentTimeMillis返回的闹铃时间(wall clock in UTC)．如果设备睡眠过程中闹铃响了，不会唤醒设备，直到下次设备被唤醒时才会被传递
Constant Value: 1 (0x00000001)

RTC_WAKEUP
int　RTC_WAKEUP
System.currentTimeMillis返回的闹铃时间(wall clock in UTC)．会唤醒设备
Constant Value: 0 (0x00000000)

<h3>Public methods</h3>
cancel
void cancel(PendingIntent operation)
移除所有Intent匹配的alarm.任何alarm,任何类型，只要Intent匹配(filterEquals(Intent))就会被取消

cancel
void cancel(AlarmManager.OnAlarmListener listener)
移除任何注册了该listener的alarm

getNextAlarmClock
AlarmManager.AlarmClockInfo getNextAlarmClock()
获取下一个延时alarm的信息．当应用调用setAlarm(AlarmManager.AlarmClockInfo,PendingIntent)方法时会put一个alarm信息．

set
void set(int type,long triggerAtMillis,PendingIntent operation)
设置一个延时alarm.注意：对于时间操作(比如ticks,timeouts等)使用Handler更容易，更直接有效．
如果设置的触发时间是过去，则这个时钟立刻被触发，如果已经有一个相同Intent的延时alarm了(filterEquals(Intent)方法比较)，则之前的将会被移除，用它来替换
alarm是一个广播，可以被静态或者动态注册的BroadcastReceiver接收到．
alarm的Intent在被传递的时候携带了一个int类型的数据Intent.EXTRA_ALARM_COUNT用于指示当设备处于休眠状态时,ELAPSED_REALTIME和RTC类型的alarm无法被激活，无法发送Intent,被累计，普通状态下为1.
注意：从API19以后，通过这个方法设置的触发时间将不再准确，可能会延期，系统会使用这个策略去批量处理整个系统里的alarm，以减少唤醒设备的次数，优化功耗．一般来说，在不久的未来触发的alarm不会被延期．
如果一个应用设置了很多alarm，很有可能这些alarm无法按照期望的顺序触发．如果你的应用必须要按顺序触发的话．你可以使用setWindow(int,long,long,PendingIntent)和setExact(int,long,PendingIntent)
targetSdkVersion 19以前的应用还是使用之前那种准时的延时alarm.

|Parameters|Description|
|:-----|:-----|
|type|int:alarm类型，RTC_WAKEUP/RTC/ELAPSED_REALTIME_WAKEUP/ELAPSED_REALTIME|
|triggerAtMillis|long:设置延迟毫秒数，使用恰当的时钟(取决于alarm类型)|
|operation|PendingIntent:alarm响时要执行的动作．一般来自IntentSender.getBroadcast()|

set
void set(int type,long triggerAtMillis,String tag,AlarmManager.OnAlarmListener listener,Handler targetHandler)
set(int,long,PendingIntent)的直接回调版本，这个方法支持在alarm到期时回到AlarmManager.OnAlarmListener接口．
OnAlarmListener的onAlarm()方法将被当前指定的Handler或者当targetHandler参数传递的是null时，由应用的main looper调用．

|Parameters|Description|
|:-----|:-----|
|type|int:alarm类型，RTC_WAKEUP/RTC/ELAPSED_REALTIME_WAKEUP/ELAPSED_REALTIME|
|triggerAtMillis|long:设置延迟毫秒数，使用恰当的时钟(取决于alarm类型)|
|tag|String:alarm的字符串描述，用于打印log和battery-use 属性|
|listener|AlarmManager.OnAlarmListener:AlarmManager.OnAlarmListener实例，当alarm到期后onAlarm()方法就会被回调．一个OnAlarmListener实例只能用于一个alarm,就像一个PendingIntent只能用于一个alarm一样|
|targetHandler|Handler:执行listener的onAlarm()回调的Handler,当传递为null时由main looper调用|

setAlarmClock
void setAlarmClock(AlarmManager.AlarmClockInfo info, PendingIntent operation)
设置一个延时alarm.系统将可能显示信息给用户．这个方法很像setExact(int,long,PendingIntent)，但是默认使用RTC_WAKEUP类型.(请注意AlarmClockInfo的构造函数也需要一个PendingIntent,从StackOverFlow上看到有人说是Notification显示一个图标，点击触发此PendingIntent的内容，但是我没有触发成功．也许和API level有关？)

setAndAllowWhileIdle
void setAndAllowWhileIdle(int type,long triggerAtMillis,PendingIntent operation)
类似set(int,long,PendingIntent),但是这个alarm即使在系统处于low-power idle模式仍然可以执行．这种alarm必须只能在确实需要在idle时响的情况下使用．一个合理的例子是，一个calendar notification需要一个铃声去提醒用户．当这个闹铃响了，此app也会被添加到系统的临时白名单里大约十秒钟，以便于允许这个应用可以唤醒设备完成任务．

这个alarm会显著地提高idle时的功耗，所以使用时需要谨慎．为了减少滥用，对于特定的应用会有alarm响多频繁的限制．如果是普通的系统操作，每分钟只能响一次，如果是处于low-power idle模式，这个周期更长，15分钟．

不像其他alarm,这种alarm不会被系统延迟，在设备处于idle状态也会准时触发．当然不出与idle的时候也会被触发．

不管当前应用的SDK version是什么．this call always allows batching of the alarm.

|Parameters|Description|
|:-----|:-----|
|type|int:alarm类型，RTC_WAKEUP/RTC/ELAPSED_REALTIME_WAKEUP/ELAPSED_REALTIME|
|triggerAtMillis|long:设置延迟毫秒数，使用恰当的时钟(取决于alarm类型)|
|operation|PendingIntent:alarm响时要执行的动作．一般来自IntentSender.getBroadcast()|

setExact
void setExact(int type,long triggerAtMillis,PendingIntent operation)
准时触发的延时alarm

这个方法很像set(int,long,PendingIntent),但是不允许系统去调整触发时间．这个alarm尽可能在设置的时间触发．
注意：只有在应用有很强的准时触发的需求（比如一个定时闹铃）的时候才应该使用它，强烈不推荐在非必要的时候使用以减低系统功耗．

|Parameters|Description|
|:-----|:-----|
|type|int:alarm类型，RTC_WAKEUP/RTC/ELAPSED_REALTIME_WAKEUP/ELAPSED_REALTIME|
|triggerAtMillis|long:设置延迟毫秒数，使用恰当的时钟(取决于alarm类型)|
|operation|PendingIntent:alarm响时要执行的动作．一般来自IntentSender.getBroadcast()|

setExact
void setExact(int type,long triggerAtMillis,String tag,AlarmManager.OnAlarmListener listener,Handler targetHandler)
setExact(int,long,PendingIntent)的直接回调版本，这个方法支持在alarm到期时回到AlarmManager.OnAlarmListener接口．
OnAlarmListener的onAlarm()方法将被当前指定的Handler或者当targetHandler参数传递的是null时，由应用的main looper调用．

|Parameters|Description|
|:-----|:-----|
|type|int:alarm类型，RTC_WAKEUP/RTC/ELAPSED_REALTIME_WAKEUP/ELAPSED_REALTIME|
|triggerAtMillis|long:设置延迟毫秒数，使用恰当的时钟(取决于alarm类型)|
|tag|String:alarm的字符串描述，用于打印log和battery-use 属性|
|listener|AlarmManager.OnAlarmListener:AlarmManager.OnAlarmListener实例，当alarm到期后onAlarm()方法就会被回调．一个OnAlarmListener实例只能用于一个alarm,就像一个PendingIntent只能用于一个alarm一样|
|targetHandler|Handler:执行listener的onAlarm()回调的Handler,当传递为null时由main looper调用|

setExactAndAllowWhileIdle
void setExactAndAllowWhileIdle(int type,long triggerAtMillis,PendingIntent operation)
很像setExact(int,long,PendingIntent),但是允许在设备处于low-power idle的时候触发，其他的和setAndAllowWhileIdle一样．

setInexactRepeating
void setInexactRepeating(int type,long triggerAtMillis,long intervalMillis,PendingIntent operation)
延时一个不精确的重复alarm．这个alarm比传统中使用setRepeating(int,long,long,PendingIntent)能降低功耗．
这个alarm第一次触发不会早于设置的时间，但是自这以后就不能保证了．重复alarm的总时间会按照标准来．两次触发的时间间隔也许会有变化．如果你的应用需求的抖动很低，推荐用一次性alarm代替，见setWindow(int,long,long,PendingIntent)和setExact(int,long,PendingIntent)

从API19以后，所有的重复alarm都是不精确的，但是这个方法从API3时就可用了，所以你可以安全地在当前或者更老的版本中调用它．

|Parameters|Description|
|:-----|:-----|
|type|int:alarm类型，RTC_WAKEUP/RTC/ELAPSED_REALTIME_WAKEUP/ELAPSED_REALTIME|
|triggerAtMillis|long:设置延迟毫秒数，使用恰当的时钟(取决于alarm类型)|
|intercalMillis|long:之后重复闹铃的间隔毫秒数，在API19之前，如果值是INTERVAL_FIFTEEN_MINUTES, INTERVAL_HALF_HOUR, INTERVAL_HOUR, INTERVAL_HALF_DAY, or INTERVAL_DAY，alarm将会被相对对齐以减少唤醒次数．否则，这个alarm就会被设置的跟应用已经调用过setRepeating(int,long,long,PendingIntent)一样．从API19以后，所有的重复alarm都是不精确的．将根据他们的重复间隔做批量处理．|
|operation|PendingIntent:alarm响时要执行的动作．一般来自IntentSender.getBroadcast()|

setRepeating
void setRepeating(int type,long triggerAtMillis,long intervalMillis,PendingIntent operation)
延时重复alarm.注意：对于定时操作(ticks,timeouts,等等)使用Handler更简单有效．如果已经存在一个具有相同IntentSender的alarm,第一个将会被取消．
很像set(int,long,PendingIntent)
如果一个alarm被延时了(被系统休眠，比如说non_WAKEUP类型)，跳过去的重复alarm将会被尽快触发．从那以后的alarm,将会和设置的一样触发，他们不会飘移．举个例子，加入你设置了一个每个钟头重复触发的广播，但是你的设备在7:45-8:45睡眠了，那么这中间的alarm会尽快被触发，然后下一个alarm将在9:00被触发．
如果你想保证每次触发的时间间隔一致，你应用使用一次性的精确alarm.每次一个alarm触发，就自己设置下一个延时alarm.
注意：从API19开始，所有的重复alarm都是不准确的，如果你的应用需要一个精确间隔的重复alarm,必须使用一次性精确alarm.targetSdkVerion19之前的应用还是精确的．

|Parameters|Description|
|:-----|:-----|
|type|int:alarm类型，RTC_WAKEUP/RTC/ELAPSED_REALTIME_WAKEUP/ELAPSED_REALTIME|
|triggerAtMillis|long:设置延迟毫秒数，使用恰当的时钟(取决于alarm类型)|
|intercalMillis|long:之后重复闹铃的间隔毫秒数|
|operation|PendingIntent:alarm响时要执行的动作．一般来自IntentSender.getBroadcast()|

setTime
void setTime(long millis)
设置系统wall clock时间，需要android.permission.SET_TIME

|Parameters|Description|
|:-----|:-----|
|millis|long:从1970-1-1 0时以来的毫秒数|

setTimeZone
void setTimeZone(String timeZone)
设置系统默认时区，这个时区对所有应用都是有效的，即使重启．使用setDefault(TimeZone)如果你指向对于当前应用改变时区的话．
在android M和之后的版本．当传递一个非奥尔森时区时将有一个错误．注意，在所有的安卓版本上这都是很糟糕的，因为POSIX(可移植操作系统接口)和TimeZone类对于同一个非奥尔森时区的’+’和’-‘有相反的解读．

|Parameters|Description|
|:-----|:-----|
|timeZone|String:通过TimeZone类的getAvailableIDs()返回的其中一个值|

setWindow
void setWindow(int type,long windowStartMillis,long windowLengthMillis,PendingIntent)
设置一个alarm在给定的时间窗触发．类似于set,该方法允许应用程序精确地控制操作系统调整alarm触发时间的程度．

|Parameters|Description|
|:-----|:-----|
|type|int:alarm类型，RTC_WAKEUP/RTC/ELAPSED_REALTIME_WAKEUP/ELAPSED_REALTIME|
|windowStartMillis|lnog:alarm第一次执行的时间，以毫秒为单位，可以自定义时间，不过一般使用当前时间．需要注意的是,本属性与第一个属性（type）密切相关,如果第一个参数对应的闹钟使用的是相对时间 （ELAPSED_REALTIME和ELAPSED_REALTIME_WAKEUP），那么本属性就得使用相对时间 （相对于系统启动时间来说）,比如当前时间就表示为:SystemClock.elapsedRealtime()； 如果第一个参数对应的闹钟使用的是绝对时间(RTC、RTC_WAKEUP、POWER_OFF_WAKEUP）, 那么本属性就得使用绝对时间，比如当前时间就表示 为：System.currentTimeMillis()。|
|windowLengthMillis|long:两次alarm执行的时间间隔，也是以毫秒为单位|
|operation|PendingIntent:alarm响时要执行的动作．一般来自IntentSender.getBroadcast()|

setWindow
void setWindow(int type,long windowStartMillis,long widowLengthMillis,String tag,AlarmManager.OnAlarmListener listener,Handler targetHandler)
setWindow(int,long,long,PendingIntent)的回调版本．没啥好说的．

<h3>Inner Class</h3>
<h3 id='AlarmClockInfo'>AlarmManager.AlarmClockInfo</h3>
public static final class AlarmManager.AlarmClockInfo
extends Object implements Parcelable
java.lang.Object
    ↳android.app.AlarmManager.AlarmClockInfo
延时”alarm clock”事件的一个不可变描述
可见于:
setAlarmClock(AlarmManager.AlarmClockInfo, PendingIntent)
getNextAlarmClock()

public methods:

|return-type|method name|
|:-----|:-----|
|PendingIntent|getShowIntent() 返回一个可以被用来展示或者编辑延时alarm clock详情的intent,注意其它应用都可以修改和发送这个intent,有其它值添加进来的可能性，详细见PendingIntent.send()或Intent.fillIn()
long	|
|long|getTriggerTime() 返回alarm的触发时间(UTC wall clock time)|
<hr/>
<h3 id='OnAlarmListener'>AlarmManager.OnAlarmListener</h3>
public static interface AlarmManager.OnAlarmListener
android.app.AlarmManager.OnAlarmListener
Direct-notification alarm:调用者从alram的设置到alram到期必须一直运行，否则就会接收不到alarm delivery(到期)消息．只有一次性alarm才可以设置，重复的alarm不行．
public methods:

|return-type|method name|
|:-----|:-----|
|abstract void|onAlarm() 当alarm 响了的时候会被系统回调|
