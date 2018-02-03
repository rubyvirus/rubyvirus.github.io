---
title: python 规范
tag: python
---
专业的人做专业的事儿，python code standard 需要学会，话不多说，[参考google分享内容](http://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_language_rules/)

特别记录一下python命名规范

| Type | column |Internal|
|--------|--------|--------|
|    Modules    |   lower_with_under  |_lower_with_under
|Packages|	lower_with_under	 |
|Classes|	CapWords|	_CapWords
|Exceptions	|CapWords	 |
|Functions|	lower_with_under()	|_lower_with_under()
|Global/Class Constants	|CAPS_WITH_UNDER|	_CAPS_WITH_UNDER
|Global/Class Variables	|lower_with_under	|_lower_with_under
|Instance Variables	|lower_with_under|	_lower_with_under (protected) or __lower_with_under (private)
|Method Names|lower_with_under()	|_lower_with_under() (protected) or __lower_with_under() (private)
|Function/Method| Parameters|	lower_with_under
|Local| Variables|	lower_with_under