---
title: android command 小集
tag: android
---
adb(android debug brige), path: android_sdk/platform-tools/

| common commands |usage|
|--------|--------|
|  adb devices -l ||
| adb tcpip 5555 adb connect device_ip_address|wifi连接设备|
| adb -s serial_number command  ||
|adb [-d -e -s serial_number] shell shell_command||
|adb shell am start -a android.intent.action.VIEW||
|adb shell pm uninstall com.example.MyApp||