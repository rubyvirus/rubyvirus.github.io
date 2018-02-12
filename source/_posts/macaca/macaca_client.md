---
title: macaca permission 解决方案
tag: macaca
date: 2018-02-11
---
自动化测试安装APP时的提示信息，一直是一个棘手问题，还要macaca提供了目前来看比较完美的解决方案，通过UIAutomatorWD模块实施监控APP安装过程
看UIAutomatorWD源码如下
```
let args = `shell am instrument -w -r -e permissionPattern ${this.permissionPatterns} -e port ${this.proxyPort} -e class ${UIAUTOMATORWD.PACKAGE_NAME} ${UIAUTOMATORWD.TEST_PACKAGE}.test/${UIAUTOMATORWD.RUNNER_CLASS}`.split(' ');
```
我们只需要在desiredCapabilities申明中添加permissionPatterns参数就可以，下面是三种客户端示例：
1. nodejs
```
desiredCapabilities:
  platformName: 'android'
  isWaitActivity: true
  activity: 'LoginActivity'
  permissionPatterns: '[\"继续安装\",\"下一步\",\"好\",\"允许\",\"确定\",\"我知道\"]'
  app: 'https://npmcdn.com/android-app-bootstrap@latest/android_app_bootstrap/build/outputs/apk/android_app_bootstrap-debug.apk'
```
2. python
```
android = {'platformName': 'Android',
'app': 'https://npmcdn.com/android-app-bootstrap@latest/android_app_bootstrap/build/outputs/apk/android_app_bootstrap-debug.apk',
'udid': v, 'package': 'com.zhubajie.witkey','permissionPatterns': '[\\"安装\\",\\"允许\\"]'}
```
3. java
参考Python

使用vivio真机测试过程中，偶尔在允许授权时，最后一个允许没有点击成功，所有在正在开始执行用例前，暂定授权的最长时间

当然也有其它解决方案来处理APP安装过程，比如在macaca初始化之前开启线程来处理安装，其间用uiautomator轮询查找定位点击，但这种方案在macaca打开APP的时候就没法点击类似于授权这样的按钮了(当然也可以再添加一步软件权限设置)，一个手机同时只能连接一个uiautomator

比较奇葩的oppo安装应用时需要手动输入密码，可能就需要两者结合了。。。