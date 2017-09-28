---
title: (转)bootanimation分析
date: 2017-09-28 10:51:01
tags: Rom开发
---
[转载自Android深度探索卷2]
bootanimation.zip文件中会有一个desc.txt文件和若干个类似part0,part1的目录.其中desc.txt文件是必须的.而part0,part1这样的目录至少要有一个,多者不限.Android系统读取part0,part1中的静态图像,并按照一定的显示规律和频率产生动画效果,而描述这些信息的就是desc.txt.
desc.txt规则:
[width] [height] [frame-rate]
p [loop] [pause] [folder]
p [loop] [pause] [folder]

width:动画播放的宽度
height:动画播放的高度
frame-rate:每秒播放的帧数.尽管该值没有要求具体的范围,但最好不要超过30,否则Android设备可能由于耗费大量的CPU资源处理开机动画造成开机缓慢,
loop:当前part动画循环次数.
pause:当前part动画播放完后暂停的帧数.
folder:当前part动画对应的静态图像文件存储的目录名称.
对于width和height要注意的是如果width和height大于当前Android设备的分辨率,则以当前Android设备的分辨率为准,也就是说动画会尽量充满整个屏幕