---
title: macaca-nosmoke 配置说明
tag: macaca
date: 2018-03-12
---
# macaca-nosmoke 配置说明
## 官方配置文件目录
https://github.com/macacajs/NoSmoke/tree/master/public
配置中文说明
```
---
# 1. Initialization option --初始化macaca server配置信息
desiredCapabilities:
  platformName: 'android' --指定运行设备类型android/ios/desktop
  isWaitActivity: true  --是否等待指定的activity加载完成
  activity: 'LoginActivity' --启动的activity
  permissionPatterns: '[\"继续安装\",\"下一步\",\"好\",\"允许\",\"确定\",\"我知道\"]'  --操作过程弹出框，自动操作指定的操作，如：指定了'下一步',运行就点击下一步
  app: 'https://npmcdn.com/android-app-bootstrap@latest/android_app_bootstrap/build/outputs/apk/android_app_bootstrap-debug.apk' --app路径

#  Web Configuration
#  platformName: 'Desktop'
#  browserName: 'Electron'
#  url: 'https://macacajs.github.io'

#  iOS Configuration
#  platformName: 'iOS'
#  deviceName: 'iPhone 6 Plus'
#  app: 'https://npmcdn.com/ios-app-bootstrap@latest/build/ios-app-bootstrap.zip'

#  Android Configuration
#  platformName: 'android'
#  isWaitActivity: true
#  activity: 'LoginActivity'
#  permissionPatterns: '[\"继续安装\",\"下一步\",\"好\",\"允许\",\"确定\",\"我知道\"]'
#  app: 'https://npmcdn.com/android-app-bootstrap@latest/android_app_bootstrap/build/outputs/apk/android_app_bootstrap-debug.apk'

# 2. Crawling option
crawlingConfig:
  platform: 'android'
  packages: 'com.github.android_app_bootstrap|com.xxx.your.optional.app'
  targetElements:
    loginAccount:
      searchValue : 'please input username'
      actionValue : '中文+Test+12345678'
    loginPassword:
      searchValue : 'please input password'
      actionValue : '111111'
    loginButton:
      searchValue : 'Login'
    alertConfirm:
      searchValue : 'yes'
  asserts:
    - type: 'regex'
      given: 'android\s+bootstrap'
      then: 'please\s+input\s+username'
    - type: 'regex'
      given: 'HOME'
      then: 'list'
  exclusivePattern: 'pushView|popView|cookie|userAgent:|Mozilla|cookie:|setTitle|Macaca Test Swipe API'
  clickTypes:
    - 'android.widget.ImageView'
    - 'android.widget.TextView'
    - 'android.widget.Button'
  editTypes:
    - 'android.widget.EditText'
  tabBarTypes:
    - 'android.widget.TabWidget'
...

# Web Configuration
#crawlingConfig:
#  platform: 'pc-web'
#  blacklist:
#    - 'github.com'
#  clickTypes:
#    - 'a'
#  editTypes:
#    - 'input'

#  # Android Configuration
# platform: 'android'
#  packages: 'com.github.android_app_bootstrap|com.xxx.your.optional.app'
#  targetElements:
#    loginAccount:
#      searchValue : 'please input username'
#      actionValue : '中文+Test+12345678'
#    loginPassword:
#      searchValue : 'please input password'
#      actionValue : '111111'
#    loginButton:
#      searchValue : 'Login'
#    alertConfirm:
#      searchValue : 'yes'
#  asserts:
#    - type: 'regex'
#      given: 'android\s+bootstrap'
#      then: 'please\s+input\s+username'
#    - type: 'regex'
#      given: 'HOME'
#      then: 'list'
#  exclusivePattern: 'pushView|popView|cookie|userAgent:|Mozilla|cookie:|setTitle|Macaca Test Swipe API'
#  clickTypes:
#    - 'android.widget.ImageView'
#    - 'android.widget.TextView'
#    - 'android.widget.Button'
#  editTypes:
#    - 'android.widget.EditText'
#  tabBarTypes:
#    - 'android.widget.TabWidget'

#  # iOS Configuration
#  platform: 'ios'
#  targetElements:
#      loginAccount:
#        searchValue : 'please input username'
#        actionValue : '中文+Test+12345678'
#      loginPassword:
#        searchValue : 'please input password'
#        actionValue : '111111'
#      loginButton:
#        searchValue : 'Login'
#      alertConfirm:
#        searchValue : 'yes'
#  exclusivePattern: 'pushView|popView|cookie|userAgent:|Mozilla|cookie:|setTitle|Macaca Test Swipe API'
#  clickTypes:
#    - 'StaticText'
#    - 'Button'
#  editTypes:
#    - 'TextField'
#    - 'SecureTextField'
#  horizontalScrollTypes:
#    - 'PageIndicator'
#  verticalScrollTypes:
#    - 'ScrollView'
#  tabBarTypes:
#    - 'TabBar'
#  exclusiveTypes:
#    - 'NavigationBar'

```
## crawlingConfig配置说明
```
crawlingConfig:
  platform: 'ios'   // platforms to run: Android, iOS, pc-web
  testingPeriod:    // maximun testing period for a crawling task, after which the task will terminate -- 一个抓取任务最大的测试阶段，之后的任务将终止
  testingDepth :    // maximum testing depth of the UI window tree, exeeding which 'Back' navigation will be triggered -- ui树最大遍历深度
  newCommandTimeout:// time interval takes to examine current window source after a crawling UI action has been performed -- 在执行了爬行UI操作之后，时间间隔用于检查当前窗口源。
  launchTimeout:    // time interval to wait after app has been launched. -- 应用程序启动后等待的时间间隔
  maxActionPerPage: // max UI actions filtered and performed perpage, this will provide greate memory optimization and prevent an Page for staying too long -- 一个页面最多抓取事件数
  targetElements:   // array of hight priority UI element to perform --高优先级执行的控件
  asserts:          // provide for regex assert test cases for windows
  exclusivePattern: // specify the pattern hence you can let those element which contain the regex pattern be excluded from exection -- 排除那些元素
  clickTypes:       // specify the types of UI element which can handle click events
  editTypes:        // specify the types of UI element which can handle edit events
  horizontalScrollTypes: // specify the types of UI element which can handle horizontal scroll
  tabBarTypes:      // specify the types of UI element may act as a control widget in master-detail pattern
  exclusiveTypes:   // specify the types of UI element in which all the sub-views will be exclueded from scanning and crawl
  ```

## 参考文档链接
[nosmoke](https://testerhome.com/topics/11482)