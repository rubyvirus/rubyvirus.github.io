---
title: requests上传中文filename
date: 2018-11-9
tag: python
---

在flask项目中使用requests请求上传文件时，遭遇文件名为中文的烦恼，根源是urllib3对中文支持不优雅，详细可以fields.py文件中value = '%s*=%s' % (name, value)

来看看一下以下解决方案：

1. 偷梁换柱的方式

```
files = {'file': (
        'file_name', open(file=file_path, mode='rb'), 'application/octet-stream')}

requests.post(url, data={'file_name': '上传的中文名称'}, files=files)

```

2. 转码中文名称

```

from urllib.parse import quote
files = {'file': (
        quote('中文名称'), open(file=file_path, mode='rb'), 'application/octet-stream')}

```