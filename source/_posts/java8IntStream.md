---
title: java8 handle multi foreach
tag: essay
---
## java8 处理多个循环遍历
如：
for(){
	for(){
    }
}
整合为：
IntStream.range(min,max).forEach(....)