---
title: test app browser atibility from macaca
date: 2018-02-24
tag: macaca
---

wonderful macaca.

最近在构建macaca构建APP or browser(各种国产浏览器)兼容性,翻了翻相关文章,都说macaca可以启动,也来尝试了下.想到的有两种方案:
1. 通过配置Desired Capabilities [参考](https://macacajs.github.io/helpful-settings),[源码](https://github.com/macacajs/macaca-android/blob/master/lib/macaca-android.js),这种方式只能启动原生的,执行过程中可能会遭遇chromedriver不匹配的问题(很棘手),macaca默认支持4.4以上版本(还要看默认浏览的版本{一般在系统设置-->全部应用-->android.system.webview} [chromedriver与chrome各版本及下载地址](http://blog.csdn.net/cz9025/article/details/70160273),如果太老的android版本就不太友好
2. 把浏览器当做应用来处理,即不在指定browserName,注明:如果浏览器有阉割就不能用此方法

查看当前应用的package以及activity,打开APP,执行如下命令:
```
adb shell dumpsys window | grep mCurrentFocus
```
```
chrome 浏览器
'package': 'com.android.chrome',
'activity': 'com.android.chrome.browser.ChromeLauncherActivity'

qq 浏览器
'package': 'com.tencent.mtt',
'activity': 'com.tencent.mtt.MainActivity'

baidu 浏览器
'package': 'com.baidu.searchbox',
'activity': 'com.baidu.searchbox.MainActivity'

uc 浏览器
'package': 'com.UCMobile',
'activity': 'com.uc.browser.InnerUCMobile'
```