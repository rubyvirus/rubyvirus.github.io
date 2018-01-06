---
title: 解追加字符（python）
tag: essay
---
==日常编码中常遇迭代字符拼接问题，特记录实现思路，供参考，如： [a,b,c,d,e,f] --> a + b + c + d + e + f==

## 思路
1. 设定一个计数器，到最后一个数，就不在拼接字符
2. 依次拼接完，再单独去掉最后拼接的字符

## 实现如下

```
思路一：

def generate_sql(*args):
	sql = 'sql='
    arg_count = len(args)
    while True:
        if arg_count == 1:
            sql += args[arg_count - 1]
            break
        else:
            sql += args[arg_count - 1] + ' AND '
        arg_count = arg_count - 1
    return sql

```

```
思路二：

def generate_sql(*args):
	sql = 'sql='
    for arg in args:
    	sql += arg + ' AND '
   	return ''.join(sql.rsplit(' AND ', 1)[:-1])

```