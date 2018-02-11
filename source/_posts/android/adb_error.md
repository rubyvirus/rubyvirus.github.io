---
title: 记录一次adb异常问题
tag: android
---
android测试过程中,经常用到[adb](https://developer.android.com/studio/command-line/adb.html?hl=zh-cn#howadbworks)命令，在同时使用模拟器和真机调用时候，发现如下异常提示：

```
* daemon not running. starting it now *   
ADB server didn't ACK   
* failed to start daemon *  
```
网上搜索一番都在说模拟器的adb设置为sdk路径，但早就已经设置了，后来发现debian系统默认安装了adb命令(whereis adb查询)
于是，使用sdk里面的adb就可以正常查看模拟器和真机devices info.

