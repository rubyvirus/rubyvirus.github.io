---
title: pythonclitoolfire
tag: python
date: 2017-12-10
---
相信大家都是用过Python 众多的命令行支持工具，如：OptionParser, getopt, argparse, sys.argv 都大同小异，今天我们来看一个google 开源的CLi fire工具模块

# 首先需要安装该模块
pip install fire

### 举个栗子(类)
```
import fire

class Calculator(object):
  """A simple calculator class."""

  def double(self, number):
    return 2 * number

if __name__ == '__main__':
  fire.Fire(Calculator)
```
命令行调用
```
python calculator.py double 10  # 20
python calculator.py double --number=15  # 30
```
### 再举个栗子(函数)
```
import fire

def hello(name):
  return 'Hello {name}!'.format(name=name)

if __name__ == '__main__':
  fire.Fire(hello)

```
命令行调用
```
$ python example.py World
Hello World!

```

### 还是举栗子（传参为集合）
```
import fire

class Calculator(object):

  def add(self, *args):
    return args

if __name__ == '__main__':
  calculator = Calculator()
  fire.Fire(calculator)

```
命令行调用
```
$ python example.py add 10 20
[10, 20]
```

### 最后一个栗子（传参为字典）
```
import fire

class Calculator(object):

  def add(self, **kwargs):
    return kwargs

if __name__ == '__main__':
  calculator = Calculator()
  fire.Fire(calculator)

```
命令行调用
```
$ python example.py add --x=10 --y=20
{'x':1, 'y':2}
```

详细参见：https://github.com/google/python-fire/blob/master/docs/guide.md