---
title: 配置mysql远程连接
date: 2018-11-6
tag: mysql
---

本地开发经常会遇到连不上mysql数据库，一般都是配置文件和权限问题，来记录一波吧，出现的问题如下：

1. 修改mysql配置文件my.cnf(默认)

```
# Default Homebrew MySQL server config
[mysqld]
# Only allow connections from localhost
bind-address = 0.0.0.0 # 表示任何机器都可以访问
port = 3306  # 连接的端口
skip-grant-tables # 跳过表权限
```

2. 更新Mysql系统权限表

mysql> use mysq;
mysql> select * from user order by host desc,user desc;
mysql> update user set host = '%' where user = 'root';
mysql> flush privileges;