---
title: java8 multi filters
tag: essay
---
#### java8流式使用逻辑运算符实现多个条件筛选

如：
stream().filter(str->str>0).filter(str->str.endWiths(""))
整合为：
stream.filter(str->str>0||str.endWiths(""))