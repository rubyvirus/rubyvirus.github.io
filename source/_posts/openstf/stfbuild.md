---
title: open-stf 源码编译运行
tag: open-stf
---

# 下载open-stf源码
https://github.com/openstf/stf
# 安装open-stf到本地
- sudo npm install （可选）
- cnpm install (推荐使用淘宝镜像) 实操发现，此步骤会自动编译，无需执行下面命令
# 安装package.json
cnpm install package.json
# 编译(angular 开发，用的是gulp构建工具)
gulp clean && gulp webpack:build

安装过程中可能会提示某些module 没有安装，使用cnpm install module

# 运行
stf local

问题：
- ERROR in Node Sass does not yet support your current environment: Linux 64-bit with Unsupported runtime (59)
查看npm package版本 cnpm ls node-sass 应该是大于3.8的，然后重新编译npm rebuild node-sass就不会抛出哦