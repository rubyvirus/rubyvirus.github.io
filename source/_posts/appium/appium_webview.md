---
title: 记录一下通过android原生浏览器测试H5页面chromedriver版本保持一致性问题
date: 2018-05-21
tag: appium
---
测试原生H5页面时，appium是通过执行脚本机器上chrome驱动与手机上面的android system webview建立联系，最后进行页面通信

## chrome driver 下载地址
https://sites.google.com/a/chromium.org/chromedriver/downloads

## 手机上查看android system webview版本号
设置>应用程序管理>全部，查找到Android System WebView应用

将执行脚本机器chrome文件替换成手机版本号一致

脚本机器路径：/usr/local/lib/node_modules/appium/node_modules/appium-chromedriver/chromedriver/