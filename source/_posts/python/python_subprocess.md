---
title: python 调用系统命令
tag: python
---
直接进入主题，使用subprocess模块调用，代码如下：
```
import subprocess
subprocess.Popen(["ping", "-c", "5", "www.baidu.com"], shell=True, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
```
1. 调用Popen方法
2. shell表示我们使用了shell=True这个参数。这个时候，我们使用一整个字符串，而不是一个表来运行子进程。Python将先运行一个shell，再用这个shell来解释这整个字符串。
shell命令中有一些是shell的内建命令，这些命令必须通过shell运行，$cd。shell=True允许我们运行这样一些命令
3. std表示子进程的标准输入，标准输出和标准错误