---
title: *args,**kwargs 元组\字典入参
tag: python 进阶
---
### 什么是*args, **kwargs
1. args,kwargs 前面的* ** 是关键
2. *args,**kwargs为用户函数定义函数参数（值传递），将不定数量的参数传递给一个函数
#### *args
1. 用来发送一个非键值对的可变数量的参数列表给一个函数
2. 例子
```
def test_var_args(f_arg, *argv):
    print("first normal arg:", f_arg)
    for arg in argv:
        print("another arg through *argv:", arg)
test_var_args('yasoob', 'python', 'eggs', 'test')
```

#### **kwargs
1. 允许你将不定长度的键值对, 作为参数传递给一个函数
2. 例子
```
def greet_me(**kwargs):
    for key, value in kwargs.items():
        print("{0} == {1}".format(key, value))
greet_me(name="yasoob")
```
### 应用场景
```
def print_arg(*args, **kwargs):
    print(args)
    print(kwargs)
ag = (1, 2, 3)
kag = {'1': 1, '2': 2}
print_arg(1, ag, kag, first_kwargs=1)
-
(1, (1, 2, 3), {'1': 1, '2': 2})
{'first_kwargs': 1}
```
标准参数与args,kwargs执行顺序,print_arg(1, ag, kag, first_kwargs=1) 从左往右依次解析。