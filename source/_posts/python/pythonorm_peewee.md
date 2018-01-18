---
title: python simple and small ORM Peewee(矮小的) 入门篇
tags: python
---
python ORM 多种多样，如：Django ORM , SQLAlchemy , SqlObject , 今天我们来介绍一款小巧精悍框架，支持的数据库: sqlite,mysql,postgresql , 支持的Python版本: 2.6+ and 3.2+
[官方文档链接](http://docs.peewee-orm.com/en/latest/index.html)

### 1. 安装peewee
- pip
` pip install peewee`

- source install
```
git clone https://github.com/coleifer/peewee.git
cd peewee
python setup.py install
```

### 2. hello world


#### hello_world.py
```

from peewee import *

DB = SqliteDatabase('helloworld.db')

class BaseModle(Modle):
	"""
	基础模型

	"""
	class Meta():
		database = DB

class HelloWorld(BaseModle):
	"""
	hello world模型
	peewee模型及数据库表，表及模型
	"""
	hwid = PrimaryKeyField(unique=True)
	hwcontent = CharField()

	@classmethod
	def save_info(cls, hw_content):
        """
			保存数据
        ""
		HelloWorld(hwcontent=hw_content).save()

	@classmethod
	def select_info(cls, hw_content=None):
    	"""
        	查询数据
        """
		if hw_content:
			cls.select().where(HelloWorld.hwcontent == hw_content)
		else:
			cls.select()
```

#### test_hello_world.py
```
from hello_world import DB, HelloWorld


def db_test():
    DB.create_tables([HelloWorld], safe=True)
    HelloWorld.save_info('hello world my peewee.')

if __name__ == '__main__':
    db_test()
    for hw in HelloWorld.select_info():
        print(hw.hwcontent)
```
done
重点理解peewee vs database 关系表

| Thing | Corresponds to… |
|--------|--------|
|   Model     |    Database table    |
|Field instance | Column on a table|
|Model instance | Row in a database table|
