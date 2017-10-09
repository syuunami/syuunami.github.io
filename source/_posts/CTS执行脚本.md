---
title: CTS执行脚本
date: 2017-10-09 09:04:29
tags: 脚本
---

```java
#!/bin/sh

#可以用来减少工作量,请放在android-cts-media的根目录下
echo "start copy media..."
for id in $@
do
{
	echo "copy media to $id"
        source copy_media.sh all -s $id
} &
done
wait
echo "copy all success!!!!!!"

echo "start test cts..."
devices=""
shardCount=0

for deviceid in $@
do
	devices+=" -s $deviceid "
	let "shardCount+=1"
done

#你自己的cts目录
cd /home/rj7/cts/7.0/r13/android-cts
./tools/cts-tradefed run cts --shards $shardCount --skip-preconditions $devices
```
