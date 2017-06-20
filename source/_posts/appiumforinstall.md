---
title: ubuntu16.04 install appium for taobao.org
tag: appium
---
## 国（中国）内安装appium方式

### 1.　使用代\理执行命令（npm install -g appium）

### 2.　使用淘宝(npm.taobao.org)代\理安装
步骤：
1. 配置淘宝源, [参见](http://npm.taobao.org)
2. 执行命令：sudo cnpm install -g appium
3. 安装过程中，依赖包会正常下载，检查脚本时候可能会一直卡在 appium-uiautomator2-server-debug-androidTest.apk，不用急Ctrl+C暂停就是，运行/use/lib/node_modules/appium/lib下的main.js，也能正常运行appium

### 3. 使用源码安装
步骤:
1. 下载源码，可以git clone xxxx 也可以　wget https://github.com/appium/appium/archive/v1.6.5.tar.gz
2. 解压源码 tar -zxvf appium-xxx　并　从命名为appium
3. 如果没有代\理，还是用上一步的淘宝源，进入appium目录，运行: sudo cnpm install
4. 退到appium上级目录，运行: sudo cnpm link    目的：将appium进行软连接，参见npm link 命令详解
5. 安装过程中可能还是会卡在下载某个文件，继续Ctrl+C 暂停就是

end.
